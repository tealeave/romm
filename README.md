# RomM Home Game Library

Self-hosted [RomM](https://github.com/rommapp/romm) instance for managing and playing a personal ROM library in the browser, running on WSL2 + Docker Desktop (WSL2 engine) with MariaDB.

## Repo layout

```
docker-compose.yml   # RomM + MariaDB stack
.env                 # secrets (DB creds, auth key, IGDB keys) — not tracked
.env.example         # template for .env
config/config.yml    # RomM config: platform mapping, EmulatorJS gamepad bindings
library/roms/<platform>/   # ROM files (nes, snes tracked; n64/ps2/psx ignored — too large)
assets/users/        # per-user saves, states, screenshots from the web player
documents/           # setup reports and notes
main.py, pyproject.toml    # uv-managed Python helper project (py7zr for archive extraction)
```

## Quick start

```bash
cd ~/romm
cp .env.example .env   # first time only: fill in DB_*, ROMM_AUTH_SECRET_KEY, IGDB keys
docker compose up -d   # start RomM + DB
```

Then open <http://localhost:8080>.

Setup notes:

- `ROMM_AUTH_SECRET_KEY`: paste output of `openssl rand -hex 32`
- IGDB Client ID/Secret come from the Twitch Dev Console (metadata + artwork ✅)
- Bind-mounts: `./library` (ROMs), `./assets` (saves/states), `./config`; named volumes for DB/cache

## Add games

**A) Copy on disk (best for many files)**

```bash
# NES/SNES folders already exist; add others as needed
cp /path/to/game.sfc ~/romm/library/roms/snes/
cp /path/to/game.nes ~/romm/library/roms/nes/
```

Then in RomM: **Scan → Scan** (or **Full rescan**).

**B) Web upload (quick test)**

- Game page → **Upload** (accepts zipped ROMs too).
- If the game doesn't appear, check **Platforms → Manage Library** to ensure `/romm/library/roms` is the path and `snes`/`nes` map to the right platforms.

## Play & save

- Open a game → **Play** → **Save and Quit** when done (SRAM/state is stored under `assets/users/`).
- Gamepad/keyboard bindings for the NES (`fceumm`) and SNES (`snes9x`) cores live in `config/config.yml`.

## Stop / persistence

```bash
cd ~/romm
docker compose stop            # stop containers; all data persists
# later
docker compose start           # or: docker compose up -d
```

- `docker compose down` removes containers but **keeps** your bind-mounted folders and volumes.

## What's tracked in git

- Small cartridge ROMs (NES/SNES, ≤4 MB each) are committed; large-file platforms (`library/roms/n64/`, `library/roms/ps2/`, `library/roms/psx/`) are gitignored because of their size. Rule of thumb: don't commit ROMs bigger than a few MB.
- `.env` is gitignored; only `.env.example` is committed.
