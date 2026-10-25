# Working guidelines for the romm repo

Self-hosted RomM game library. README.md covers setup and day-to-day usage; do not
repeat it here. These rules apply to anyone (human or agent) committing to this repo.

## 1. Never commit large files

- Hard cap: no single tracked file over 5 MB. Keep the whole repo under ~100 MB.
- Only small cartridge ROMs are tracked: `library/roms/nes/` and `library/roms/snes/`.
- Disc/large-platform folders stay local-only and are gitignored on purpose:
  `library/roms/n64/`, `library/roms/ps2/`, `library/roms/psx/`, plus `*.bin` and `*.7z`.
- Adding a new platform: if typical ROMs exceed 5 MB, add the folder to `.gitignore`
  before copying files in. Do not use Git LFS.
- Before every commit, check what is staged:
  `git diff --cached --stat` and `git ls-tree -r -l HEAD | sort -k4 -n -r | head`.
- If a large file was committed by mistake, do not push. Undo the commit
  (`git reset --soft HEAD~1`), unstage the file, add it to `.gitignore`, then recommit.

## 2. Adding ROMs

- Extract archives first; track the bare ROM (`.nes`, `.sfc`), not the `.zip`.
  Discard bundled readme/scene text files.
- Name files No-Intro style: `Title (Region) (Extras).ext`, with spaces,
  e.g. `Contra (USA).nes`, `Salamander (Japan).nes`. Do not rename files that are
  already tracked; RomM keys saves to the filename.
- Put the file in `library/roms/<platform>/`. Nothing lives at the repo root.
- Verify it works: in RomM run **Scan → Scan**, confirm the game appears under the
  right platform, open it and press **Play**. Only then commit, one commit per batch,
  e.g. `Add Salamander (Japan) NES ROM to library`.

## 3. Never lose ROMs or saves

- `git clean`, `git checkout -- .`, `git reset --hard`, and branch deletion must not be run
  without first checking that no untracked ROMs, saves, or states would be removed.
  Untracked data under `library/roms/` and `assets/users/` is the only copy.
- Never delete files under `library/` or `assets/` as part of a git fix.
- Do not touch: the MariaDB and cache Docker volumes, and any `assets/users/<id>/`
  folder that is not your own user.
- Save data (`assets/users/`) is tracked. Commit saves after a play session you want
  to keep, in their own commit, e.g. `Add Super Mario World save data from play session`.

## 4. Secrets and private notes stay local

- `.env` holds DB credentials, the auth secret key, and IGDB keys. It is never committed.
  Update `.env.example` whenever a new variable is added.
- `documents/access_notes/` holds work infrastructure notes and is gitignored.
  Nothing referencing work systems or credentials goes in tracked files.

## 5. Branching and pushing

- `main` is the integration branch. Do feature work on a branch named `<topic>_dl`
  (current: `adding_feature_dl`), then fast-forward `main` to it.
- Keep `main` and feature branches sharing history. Do not re-initialize the repo.
- Force pushes are only allowed to your own feature branch, never to `main`,
  unless explicitly agreed in the moment.
- Commit messages: imperative one-line summary under 72 chars.

## 6. Config changes

- RomM settings and EmulatorJS gamepad bindings live in `config/config.yml`.
  After editing, run `docker compose restart romm` and test in the web player.
- Compose stack changes go in `docker-compose.yml`. Keep bind-mounts relative to the repo.

## 7. Reports

- Write a session report only when asked. Put it in `documents/reports/<YYYY-MM-DD>/`
  and follow `documents/report_guide/report_guide.md`.

## 8. Python helper project

- Managed with `uv`. Run scripts with `uv run main.py`; add deps with `uv add <pkg>`.
- Commit `uv.lock` alongside `pyproject.toml` changes.
