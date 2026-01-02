# RoMM Setup & Configuration Report

## 0) Report Header

* **Date:** 2026-01-02 (Pacific Time)
* **Project:** RoMM (ROM Manager) Docker Setup & Configuration
* **Starting Plan of the Day:**
  - Set up RoMM with Docker for ROM management and in-browser gameplay
  - Configure IGDB metadata provider for game artwork
  - Configure gamepad support for EmulatorJS
  - Add ROMs to the library

---

## 1) What Was Done

### Infrastructure Setup

| Task | Status | Details |
|------|--------|---------|
| Created `.env` file | Done | DB credentials, timezone (America/Los_Angeles), auth secret key |
| Configured IGDB | Done | Client ID & Secret from Twitch Developer Portal |
| Docker containers | Done | RoMM (rommapp/romm:latest) + MariaDB 11.4 |
| Debug logging | Done | Added `LOGLEVEL=DEBUG` for troubleshooting |

### ROM Library Setup

| Platform | Location | Games |
|----------|----------|-------|
| NES | `library/roms/nes/` | 1 game (Contra) |
| SNES | `library/roms/snes/` | 17 games |

### Controller Configuration

Configured Xbox 360-compatible controller (3rd party) for EmulatorJS in `config/config.yml`:

| Physical Button | Gamepad Index | NES Mapping | SNES Mapping |
|-----------------|---------------|-------------|--------------|
| A (green) | B0 | A | A |
| B (red) | B1 | B | B |
| X (blue) | B2 | - | Y |
| Y (yellow) | B3 | - | X |
| LB | B4 | - | L |
| RB | B5 | - | R |
| LT | B6 | - | L2 |
| RT | B7 | - | R2 |
| Back | B8 | Select | Select |
| Start | B9 | Start | Start |
| D-pad Up | B12 | Up | Up |
| D-pad Down | B13 | Down | Down |
| D-pad Left | B14 | Left | Left |
| D-pad Right | B15 | Right | Right |

**Key Learning:** EmulatorJS requires numeric button indices specific to each controller model, not generic names like `BUTTON_1` or `DPAD_UP`.

---

## 2) Artifacts Produced

| File | Purpose |
|------|---------|
| `.env` | Environment configuration with DB creds, IGDB API keys |
| `config/config.yml` | RoMM config with EmulatorJS controller mappings |
| `docker-compose.yml` | Modified to add `LOGLEVEL=DEBUG` |

---

## 3) Current ROM Library

### NES (1 game)
- Contra (USA).nes

### SNES (17 games)
- Final Fight 3 (USA) (Virtual Console).sfc
- Goof Troop (USA).sfc
- Great Circus Mystery Starring Mickey & Minnie, The (USA).sfc
- Kirby Super Star (USA).sfc
- Kirby's Dream Land 3 (USA).sfc
- Legend of the Mystical Ninja, The (USA).sfc
- Mega Man X (USA) (Capcom Town).sfc
- Mega Man X2 (USA).sfc
- Mega Man X3 (USA).sfc
- Pocky & Rocky (USA).sfc
- Pocky & Rocky 2 (USA).sfc
- Super Bomberman (USA).sfc
- Super Mario Kart (USA).sfc
- Teenage Mutant Ninja Turtles IV - Turtles in Time (USA).sfc
- Wild Guns (USA).sfc
- Zombies Ate My Neighbors (USA).sfc

---

## 4) Open Issues

| Issue | Status | Notes |
|-------|--------|-------|
| IGDB artwork not fetching | Investigating | Need to run "Unmatched Games" scan with IGDB selected |
| Controller config | Resolved | Required numeric button indices instead of named values |

---

## 5) Next Steps

### Immediate: Download Additional SNES ROMs from Vimm's Lair

**Source:** https://vimm.net/vault/ (SNES section)

**Recommended version:** USA, non-Virtual Console, Version 1.0

#### Platformers
| Game | Search Term | Priority |
|------|-------------|----------|
| Super Mario World | `super mario world` | High |
| Donkey Kong Country | `donkey kong country` | High |
| Donkey Kong Country 2 | `donkey kong country 2` | High |
| Donkey Kong Country 3 | `donkey kong country 3` | High |
| Super Metroid | `super metroid` | High |
| Super Castlevania IV | `super castlevania iv` | High |
| Contra III: The Alien Wars | `contra iii` | High |

#### Beat 'em Ups
| Game | Search Term | Priority |
|------|-------------|----------|
| Final Fight 2 | `final fight 2` | High |
| Knights of the Round | `knights of the round` | Medium |
| King of Dragons | `king of dragons` | Medium |
| The Peace Keepers | `peace keepers` | Medium |
| Sunset Riders | `sunset riders` | Medium |
| Run Saber | `run saber` | Medium |

#### Multiplayer
| Game | Search Term | Priority |
|------|-------------|----------|
| Super Bomberman 2 | `super bomberman 2` | Medium |
| Super Bomberman 3 | `super bomberman 3` | Medium |
| Super Bomberman 4 | `super bomberman 4` | Low |
| Super Bomberman 5 | `super bomberman 5` | Low |
| Street Fighter II Turbo | `street fighter ii turbo` | High |

### Short-term: After Downloads
1. Move all `.zip` files from `~/Downloads/` to `library/roms/snes/`
2. Unzip all ROMs
3. Run "Unmatched Games" scan in RoMM with IGDB selected
4. Verify artwork is fetched for all games

### Nice-to-have
- Add ScreenScraper as backup metadata provider
- Configure 2-player controller mappings for co-op games
- Explore RoMM's save state and screenshot features

---

## 6) Reproducibility Notes

### Starting RoMM
```bash
cd /home/david/projects/romm
docker compose up -d
```

### Stopping RoMM
```bash
docker compose down
```

### Access
- **URL:** http://localhost:8080
- **Port:** 8080

### Adding New ROMs
```bash
# Move zips to library
mv ~/Downloads/*.zip library/roms/snes/

# Unzip and clean up
cd library/roms/snes
for f in *.zip; do unzip -o "$f" && rm "$f"; done

# Then run scan in RoMM UI
```

### Key Configuration Files
| File | Purpose |
|------|---------|
| `.env` | Environment variables (DB, IGDB credentials) |
| `config/config.yml` | RoMM & EmulatorJS settings |
| `docker-compose.yml` | Container orchestration |

### Docker Volumes (persistent data)
- `romm_mysql_data` - MariaDB database
- `romm_romm_resources` - RoMM resources/artwork
- `romm_romm_redis_data` - Redis cache

---

## 7) References

- **RoMM Documentation:** https://docs.romm.app/
- **EmulatorJS Control Mapping:** https://emulatorjs.org/docs4devs/control-mapping/
- **IGDB API (Twitch):** https://dev.twitch.tv/console/apps
- **Vimm's Lair:** https://vimm.net/vault/
- **Gamepad Tester:** https://gamepad-tester.com
