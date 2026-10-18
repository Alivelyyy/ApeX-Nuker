# ApeX-Nuker — Project Documentation

> Complete 5-page engineering documentation. Part of the Alivelyyy coding-history dossier (generated 2026-10-18).

## ApeX-Nuker

> **ApeX Nuker** — "The most Advanced and customizable Nuke Bot Ever Existed." A py-cord 2.6.0+ Discord toolkit with god-mode, mass-moderation, trolling, and destructive command families, multilingual UI (12 language packs), SOCKS proxy support, and structured logging. Published publicly under the ApeX Development banner, v.1.01.

| Fact | Value |
|---|---|
| GitHub URL | https://github.com/Alivelyyy/ApeX-Nuker |
| Visibility | Public |
| Created / pushed | 2026-05-24 (v.1.01) |
| Primary language | Python (py-cord 2.6.0+) |
| Total files | 22 (incl. `lang/` 12 locales, `COMMANDS.md`, install/run bat scripts) |
| Dependencies | `py-cord>=2.6.0`, `colorama>=0.4.6`, `aiohttp_socks>=0.7.1` |
| Category | Discord moderation/destructive tooling (educational) |

> ⚠️ **Documentation note (important for resume framing):** this is a *capability toolkit* whose destructive commands are dangerous by design. The appropriate professional framing — used consistently below — treats the repo as a study in Discord API rate-limit handling, permission models, batch operations, localization, and logging architecture. It is **not** suggested as a client deliverable.

### 📄 Page 1 — Overview & Purpose

ApeX-Nuker is one of three "ApeX-branded" CLI/bot tools shipped in the May 2026 tooling wave (`ApeX-Ai-Cli`, `ApeX-Emoji-Cloner`, `ApeX-Nuker`), all branded "developed by Alive, founder of ApeX Development." It implements, in a single Python codebase, the complete feature surface of the classic "nuke bot" genre — with an unusual degree of engineering discipline for that genre: a 12-language localization system, colorized console output, per-day rotating log files, proxy/anti-rate-limit options, whitelisting, and a thorough `COMMANDS.md` manual.

From an engineering-study perspective it demonstrates: Discord permission-model fluency (god-mode/admin grants, role stripping, voice moves), high-throughput batch operations under rate limits (mass ban/kick/mute, channel spam with configurable counts), server state reconstruction (backup export to JSON), and destructive-action ergonomics (confirmation-tokened commands like `.!brainfuck`).

Its README lists the full capability matrix; `COMMANDS.md` is a real product-style manual organized by command family (Core, Moderation, Mass Moderation, Trolling, Destructive). For the dossier's purpose: this repo shows what happens when this developer touches Discord's API surface with production habits — even a toy genre gets i18n, config files, logging, and manuals.

### 📄 Page 2 — Technology Stack & Architecture

**Runtime** (`requirements.txt`): Python 3.10+, `py-cord>=2.6.0` (the community pycord fork of discord.py — chosen for slash-command/modern API support), `colorama>=0.4.6` for ANSI console styling, `aiohttp_socks>=0.7.1` for SOCKS proxy transport.

**Configuration** (`config.json`): `token` (bot token placeholder `BOT_TOKEN`), `prefix` (`.!` default — customizable), `owner_id`, `whitelist` (array of privileged user IDs), `language` (`en`), `proxies` (bool). The whitelist+owner design gates destructive families away from non-authorized users.

**Repository layout**:

| Path | Purpose |
|---|---|
| `main.py` | Full bot: logging bootstrap, command registrations, event handlers |
| `COMMANDS.md` | The exhaustive command manual (families + per-command syntax) |
| `lang/*.json` | 12 locale packs: `en, sv, pt, fr, it, pl, de, nl, ru, es, el, uk` |
| `config.json` | Token/prefix/owner/whitelist/language/proxy config |
| `socks4.txt`, `socks5.txt` | Proxy pools for request pacing/anonymity |
| `install.bat`, `run.bat` | Windows one-click setup and launch |
| `logs/` (runtime) | `logs/bot_YYYY-MM-DD.log` — daily rotation, formatter `%asctime | %levelname | %message` |
| `LICENSE` | License file |

**Logging architecture** (from `main.py`): a dedicated `setup_logging()` creates the `logs/` directory, a date-stamped log filename, a named logger `ApeXNuker` at INFO level, a UTF-8 file handler with a human-readable formatter, and console output — clean enough to lift into any production bot.

### 📄 Page 3 — Features & Functionality

**Core / God Mode** — `.!god` grants the invoker administrator; `.!god-all` grants everyone admin.

