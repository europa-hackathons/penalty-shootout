# Penalty Shootout

A football penalty-shootout game that runs in the browser on laptop and phone. You are the kicker and the computer is the goalkeeper. Each level has 5 kicks and you need 3 goals to clear it. Every third level is a boss fight, there are nine bosses, and beating the last one at level 27 ends the game. You can play with a mouse, touch or keys, or with your hand through a webcam. English and Swedish text.

Built by Bryn and two young developers Miles and Maxim. The whole game is one HTML file with plain JavaScript and a canvas. There are no libraries to install, no build step and no server (except for the camera, see below).

## Run it

**Mouse, touch and keys:** open `penalty-shootout-fixed.html` in a browser. That's it.

**Camera controls:** a browser only allows the camera on `localhost` or https, so opening the file directly won't work. From the game's folder run:

```
python3 -m http.server 8765
```

then open `http://localhost:8765/penalty-shootout-fixed.html` in Chrome or Edge. The first time you choose Camera, allow the camera when the browser asks. The tracking library and model load from the internet (jsDelivr and Google), so a locked-down network can block it. If the camera can't start, the game says why and you can play with the mouse.

Notes:

- Pictures load from an `assets` folder next to the HTML file, or from pictures built into the file (see below). If a picture is missing, the game draws a stand-in in code, so nothing breaks.
- The "Russo One" font comes from Google Fonts. Offline, the game falls back to Impact or Arial Narrow.
- On a phone the game asks you to turn the phone sideways.

## Files

| File | What it is |
| --- | --- |
| `penalty-shootout-fixed.html` | The game. Code and built-in pictures in one file. |
| `penalty-shootout-spec.md` | The design spec: rules, difficulty, bosses, camera controls, assets. Read this first for the "why". |
| `embed-assets.html` | Browser tool that builds pictures from `assets` into the game file. No installs. |
| `assets/` | Source pictures (not always in the repo). See the names below. |

## Controls

**Home screen:** choose how to play. Mouse / touch (press M or Space) or Camera (press C). L switches language.

**Mouse, touch and keys**
- **Aim:** move the mouse or drag a finger, or use the arrow keys.
- **Start the timing ring:** click, lift your finger, or press space.
- **Kick:** click, tap, or press space when the white ring is on the green.

**Camera**
- **Menus:** a white cursor follows your index finger. Point at a button and hold still for about a second (a ring fills) to press it. On screens with no button (boss intro, level result, the ending) make a fist to continue. The first time you choose Camera you still need a click or the C key, because the camera isn't running yet.
- Choosing Camera opens a four-step tutorial. Each step ticks off when the camera sees it. "Skip tutorial" (or Esc) goes to team selection.
- **Aim:** point with your index finger.
- **Start the ring:** make a fist. The aim locks to where your finger was just before the fist.
- **Kick:** open your hand when the ring is on the green.
- A camera is slower than a click, so the green zone is about 40% wider while it is on, and the kick timing is shifted back by a delay (120 ms to start). Press `[` or `]` to change it in 20 ms steps while playing.
- The camera is off by default. Video stays in the browser and is never recorded or uploaded.

## How the game is organised

Everything is in the `<script>` block of `penalty-shootout-fixed.html`. Useful landmarks, which you can search for by name:

- **`S`** is the game state. `S.phase` is the state machine: `menu`, `tutorial` (camera only), `team`, `intro` (boss levels only), `aim`, `ring`, `shot`, `result`, `end`.
- **`P(level)`** sets the difficulty: ring time, green-zone width, keeper reaction time, and how often the keeper picks the right side. This is where to tune.
- **`BOSSKEYS`** lists the bosses in order: rexo, kraken, frostbite, titan, gus, phil, clown, hugo, zorp. **`bossOf(level)`** returns the boss for a level, or -1. **`FINAL_LEVEL`** (27) and **`gameWon()`** handle the ending.
- **`kick()`** and **`resolve()`** decide the result of a kick (goal, save, miss) and apply each boss's special rule. Bosses are identified by their position in `BOSSKEYS`. Other places with boss rules: `P()` (Frostbite's slow ring), `ringW()` (Clockwork Clown's changing ring speed), `nextAim()` (picks Hydra Hugo's sleeping head), `drawKeeper()` (Phantom Phil's flicker), and `drawBossFx()` (Hugo's "Zzz" and Zorp's magnet).
- **`TEXT`** holds all on-screen words, in `en` and `sv`, including the boss names and one-line descriptions. Add new text to both languages.
- **`TEAMS`** lists the teams: id, player name, shirt number, and kit colours.
- **`EMBED`** holds the pictures that are built into the file. **`IMG`** holds every loaded picture.
- **`drawKeeper`, `drawBoss`, `drawIntro`, `drawBall`, `paintBackground`** draw the keeper and bosses, the code-drawn stand-in bosses, the boss intro banner, the ball, and the stadium and pitch. The background is painted once and reused. Set `bgDirty=true` to repaint it.
- **`BOSS_DIVE_W`** controls how large a diving boss is drawn.
- **Camera:** the `CAM` object and the functions `camStart`, `camLoop`, `camDetect` and `camFeed` run the hand tracking. `camFeed` turns hand landmarks into aim, fist and open-hand actions. `camUI`, `camButtons` and `camClick` run the hands-free menus (`UIPH` lists the menu screens): a finger hovering on a button for a second presses it by sending a real tap to the canvas. `drawHome`, `drawTutorial`, `drawCursor` and `drawCam` draw the home cards, the tutorial, the finger cursor and the corner preview.

Saved in the browser (`localStorage`): `best` (best level), `lang`, and `team`.

## Pictures

The game looks for these names in `assets` (PNG for figures, JPEG or JPG for backgrounds):

- `stadium`, `pitch`, `ball`, `keeper-stand`, `keeper-dive-left`
- `kicker-<team>` and `kicker-<team>-kick`, where team is `miami`, `barcelona` or `sweden`
- `boss-<name>` and `boss-<name>-dive`, where name is `rexo`, `kraken`, `frostbite`, `titan`, `gus`, `phil`, `clown`, `hugo` or `zorp`
- `stadium-<name>-boss`, a different stadium for each boss level, with the same nine names
- Optional: `boss-hugo-sleep-left`, `boss-hugo-sleep-middle` and `boss-hugo-sleep-right`, Hydra Hugo with that head asleep
- Optional: `tutorial-point`, `tutorial-fist` and `tutorial-open`, hand pictures for the camera tutorial

Notes:

- Figures need a transparent background. Dive pictures show the boss diving to its left, and the game flips them for the right.
- A boss without a dive picture tilts its standing picture instead. A boss without a standing picture is drawn as a simple stand-in shape. A boss without its own stadium uses `stadium`.
- Pictures built into the file win over pictures in `assets`. Built in now: the team kickers, the stadium and pitch, the ball, the keeper, all nine standing bosses, the dive pictures and stadiums for Phantom Phil, Clockwork Clown, Hydra Hugo and Zorp, Hugo's three sleeping-head pictures, and the tutorial hands. **Not built in yet:** the dive pictures and boss stadiums for Rexo, Kraken, Frostbite, Titan and Gus. They show when you run the game from your computer with the `assets` folder, but not on a phone or the published link until you run `embed-assets.html` with them.

### Building pictures into the game file

Pictures in `assets` only show when you open the game from your own computer. A phone, or a link you share, can't read your `assets` folder. To fix this:

1. Open `embed-assets.html` in Chrome or Edge.
2. Choose the game file, then select the pictures from `assets`.
3. Click build. A new game file downloads with the pictures inside and your original is untouched.

The tool crops each picture to the figure, removes the green edge left by background removal, and shrinks it. The result is a bigger file, around 1 to 3 MB depending on how many pictures you add. Run it again whenever you add or change a picture. Use Chrome or Edge, because Safari can't make the picture format the tool needs.

## Adding things

- **A language:** add an entry to `TEXT` with all the same keys, and a button to `LBTN`.
- **A team:** add an entry to `TEAMS` and a name to `TEXT.<lang>.teams`. Add `kicker-<id>` and `kicker-<id>-kick` pictures. The team picker has room for three cards, so a fourth team needs the layout in `TBTN` changed. A team with `soon:true` shows as "Coming soon" and can't be picked.
- **A boss:** add its key to `BOSSKEYS`, its glow colour to `BOSSGLOW`, and its name and description to `TEXT.<lang>.bosses`. Add a code-drawn stand-in in `drawBoss`, a sound in `bossSfx`, and its rule in `kick()`. `FINAL_LEVEL` moves automatically, so the last boss in the list is the one that ends the game. Add the picture names to the loader list near `EMBED`.

## Testing

There are no automated tests. We check the game by playing it, and by taking screenshots with a headless browser. The quickest ways to test a specific situation are to set the state from the browser console, for example `S.level=24; newLevel()` to jump to a boss level. To test the camera logic without a camera, call `camFeed(landmarks, performance.now())` with 21 made-up hand points (index fingertip is point 8): an extended index finger is "pointing", a curled index finger with at least two other curled fingers is a fist.

## Things to know

- **Real player names:** the team picker shows the names of real players (as text only). There are no club crests or sponsor logos. Check the licensing before sharing the game widely.
- **Credits and licences:** hand tracking uses Google MediaPipe Tasks Vision (Apache 2.0). Parts of the tracking approach (smoothing filter, spike rejection and the zoomed crop) are adapted from the BDOU hackathon game, with permission from its creators. That game is all rights reserved, so don't reuse more of it without asking.
- **Spec:** `penalty-shootout-spec.md` is the source of truth for the design. Update it when you change how the game plays.
- **Known gaps:** there is no real slow-motion replay of goals, no 2-player mode, no profile picker yet, and the first choice of Camera needs a click or the C key. These are in the spec as later ideas.
