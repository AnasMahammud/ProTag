# PRO TAG

A fast 3D parkour tag game. Vault, slide, mantle, wall-jump and dodge across four arenas: Neon Rooftops, Pulse Arena (a chase-tag stadium), Sunset Dockyard and the Training Ground. Play offline against bots or online against friends through the included authoritative server.

**No installation beyond Python.** The game runs in any modern browser (Chrome, Edge or Firefox). The server is plain Python 3 with no `pip install`.

---

## Quick start (Windows)

1. Install **Python 3.10 or newer** from <https://www.python.org/downloads/>. Tick *"Add python.exe to PATH"* during setup.
2. Double-click **`start-game.bat`**. It starts the server and opens <http://localhost:8080>. (To play with friends over the internet use **`start-online.bat`** instead; see "Play with friends anywhere".)
3. Click **Play → Start offline match**.

**Playing with friends over the internet?** Double-click **`start-online.bat`** instead and send them the link it prints. See [Play with friends anywhere](#play-with-friends-anywhere-no-lan-no-localhost).

Or start it from a terminal (VS Code: *Terminal → Run Task → PRO TAG: start server*):

```bash
python server/main.py
```

Then open <http://localhost:8080>. Stop the server with `Ctrl+C`.

Options:

```bash
python server/main.py --port 9000          # another port
python server/main.py --host 0.0.0.0       # LAN play (see "Online play")
python server/main.py --verbose            # debug logging
```

> The game must be opened through the server (`http://localhost:8080`). Opening `index.html` directly from disk (`file://`) won't work, because browsers block ES modules there.

## How to play

- **Classic Tag:** one player is IT. Touch a runner to pass IT on. The player with the **least total time as IT** wins. New ITs are stunned for 1 s, and there are no instant tag-backs.
- **Infection:** tagged runners join the infected. Runners win if anyone survives the clock; the infected win if everyone is caught.
- **Last Runner:** tagged runners are eliminated and spectate (press Jump to switch who you watch). The last runner standing wins.
- **Practice:** no clock. Train every move on any arena, with 0 to 7 bots. Press **T** to swap who is IT.

Tagging is automatic on contact. The HUD always shows who is IT, a directional arrow to the nearest threat (or, if you are IT, the nearest runner within 30 m), your stamina, and the dodge and slide cooldowns. Hold **Tab** for the scoreboard.

### Controls (defaults, all rebindable in Settings → Controls)

| Action | Keyboard / mouse | Controller | Touch |
|---|---|---|---|
| Move / look | WASD / mouse | Left / right stick | Left half drag / right half drag |
| Jump, wall jump | Space | A | Jump button |
| Sprint | Shift (hold) | RB / RT (hold) or L3 | Sprint toggle, or push the stick fully |
| Slide | C (or F) | B or LT | Slide button |
| Dodge | Q or right mouse | LB | Dodge button |
| Vault / climb | E (also automatic) | X | Vault button |
| Emotes | 1 · 2 · 3 | D-pad | — |
| Scoreboard | Tab | View | — |
| Pause | Esc / P | Menu | ❚❚ |

Ctrl isn't a default binding on purpose: in a browser window, Ctrl+W (slide + forward) closes the tab. If you bind Ctrl, play in fullscreen (Settings → Graphics), where the game locks browser shortcuts.

**Movement tips:** run into low walls to auto-vault. Jump at ledges up to about 3 m to mantle up. Jump again while touching a wall to wall-jump (twice). Jumping out of a slide keeps its momentum. Dodge has a cooldown and does **not** make you invulnerable.

## Online play

The server in `server/` is authoritative. It decides IT status, tags, eliminations, timers, results and XP. Clients send only their movement (validated for speed, teleports, wall-clipping and freeze) plus tag *requests*, which the server checks with lag compensation.

### Play with friends anywhere (no LAN, no localhost)

Your friends only need a web browser and a link. There are two ways to get that link:

**A. One-click public link from your PC: `start-online.bat` (quickest, no account)**

1. Double-click **`start-online.bat`**. The first time, it offers to download Cloudflare's free tunnel program `cloudflared` (about 60 MB, from Cloudflare's official GitHub releases) into `tools/bin/`. Or install it yourself with `winget install --id Cloudflare.cloudflared`.
2. It prints a public address like `https://wild-otter-example.trycloudflare.com` (also copied to your clipboard) and opens the game for you.
3. Send that link to your friends. They open it in any browser, from anywhere, and join your lobby. The lobby's **Copy invite link** button and friend invites use the public address automatically.

The server itself stays private to your PC; Cloudflare relays only the game. The address changes each time you start it, and hosting stops when you close the window. Quick tunnels are a free service with no uptime guarantee, which is ideal for game nights.

