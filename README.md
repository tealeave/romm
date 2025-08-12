# What we set up

* WSL2 + Docker Desktop (WSL2 engine).
* `~/romm` project with `docker-compose.yml` for **RomM** + **MariaDB**; bind-mounts:

  * `./library` (your ROMs), `./assets`, `./config`; volumes for DB/cache.
* `.env` with `DB_*`, `ROMM_AUTH_SECRET_KEY`, and **IGDB** keys (Client ID/Secret from Twitch Dev Console).
* Brought stack up: `docker compose up -d`.
* Created RomM admin, confirmed IGDB ✅.
* Placed an SNES ROM at `~/romm/library/roms/snes/…`, scanned, and played via the web player.

# Quick start (next time)

```bash
cd ~/romm
docker compose up -d                    # start RomM + DB
open http://localhost:8080             # in your browser
```

# Add games (two ways)

**A) Copy on disk (best for many files)**

```bash
# NES/SNES folders already exist; add others as needed
cp /path/to/game.sfc ~/romm/library/roms/snes/
cp /path/to/game.nes ~/romm/library/roms/nes/
```

Then in RomM: **Scan → Scan** (or **Full rescan**).

**B) Web upload (quick test)**

* Game page → **Upload** (accepts zipped ROMs too).
* If the game doesn’t appear, check **Platforms → Manage Library** to ensure `/romm/library/roms` is the path and `snes/nes` map to the right platforms.

# Play & save

* Open a game → **Play** → **Save and Quit** when done (SRAM/state is stored in your mapped/volume paths).

# Stop / persistence

```bash
cd ~/romm
docker compose stop            # stop containers; all data persists
# later
docker compose start           # or: docker compose up -d
```

* `docker compose down` removes containers but **keeps** your bind-mounted folders and volumes.
* **Do not** use `docker compose down -v` unless you want to wipe the DB/resources.

# Update RomM later

```bash
cd ~/romm
docker compose pull
docker compose up -d --force-recreate romm
```

# Useful one-liners

```bash
docker ps -a                                  # see containers
docker compose logs -f romm                   # tail app logs
docker exec -it romm bash -lc 'ls -al /romm/library/roms/snes'
```

# Quick “Play failed / Network Error” tips

* Try an incognito window / different browser; disable ad-blockers for `localhost:8080`.
* Watch logs while pressing Play: `docker compose logs -f romm`.
  Send me any errors and I’ll pinpoint the fix.
