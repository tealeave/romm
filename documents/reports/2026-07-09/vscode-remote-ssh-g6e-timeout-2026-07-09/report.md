# VS Code Remote-SSH → EC2 g6e Connection Failure — Troubleshooting Report

## 0) Report Header

* **Date:** 2026-07-09 (Pacific Time)
* **Context:** VS Code Remote-SSH could not connect to EC2 g6e instance (`ec2-innovation-g6e`, `i-09383976532e5c59c`) while on in-flight wifi
* **Constraint:** EC2 instance runs an EDR agent — diagnosis had to avoid running anything on the remote that could trigger InfoSec alerts
* **Outcome:** Root-caused and fixed entirely from the local machine. Zero commands executed on the EC2 instance.

---

## 1) Symptom

VS Code Remote-SSH failed with:

```
Resolver error: Error: Connecting with SSH timed out
```

Meanwhile, plain `ssh ec2-innovation-g6e` from the terminal worked fine.

## 2) Environment

| Component | Detail |
|-----------|--------|
| Connection path | SSH over AWS SSM Session Manager (`ProxyCommand ~/.local/bin/ssm-proxy.sh %h %p`) — no direct SSH port exposure |
| Network | In-flight wifi (high latency, satellite) |
| Local | Ubuntu, OpenSSH 9.6p1, VS Code Remote-SSH extension v0.124.0 |
| Remote | EC2 g6e (GPU), Ubuntu, user `ubuntu`, key `~/.ssh/id_aws_ds` |

## 3) Diagnosis

### Step 1 — Verify raw SSH works

`ssh -vvv ec2-innovation-g6e exit` succeeded: SSM tunnel established, publickey auth accepted, clean exit. So AWS creds, the SSM proxy, host keys, and the instance itself were all healthy. The problem was specific to VS Code's layer.

### Step 2 — Read the local Remote-SSH log (no remote footprint)

VS Code writes its connection log locally under:

```
~/.config/Code/logs/<session>/window*/exthost/output_logging_*/1-Remote - SSH.log
```

The log showed the smoking gun — a race lost by ~100 ms:

```
17:17:15.0  ssh spawned                          (timeout budget: 17s)
17:17:26.3  server host key received             (11s for SSM tunnel + handshake)
17:17:31.8  Authenticated ... using "publickey"  ← auth SUCCEEDED
17:17:31.9  Terminating local server             ← VS Code 17s timer fired anyway
```

## 4) Root Cause

The in-flight wifi's latency stretched the SSM handshake to ~17 seconds. VS Code Remote-SSH uses `remote.SSH.connectTimeout` (default **15s**, +2s grace = 17s) and killed the connection immediately after authentication completed. Plain terminal `ssh` worked because it has no such timer.

Not the cause (ruled out): AWS credentials, SSM proxy script, host key mismatch, vscode-server install failure, remote disk space, EDR interference.

## 5) Fixes Applied (all local)

### VS Code — `~/.config/Code/User/settings.json`

```json
"remote.SSH.connectTimeout": 60
```

Gives the slow SSM handshake room to complete.

### SSH — `~/.ssh/config`, `Host ec2-innovation-g6e` block

```
ServerAliveInterval 15
ServerAliveCountMax 8
```

Keepalives every 15s, tolerating 8 misses (~2 min) — keeps the SSM tunnel alive through brief wifi dropouts mid-session instead of killing the connection.

## 6) EDR / InfoSec Considerations

* Initial plan included running `df -h`, `ls ~/.vscode-server`, and a `curl` reachability probe on the remote — **abandoned** to avoid any chance of EDR flagging (outbound `curl` from a server can look like beaconing).
* Replaced with the local-log approach (Step 2 above), which reads only files on the laptop.
* Nothing was ever executed on the EC2 instance during this investigation.

## 7) Lessons / Playbook for Next Time

1. **First test:** `ssh -vvv <host> exit` — if it succeeds, the problem is VS Code's layer, not the network/AWS.
2. **Second test:** read the local Remote-SSH log (path above) — it timestamps every phase and names the exact failure. No remote access needed.
3. **High-latency links (flight wifi, hotel wifi, tethering) + SSM proxying** routinely exceed VS Code's 15s default timeout. The 60s setting is now persistent, so this class of failure shouldn't recur.
4. On EDR-monitored hosts, prefer diagnosis paths that stay on the local machine; plain SSH logins and normal admin reads are fine, but avoid ad-hoc outbound probes (`curl` to external endpoints).