**B. Permanent online server (always on, same link every time)**

Deploy the `server/` once to a cloud host. Then nobody runs anything locally: everyone opens your site (e.g. `https://pro-tag.onrender.com`), makes lobbies and joins friends. The project is ready to deploy. The server reads `$PORT` and the platform's public address by itself, and `/healthz` is its health check.

- **Render (free plan):** put this folder in a GitHub repository, then on render.com choose **New → Blueprint** and pick the repo. `render.yaml` configures everything. Free instances sleep after about 15 minutes idle, so the first visit after that takes up to a minute to wake.
- **Railway / Heroku-style hosts:** deploy the repo; `Procfile` starts the server.
- **Fly.io, Koyeb, Google Cloud Run, or any Docker host:** use the included `Dockerfile` (`docker build -t pro-tag .` then `docker run -p 8080:8080 pro-tag`).
- **Your own VPS:** `python3 server/main.py --host 0.0.0.0 --public-url https://your.domain` behind a TLS proxy (Caddy or nginx) for `https://` and `wss://`.

No credentials or API keys are needed or stored anywhere.

### Same PC or same network

- **Same computer:** open two browser windows at `http://localhost:8080`.
- **Local network (LAN):** run `start-lan-server.bat` (or `python server/main.py --host 0.0.0.0`). Friends open the `http://<your-ip>:8080` address printed in the console. Allow Python through the Windows firewall on *private* networks when asked.

### Lobbies and friends

Everything is under **Play → Online with friends**, and the **Friends** button on the main menu:

- **Create lobby:** choose *Public* (listed in the lobby browser and Quick play) or *Friends only* (hidden; you need the code, the link or an invite). You become the host.
- **Invite friends**, in any of three ways:
  - **Copy invite link** in the lobby. Friends open it and land straight in your lobby (`http://<server>/?join=CODE`). When you play on `localhost`, the link automatically uses your LAN or public address.
  - **Lobby code:** friends type the 5 letters under *Join with code*.
  - **Friend codes:** everyone has a permanent friend code (e.g. `6LU-Z72`) on the Friends screen. Add each other once, and from then on you can see who's online and what they're doing, press **Invite** (they get a pop-up with a Join button), or press **Join** on a friend's lobby. A friends-only lobby can only be joined by friends you invited. Players you meet in lobbies appear under *Recently played with*.