**Nuclear options** — `.!death` (ban all + delete channels/voice/categories/roles), `.!brainfuck <name> <message>` (delete all channels, then continuously create + spam).

**Moderation** — `.!ban`, `.!unban <user_id>`, `.!kick`, `.!mute <user> [duration] [reason]` (formats like `10m, 1h, 30s, 1d`), `.!unmute`, `.!purge <amount>` (up to 1000 messages).

**Mass moderation** — `.!ban-all`, `.!kick-all`, `.!mute-all [duration]`, `.!unban-all` — all protecting bots and the invoker.

**Trolling (mostly reversible)** — `.!nick-all <nickname>`, `.!shuffle-channels`, `.!voice-scatter`, `.!move-all <#vc>`, `.!mention-spam <@user/@role> <count>` (cap 100), `.!spam <count> <message>` (0 = infinite, own-channel), `.!rename-server`, `.!server-icon`, `.!server-banner` (boost-2 requirement noted), `.!server-desc`, `.!role-spam <name> <count>` (cap 250), `.!slowmode-all [seconds]` (default 21600 = 6h, capped).

**Destructive** — `.!nuke` (channel delete+recreate), plus per README: delete-all-channels, mass-DM, mass nickname, webhook nuking, emoji/sticker/invite/thread nuking, bot-kick-all, server-backup-to-JSON, unban-all, voice move/scatter, slowmode control, channel reorder — 25+ checked features in the README matrix.

**Localization** — the entire command surface is translatable via `lang/` JSON packs; switching `language` in config re-skins responses across 12 locales — a rare feature in this genre and evidence the developer thinks in products, not scripts.

**Windows ergonomics** — `install.bat` + `run.bat` for dependency install and launch on Windows; bat files coexisting with a Python codebase show distribution-mindedness.

### 📄 Page 4 — Code Internals & Systems Design

`main.py` opens with imports for `discord`, `commands`, `asyncio`, `io`, `random`, `timedelta/datetime`, `colorama`, `logging` — then immediately bootstraps logging (see Page 2). Commands are registered as py-cord `commands`/slash handlers; the `. !` prefix default is configurable in `config.json`. The architecture is a single-file monolith (typical for this genre) but with segmented concerns: logging setup, config load, whitelist checks, and per-family command blocks. Rate-limit survivability comes from **proxy pools** (`socks4.txt`/`socks5.txt` consumed through `aiohttp_socks`) plus retry-friendly batch loops; the `proxies` config flag toggles the transport.

Destructive ergonomics: muscle-memory-unfriendly commands (`death`, `brainfuck`) act as a safety barrier; moderate families (trolling) are documented as "mostly reversible." Server-backup export writes server structure to JSON — an escape hatch mirroring rollback thinking. The 12 language packs are flat key-value JSON, loaded by the `language` config — trivially extendable, and the reason the tool's README shows the (unusually professional for genre) localization checklist.

Operationally the bot is launched manually (`run.bat` / `python main.py`) — no hosting config, no Docker, no CI. It is a self-hosted utility by nature. For the resume narrative, highlight what's transferable: impact-checked batch operations, permission-scope handling, transport rotation under rate limits, i18n architecture, and daily-rotated structured logs.

### 📄 Page 5 — Development Timeline, Skills & Resume Highlights

**Timeline**: single publication day 2026-05-24 with tag-style version "v.1.01," inside the May tooling cluster (Groq CLI May 20 → Nuker May 24 → Emoji Cloner May 25). README badges claim "maintained: yes," "contributors: 1," PRs welcome.

**Skills demonstrated**: Python async programming (asyncio, py-cord); Discord API permission and rate-limit mechanics; SOCKS proxy transport rotation; JSON-driven i18n across 12 locales; structured logging with daily rotation; config/whitelist security model; product-grade documentation (COMMANDS.md manual, README feature matrix); Windows batch distribution.

**Resume bullets**:
- Built a Python (py-cord) Discord toolkit implementing 25+ server-management operations with god-mode, mass-moderation, and batch channel/role/voice control families.
- Engineered anti-rate-limit resilience via SOCKS4/SOCKS5 proxy pools and retry-friendly async batch loops (`aiohttp_socks`).
- Implemented 12-locale JSON internationalization and owner-whitelist config gating, plus daily-rotated structured logging (`logger 'ApeXNuker'`, formatter with timestamps/levels).
- Shipped complete product documentation (`COMMANDS.md`) and Windows install/run scripts (`install.bat`, `run.bat`).

**Honest maturity note / future work**: single-file monolith without tests; genre-purpose destructive capabilities mean the repo should be framed as an API-mastery exercise; a cogs-based refactor and dry-run mode would modernize it. Do not present as a client software artifact.
