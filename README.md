# nullroom-
Anonymous rooms. No account, no history, no tracking

<div align="center">

# nullroom

**Anonymous rooms. No account, no history, no tracking.**

Walk into a room and talk. Nothing is stored, nothing follows you home.

[![Python 3.9+](https://img.shields.io/badge/python-3.9%2B-blue)](https://www.python.org/)
[![Dependencies](https://img.shields.io/badge/dependencies-zero-brightgreen)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Tests](https://img.shields.io/badge/tests-68%20passing-brightgreen)](#testing)

[Features](#features) · [Quick start](#quick-start) · [Kali client](#kali-terminal-client) · [Invites](#creating-a-room-and-inviting-people) · [Deploy](#deploy) · [Privacy](#privacy) · [Testing](#testing)

</div>

---
From a fresh Kali terminal, top to bottom:
1. Get the project onto the machine
git clone https://github.com/<you>/nullroom.git
cd nullroom

No git? Download the ZIP from the GitHub page and unzip it instead.
2. Check Python (every Kali has it, but confirm once)
python3 --version

You need 3.9 or newer. If it's missing for some reason:
sudo apt update && sudo apt install -y python3

Nothing else is required. No pip install, no packages, no virtualenv — there are deliberately zero dependencies.
3. Start the server (if you're hosting the chat on this same box)
python3 server.py

It listens on port 8080. Leave that terminal running — killing it erases every room and message, by design.
4. Install the terminal client (new terminal window)
cd nullroom/kali
python3 tests_tui.py     # sanity check — should end with "OK"
bash install.sh          # installs the `nullroom` command

If ~/.local/bin isn't on your PATH, the installer tells you. Add this to ~/.zshrc (Kali's default shell) and reopen the terminal:
export PATH="$HOME/.local/bin:$PATH"

5. Connect
# same machine
nullroom --server 127.0.0.1:8080 --room kali

# someone else's server
nullroom --server chat.example.com --room kali

# from an invite someone sent you
nullroom --invite chat.example.com/my-room?id=6uvc3f

First-run checklist
- Ctrl-R shows live rooms, Ctrl-N creates one, Ctrl-I copies the invite, /help lists everything.
- If the layout looks broken (wrong terminal size, missing colors, misaligned boxes), run resize or maximize the window and press Ctrl-L to redraw. If it's still wrong, send me a screenshot — that's the one combination I can't test from here.
- The client keeps one small config file (~/.config/nullroom/config.json) with your server and theme. Transcripts are written only when you press Ctrl-S.

If you're joining a friend's server you only need steps 1, 2 (check), and 5. No server, no install script — python3 kali/client.py --server <their-address> works straight from the folder.

What changed in install.sh: after copying, client.py and tui.py are set to 444 (read-only for everyone) and owned by you. Only the nullroom launcher is executable (755). The client never needs to write to its own code, so there's no reason it should be writable. And at the end, the installer prints ls -l of all three files, so you see the proof instead of taking my word for it.
Expected output after install:
-r--r--r-- you you ... client.py
-r--r--r-- you you ... tui.py
-rwxr-xr-x you you ... nullroom

No sudo is used or needed at any point — it's all inside your home directory (~/.local/share/nullroom, ~/.local/bin).
The reasoning, briefly: on a box like Kali, which runs a lot of untrusted tools, the threat isn't someone reading your chat client — it's something rewriting it. Read-only files mean malware running as you can't silently patch the client to leak messages; it would have to chown/chmod first, which is noisier and leaves a trace. It's a small hardening, not a security model, but it's free and it's correct for a tool whose whole pitch is "stores nothing."

One caveat I won't hide: this protects the installed files. The config (~/.config/nullroom/) and any saved transcripts (/save) are still normal writable files — they have to be, since you create them. And 444 won't stop root. Nothing does; that's what root means.
Pull the updated install.sh and re-run it — it'll overwrite the old copies with the locked-down ones.

nullroom — anonymous rooms. No account, no history, no tracking.

One Python server with zero dependencies, two clients, one idea: privacy by storing nothing. Messages live in RAM. IPs are never written down. Kill the process and everything is gone — unrecoverably, including for the operator. That's the whole pitch, and it's architectural, not a promise.

What it does

Walk into a room by name and talk. No login, no signup, nothing to leak.
Create your own room (Ctrl-N), get a 6-character ID, share it with one line: nullroom --invite host/room?id=xxxxxx. Same link opens in a browser.
Join by ID alone — no need to know the room name. Lock it with a key, rotate the ID to kill the old one, close it for everyone at once (/close).
Room browser, topics, mentions with beep and desktop ping, scrollback search, transcript export (only when you ask), three themes, latency readout.
Two front ends: a web client (landing page, live room list, dark/light) and a full-screen terminal client built for Kali — ANSI panes, works over SSH and tmux, no ncurses, no pip.
What it doesn't do

No end-to-end encryption. The server sees plaintext while it runs. It's not Signal — it just forgets everything.
No moderation beyond the creator's close button. Optional profanity/URL filters exist on the server.
One instance only. Rooms are per-process memory; scale it and you get two disjoint worlds.
Status: complete and verified. 25 server checks plus 43 client checks, all passing. The web client runs at http://127.0.0.1:8100; the Kali client installs with bash install.sh (read-only files, no sudo). Deploy configs for Docker, Fly.io, Render, and Caddy are in the repo. MIT licensed, on GitHub, ready to clone.

ok tell users to what actually tool did

If you're writing the words a new user sees — the GitHub tagline, the landing page, the thing you tell a friend — use this:

nullroom lets you talk freely, with no account and nothing kept.

Open a room, pick up a random name, type. When the room empties, the conversation is gone — not archived, not logged, not recoverable. There is no database, no IP log, no analytics, no cookies. The server literally cannot show your messages to anyone tomorrow, because it won't have them.

What you can do with it:

Talk without signing up. No email, no phone number, no username to protect. Close the tab and you were never there.
Make a room in seconds and share it with a short ID — six characters your friends paste to walk straight in.
Lock it with a key only your people have. Rotate the ID when it leaks. Close the room and it's over for everyone, instantly.
Use it from a browser or a Kali terminal. Same rooms, same invites, both ends anonymous.
What it won't do for you:

It won't hide you from someone watching the network right now — there's no end-to-end encryption. Its protection is forgetting, not armoring.
It won't moderate. A room is yours to run; the tools are lock, rotate, and close.
It won't remember. That's the feature, and it's also the price.
That's the honest version. It sells the thing without promising what it can't deliver — and for a privacy tool, the second half is what makes the first half believable.

nullroom.zip — 59 KB, 20 files, verified by extracting it back. Unzips into a single nullroom/ folder with the structure intact:
nullroom/
├── server.py, ws.py, selftest.py
├── static/   (index.html, app.css, app.js)
├── kali/     (client.py, tui.py, tests_tui.py, install.sh)
├── Dockerfile, docker-compose.yml, fly.toml, render.yaml, Caddyfile
├── README.md, LICENSE, .gitignore, .dockerignore

No logs, no cache files, no scratch — I cleaned those out before packing.
On Kali:
unzip nullroom.zip
cd nullroom
python3 server.py            # terminal 1 — the server
cd kali && bash install.sh  # terminal 2 — the client
nullroom --server 127.0.0.1:8080 --room kali

## What it is

A chat server and two clients, written in pure Python with **zero dependencies** —
no framework, no `pip install`, no Node, no build step. One port serves HTTP and
WebSockets. Everything runs from a single folder:

```
nullroom/
├── server.py          HTTP + WebSocket server, rooms, ids, locks, rate limits
├── ws.py              hand-rolled RFC6455 (standard library only)
├── selftest.py        end-to-end server suite — 25 checks
├── static/            web client: landing page, room list, live chat
│   ├── index.html
│   ├── app.css
│   └── app.js
├── kali/              full-screen terminal client for Kali Linux
│   ├── client.py      TUI client — zero dependencies, no ncurses
│   ├── tui.py         ANSI rendering engine
│   ├── tests_tui.py   TUI suite — 43 checks
│   └── install.sh     installer for Kali / Debian / Ubuntu
├── Dockerfile
├── docker-compose.yml
├── fly.toml
├── render.yaml
├── Caddyfile
└── LICENSE
```

## Features

### Server

- **In-memory rooms** — messages live in RAM, die with the process
- **Shareable room IDs** — 6 characters, derived from `sha256(name + salt)`; no mapping stored
- **Join by ID** — walk in without knowing the room name
- **Locked rooms** — optional key required to enter
- **ID rotation** — the old ID dies the instant a new one is issued
- **Room topics** — set by the creator, broadcast to everyone
- **Creator-owned rooms** — ownership is claimed with the name, never by arrival order
- **Close-room kill switch** — the creator deletes a room for everyone, at once
- **Rate limiting** — per-connection token bucket, slow clients dropped, not buffered
- **Idle reaping + hard TTL** — empty rooms and old rooms are deleted automatically
- **Optional filters** — profanity masking and URL stripping (`FILTER_PROFANITY=1`, `FILTER_URLS=1`)
- **Hardened HTTP** — CSP, HSTS, X-Frame-Options, path-traversal rejection, `/healthz`

### Web client

- Landing page with live room list, headcounts and lock indicators
- Real-time chat with typing indicators and auto-reconnect
- **Full invite panel** — create, copy the invite, lock/unlock, rotate the ID
- Paste a room ID or a full invite into the room box to join directly
- Dark and light themes, responsive layout
- Zero external requests (enforced by Content-Security-Policy)

### Kali terminal client

| # | Feature | How |
|---|---------|-----|
| 1 | Room browser | `Ctrl-R` — every live room with headcounts |
| 2 | Full-screen TUI | ANSI panes, 256 colours, resize-safe, works over SSH and tmux |
| 3 | Live chat | Real-time broadcast, typing indicators |
| 4 | Anonymous identity | Random handle per session, `/nick` to regenerate |
| 5 | Create your own room | `Ctrl-N` or `/create name Topic` |
| 6 | Shareable room ID | 6-char ID, `Ctrl-I` to copy the invite |
| 7 | Join by ID | `/join k6bb4h` — no need to know the name |
| 8 | Locked rooms | `/lock key` — key required to enter (`/key` for a random one) |
| 9 | Rotate the ID | `/rotate` — old ID dies immediately |
| 10 | Invite links | `nullroom --invite host/room?id=xxxxxx` — same link works in a browser |
| 11 | Room topics | `/topic text` (creator only) |
| 12 | Mentions | Your handle lights up, plus beep and desktop notification |
| 13 | Scrollback search | `Ctrl-F`, `/next` to step through hits |
| 14 | Transcript export | `Ctrl-S` or `/save` — only ever when you ask |
| 15 | Server profiles | Saved to `~/.config/nullroom/config.json` |
| 16 | Latency stats | Ping/pong round trip in the header |
| 17 | Themes | `Ctrl-T` — dark, light, high-contrast |
| 18 | URL detection | `/urls`, `Ctrl-O` to open the last one |
| 19 | Close the room | `/close` (or `Ctrl-X`) — creator deletes it for everyone |

---

## Quick start

Requires **Python 3.9+** and nothing else.

```bash
git clone https://github.com/<you>/nullroom.git
cd nullroom

# start the server
python3 server.py
# open http://127.0.0.1:8080
```

Create a room on the landing page, then open the same room in a second tab —
you're chatting.

## Kali terminal client

```bash
cd kali
python3 tests_tui.py        # 43 checks, should print OK
bash install.sh             # installs the `nullroom` command to ~/.local/bin
nullroom --server 127.0.0.1:8080 --room kali
```

Or run it straight from the checkout, no install needed:

```bash
python3 client.py --server chat.example.com --room kali
```

Works over SSH and inside tmux. Needs an interactive terminal (`isatty`).

## Creating a room and inviting people

```
Ctrl-N  (or /create my-room Our topic)   create a room
/invite  (or Ctrl-I)                     show and copy the invite
/lock mykey                              require a key to enter
/rotate                                  new ID — the old one stops working
/close    (or Ctrl-X)                    delete the room for everyone, at once
```

The person you're inviting runs **one** of these — both go to the same room:

```bash
# terminal
nullroom --invite chat.example.com/my-room?id=6uvc3f
```

```
# browser — open this URL
https://chat.example.com/r/my-room?id=6uvc3f
```

Room IDs are `sha256(room name + per-room salt)`, so the server stores no
mapping — the ID is computed, not looked up. An ID is an **identifier, not a
secret**: anyone holding it can enter, unless the room is locked, in which case
the key is required too. Send the key separately from the ID if it matters.

---

## Configuration

Every setting is an environment variable with a safe default.

| Variable | Default | Meaning |
|---|---|---|
| `HOST` | `0.0.0.0` | Bind address |
| `PORT` | `8080` | Bind port |
| `MAX_ROOMS` | `200` | Cap on simultaneous rooms (memory bound) |
| `MAX_USERS_PER_ROOM` | `80` | Cap on people in one room |
| `MAX_MESSAGE_CHARS` | `1000` | Messages are truncated, never rejected |
| `MAX_MESSAGES_PER_MIN` | `30` | Per-connection token bucket, burst-tolerant |
| `BACKLOG_PER_ROOM` | `50` | Messages replayed to someone entering a room |
| `ROOM_IDLE_SECONDS` | `1800` | Empty room is deleted after this |
| `ROOM_TTL_SECONDS` | `21600` | Hard room lifetime (6 h) |
| `SEND_QUEUE_LIMIT` | `200` | Slow clients are disconnected, not buffered forever |
| `FILTER_PROFANITY` | `0` | `1` = mask the small built-in word list |
| `FILTER_URLS` | `0` | `1` = strip links to `[link removed]` |
| `ID_SALT` | `nullroom-v1` | Per-deployment salt for room IDs |
| `ACCESS_LOG` | `0` | `1` = print method + path + status. Never an IP. |
| `TRUST_PROXY` | `0` | `1` only behind a reverse proxy you control |

> **Scaling note:** rooms are per-process memory. Keep it at **one instance**.
> Two instances means two disjoint sets of rooms.

---

## Privacy

These are not policies that could quietly change — they are what the code does:

| Claim | How it is true |
|---|---|
| No accounts | There is no user table and no auth code. A name is a string the client sends. |
| No history | Messages live in `Room.backlog`, a Python list in RAM. There is no `open()` for writing anywhere in the codebase. |
| Nothing survives a restart | Killing the process destroys every message — unrecoverably, including for the operator. |
| No IP logging | A client's address is used only as the live socket's peer address. It is never stored, never logged, never broadcast. `ACCESS_LOG=1` prints method + path + status only. |
| No tracking | No cookies, no localStorage, no fingerprinting, no analytics. The web frontend makes zero third-party requests. |
| No persistence at the edge | `docker-compose.yml` runs `read_only: true` with no volumes. |

**The honest limit, stated on the landing page too:** this is private by *not
keeping anything*, not by cryptography. While the process runs, the operator
(or anyone who can instrument the machine) can read traffic in memory. There is
no end-to-end encryption. Do not use it for anything you would mind being read.

---

## Deploy

### Docker on any VPS (recommended)

```bash
docker compose up -d --build
```

Then put TLS in front. The included `Caddyfile` is two lines after your domain:

```
chat.example.com {
    reverse_proxy 127.0.0.1:8080
}
```

Caddy provisions the certificate automatically; WebSockets pass through `reverse_proxy`
with no extra config. Set `TRUST_PROXY=1` so rate limits apply per visitor, not per proxy.

### Fly.io

```bash
fly launch --no-deploy   # accept the existing fly.toml
fly deploy
fly scale count 1        # single instance: rooms are in RAM
```

`fly.toml` is set up with one machine, `force_https`, and a `/healthz` check.

### Render

**New → Blueprint**, point at the repo. `render.yaml` sets the runtime, health
check and environment. Note: free instances sleep, and a sleeping instance loses
its rooms (which is on-brand).

### Railway / Heroku-style

The start command is `python -u server.py`; the app reads `PORT`. Keep the
instance count at 1.

### Behind Cloudflare

Works, but understand the trade: Cloudflare terminates TLS and sees client IPs
and message contents. If the claim is "nobody watches this", proxying through a
third party undercuts it. Skip the CDN, or say so on your landing page.

### Before you go public

- [ ] TLS on, HTTP redirecting to HTTPS (the app sends HSTS; it needs TLS to mean anything)
- [ ] One instance only
- [ ] `ACCESS_LOG=0` unless you have a reason (it never logs IPs even when on)
- [ ] Memory limit set (256 MB is generous for ~200 rooms)
- [ ] `read_only: true`, no volumes — nothing can be written even by accident
- [ ] Moderation decision made: rooms are unmoderated by default; creators can close their own rooms, and `FILTER_PROFANITY` / `FILTER_URLS` exist if you want them
- [ ] Privacy text on the landing page matches what the code does — it does today; keep it that way

---

## Testing

```bash
# server: start it, then run the suite against it
python3 server.py &
python3 selftest.py 8080        # 25 checks — handshake, broadcast, backlog,
                                # size caps, flood throttling, traversal, emptying

# terminal client: no terminal needed
cd kali && python3 tests_tui.py # 43 checks — widths, wrapping, editing, history,
                                # invites, mentions, tabs
```

Expected output ends with `all checks passed` / `OK` in both.

## Protocol

Client ↔ server messages are JSON over WebSocket. The interesting ones:

| Direction | Message | Purpose |
|---|---|---|
| → | `{"t":"hello","nick":"…"}` | Claim a display name |
| → | `{"t":"create","room":"…","topic":"…","nick":"…"}` | Create a room you own |
| → | `{"t":"msg","text":"…"}` | Send a message |
| → | `{"t":"id"}` | Ask for this room's ID |
| → | `{"t":"rotate-id"}` | New ID (creator only) |
| → | `{"t":"set-topic","topic":"…"}` | New topic (creator only) |
| → | `{"t":"lock","key":"…"}` | Lock/unlock (creator only) |
| → | `{"t":"close-room"}` | Delete the room for everyone (creator only) |
| ← | `{"t":"joined","room":{…},"created":true}` | Room state, incl. `id`, `owner`, `locked` |
| ← | `{"t":"room-id","id":"…","you_own_it":…}` | Confirmed ID |
| ← | `{"t":"closed","room":"…","reason":"…"}` | The room was deleted |

Join by ID over the URL: `/ws/lobby?id=k6bb4h[&key=…]`. Room IDs are 6
characters from an unambiguous alphabet (`23456789abcdefghjkmnpqrstuvwxyz`).

## Known limitations

- **No end-to-end encryption.** The server sees plaintext. A deliberate trade for a server that stores nothing — not a substitute for Signal.
- **No message edit or delete.** Messages are immutable once sent.
- **Nicknames are not unique.** Two people can pick the same name; nothing prevents it because nothing identifies anyone.
- **Rooms are invite-gated, not private.** Anyone holding the ID can enter unless the room is locked — and the server sees plaintext either way.
- **Moderation is the close button.** Creators can delete their own room for everyone. No bans, no mutes, no report queue. Optional server-side word/URL filters exist.
- **Single-process.** No horizontal scaling without added infrastructure.

## Contributing

Pull requests welcome. Ground rules:

1. **Zero dependencies** — standard library only, on both server and clients. A PR that adds a requirement will not merge.
2. **Privacy is architectural** — nothing may write messages, IPs, or metadata to disk. If it needs persistence, it needs a design discussion first.
3. **Tests for features** — server behavior goes in `selftest.py`, client logic in `kali/tests_tui.py`.
4. **Honest copy** — if a feature changes what the privacy table promises, the table and the landing page change in the same PR.

## License

[MIT](LICENSE)