- **Browse lobbies:** a live list of public lobbies with host, mode, arena and player count.
- **In the lobby:** text chat with history, ready up, add other players as friends.
- **Host controls:** rename the lobby, set the mode, arena, privacy and size (2 to 10), kick players (they can't rejoin for 2 minutes), and start the match once everyone is ready. If the host leaves, the next player becomes host.
- **Quick play** drops you into the fullest open public lobby, or creates one.

If your connection drops, the client reconnects automatically and you keep your place (including a running match) for 30 s. Friend codes belong to the browser profile on your device; two tabs in the same browser share one code.

## Settings

Everything in Settings changes the real game and is saved on your device:

- **Audio:** master, music and SFX volume.
- **Controls:** mouse X/Y, controller and touch sensitivity, invert Y, always-sprint, full key rebinding.
- **Camera:** FOV, distance, speed-FOV, shake, landing dip.
- **Graphics:** Low/Medium/High/Ultra presets, resolution scale, frame-rate limit, FPS counter, fullscreen.
- **Accessibility:** reduce flashing and shake, HUD scale, larger text.
- **Network:** custom server address.

On first launch the game picks a preset to suit your GPU.

## Progression

You earn XP for every match: participation, tags, survival time, wins, and a top-3 bonus. Levels unlock cosmetics: suit, trim and accent colours, 10 hats, trails, tag effects and emotes. Some unlock through achievements (wins, lifetime tags, total survival time). The Profile screen shows lifetime stats and recent matches. Cosmetics never change gameplay. There are no purchases or loot boxes.

---

## Project structure

```
index.html              entry page (import map -> vendored three.js)
start-game.bat          one-click launcher (Windows)
start-lan-server.bat    launcher for LAN play
config/game.json        ALL gameplay tuning, shared by client and server
src/
  core/         config loader, event bus, settings, storage, input (kbd/mouse/gamepad/touch), math
  movement/     collision world (AABB + ramps, grid broad-phase) and the character controller
  gameplay/     rules engine, Game (renderer/loop), session base, offline LocalMatch
  multiplayer/  WebSocket client, OnlineMatch (prediction + interpolation)
  ai/           navigation graph + A*, bot brain
  player/       procedural character model + animation, Player entity
  camera/       third-person camera rig
  maps/         map loader, renderer, procedural textures, decor prefabs, sky/backgrounds
  effects/      pooled particles, rings, trails, lightning
  audio/        synthesised SFX engine + procedural music sequencer
  progression/  XP/levels/stats, cosmetics catalogue
  ui/           menus, HUD, touch controls, character preview, stylesheet
server/         Python stdlib server: HTTP + RFC 6455 WebSocket, rooms, match loop,
                rules (port of src/gameplay/rules.js), movement validation, map collision
tools/build_maps.py     map source -> assets/maps/*.json (run after editing maps)
assets/maps/            generated map JSON (rendering, physics, AI and server validation)
tests/                  Python unit and integration tests, browser test suite, shared rule cases
docs/                   architecture, testing and limitations notes
vendor/three/           three.js r170 (MIT) + post-processing add-ons
```

See **[docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)** for the technical design and decisions.

### Editing the game

- **Tuning** (speeds, jump height, cooldowns, match length, bot difficulty, XP): edit `config/game.json`. Client and server both read it.
- **Maps:** edit `tools/build_maps.py`, then run `python tools/build_maps.py`. The builder validates spawns and geometry before writing.
- **Rules:** change `src/gameplay/rules.js` **and** `server/rules.py`, and add a case to `tests/shared/rules_cases.json`. Both test suites run it.

## Tests

```bash
python -m unittest discover -s tests -v                      # 52 server, rules, integration, social and hosting tests
# with the server running, open http://localhost:8080/tests/browser/   (65 browser tests)
```

Latest results: **52/52 Python tests** and **65/65 browser tests** passing. Details, the manual verification checklist, and what could not be tested are in **[docs/TESTING.md](docs/TESTING.md)**.

## Requirements and performance

- Windows 10/11 (also works on macOS/Linux), Python 3.10+, and a WebGL 2 browser (current Chrome, Edge or Firefox).
- Measured on the development laptop's **Intel UHD integrated GPU** at 1024×768, in a full 10-player match on the busiest map (Neon Rooftops). Figures are frame cost converted to FPS (CPU simulation + GPU render, forced to finish):

  | Preset | Frame cost | ≈ FPS |
  |---|---|---|
  | Low | 8.1 ms | 123 |
  | Medium (auto-selected for this GPU) | 7.9 ms | 126 |
  | High | 13.9 ms | 72 |
  | Ultra | 26.4 ms | 38 |

  Simulation of all 10 players, including bot AI, costs about 1–1.5 ms per frame. A discrete GPU at 1080p should comfortably run High; that hasn't been measured on other hardware.

## Known limitations

Also listed in [docs/TESTING.md](docs/TESTING.md#limitations).

- **I couldn't watch the reference video** (only its title, "World Chase Tag USA 2021", was accessible). The design follows the publicly known chase-tag format and this project's design brief.
- **Bots are offline only.** Online rooms are human players (minimum 2).
- **Progression is stored locally** in the browser. Online XP is computed by the server, but local storage can still be edited by the player, so this isn't tamper-proof persistence.
- **Anti-cheat is practical, not perfect:** the server validates speed, teleports, solids and freeze, and checks tags with lag compensation, but it doesn't re-simulate full movement physics.
- **Characters are procedural primitives**, animated procedurally; there are no skinned or motion-captured models.
- **Not tested on real hardware here:** gamepads, physical touch devices, and real internet latency. Each is implemented to the standard browser APIs; see TESTING.md.
- **There's no Exit button.** It isn't applicable in a browser: close the tab, or stop the server with Ctrl+C.
- **No moving platforms.** Jump pads are the only dynamic traversal.

## Roadmap

1. Server-side bots for online rooms (needs a Python port of the controller and navigation, or a shared WebAssembly core).
2. A server-trusted account and progression service (signed results or a small database).
3. Skinned character models with authored animations, and more emotes.
4. Moving platforms, ziplines and destructible cover, with server reconciliation.
5. Replays and a kill-cam for tags, plus spectator free-cam.
6. Matchmaking across several server instances, region selection and a server browser.
7. Mobile packaging (PWA) and controller-first menu polish.
8. A map editor UI on top of `tools/build_maps.py`.

## Credits and licensing

- Game code, maps, procedural art and audio: original to this project (Anas Studios, built with Claude).
- [three.js](https://threejs.org) r170, MIT licence (`vendor/three/LICENSE`).
- All textures are generated at runtime from code, and all music and sound effects are synthesised with the Web Audio API. There are no third-party asset files.
- PRO TAG is an independent project and isn't affiliated with any chase-tag league, broadcaster or brand.
