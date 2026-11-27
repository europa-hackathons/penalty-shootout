# Penalty Shootout: Game Design Spec (v0.4)

Prepared for Bryn and the two young game developers, who are building this game together. Status: the game is built and playable, with nine bosses and an ending, and this spec is updated as it changes.

## 1. Vision

A football penalty-shootout game that runs in a web browser on both laptop and mobile. This first version is single player: the human is the kicker and the computer is the goalkeeper. Each level has 5 kicks, and you win the level by scoring 3 or more. The keeper gets harder with every level.

## 2. Developers and player

- **Developers:** Bryn and the two kids design and build the game as a team.
- **Mode:** version 1 is single player, with one human kicker against the computer keeper. There is no 2-player mode yet.
- **Player profile:** a **name**, a chosen **national team look**, and saved **level progress**. Anyone can save a profile on the same device and take turns, one player at a time.
- Progress is saved on the device only, with no accounts and no sign-up. Nothing is sent over the internet.
- A profile picker is the first screen, and players can switch at any time.

## 3. Core loop (one kick)

1. **Aim:** place a target inside the goal.
2. **Time:** a ring closes in on the ball.
3. **Kick:** tap or click when the ring is in the right place.
4. **Result:** the keeper dives, and the kick ends as a goal, a save, or a miss.

After 5 kicks the level ends. 3 or more goals is a win and unlocks the next level. Fewer than 3 goals means you lose the level and must play it again. Play stops early once the result is certain (for example, 3 goals scored).

## 4. Controls (laptop and mobile)

Both platforms use the same two actions, so the game feels identical everywhere.

| Action | Laptop | Mobile |
| --- | --- | --- |
| Aim | Move the mouse (or arrow keys) | Touch and drag on the goal |
| Kick | Click or press space | Tap anywhere |

- Aiming and kicking are separate steps, so a finger never covers the ring at the moment of timing.
- On mobile the game asks the player to turn the phone sideways (landscape). The screen scales to fit any size.
- All buttons are large enough for small fingers.

Camera controls (optional): players can use their hand instead of a mouse or touch. The home screen asks "How do you want to play?" with two cards, Mouse / touch (press M or Space) and Camera (press C). Mouse and keys keep working at any time.

- **Tutorial:** choosing Camera opens a four-step tutorial: show your hand, point to aim, make a fist to start the ring, and open your hand to kick. Each step is ticked off when the camera sees it. A "Skip tutorial" button (or Esc) goes straight to team selection.
- **Playing:** point with the index finger to aim, make a fist to start the ring (the aim locks to where the finger was just before the fist), and open the hand when the ring is on the green to kick.
- **Menus:** with the camera on, a white cursor follows the index finger. Point at a button and hold still for about a second (a ring fills) to press it, on the home, team and tutorial screens. On screens with no button (boss intro, level result, game complete) make a fist to continue. After the game ends, pointing at the Camera card again goes straight to team selection. The first choice of Camera still needs a click or the C key, because the camera is not running yet and the browser asks for permission.
- **Timing:** a webcam is slower than a click, so the green zone is about 40% wider with the camera on, and the kick timing is shifted back by a delay (120 ms to start, adjustable with [ and ] while playing).
- **Privacy and setup:** hand tracking (Google MediaPipe) runs in the browser, and no video is recorded or uploaded. The camera is off by default. It needs https or localhost, and internet access to load the tracking library and model, so it does not work from a file opened directly or from the published artifact link.
- **Credit:** parts of the tracking approach (smoothing, spike rejection and the zoomed crop) are adapted from the BDOU hackathon game, with permission from its creators.

## 5. The timing ring

A ring closes in on the ball. Where the ring is when you kick decides the shot.

| Ring zone | Timing | Result |
| --- | --- | --- |
| Red (outer) | Far too early | Weak dribble, easy save or a miss |
| Yellow | A little early | A bit soft, easy for the keeper |
| **Green** | Just right | Perfect, powerful shot exactly on target |
| Yellow | A little late | A bit hard, drifts slightly off target |
| Red (inner) | Far too late | Blasted over the bar or wide, a miss |

- Green gives a flash and a "PERFECT!" pop. Red gives a red flash and a crowd groan.
- A perfect shot is much harder for the keeper to reach.
- As levels rise, the ring closes faster and the green zone shrinks.
- Colour is not the only signal: zones also differ in brightness and pattern, so the ring is readable for colour-blind players.

## 6. Keeper difficulty ladder

Starting values to tune during playtesting.

| Level | Reaction delay | Dive speed | How it chooses a side | Green zone | Ring speed |
| --- | --- | --- | --- | --- | --- |
| 1 | Very slow | Slow | Random | Very wide | Slow |
| 2 to 3 | Slow | Slow | Random | Wide | Slow |
| 4 to 5 | Medium | Medium | Random, sometimes a lucky guess | Medium | Medium |
| 6 to 8 | Quick | Fast | Remembers your favourite side | Narrow | Fast |
| 9 and up | Very quick | Very fast | Learns your habits | Very narrow | Very fast |

- Every **3rd level (3, 6, 9 and so on up to 27) is a boss keeper fight, designed together by the whole team. See the boss tables below**.
- Keepers have small tells (for example, leaning before a dive), so sharp players can learn to read them.
- A keeper can never be perfect: a green shot into a top corner always has a chance of going in.

* A shot aimed straight at the keeper's body is always saved. Kraken Keeper's tentacles make the blocked area wider.

Each boss gets a name, a costume or monster look, and one signature move. Bryn and the kids design them together.

| Level | Boss name | Costume or monster | Signature move |
| --- | --- | --- | --- |
| 3 | Rexo the T-Rex | A dinosaur in keeper gloves with tiny arms | Huge leaps for high shots, but weak on low corners |
| 6 | Kraken Keeper | A sea monster with tentacles in the net | Tentacles shoot out to block the corners |
| 9 | Frostbite | An ice giant that freezes over the pitch | Slows your ring down for one kick |
| 12 | The Titan | A giant robot goalkeeper | Teleports to where you aim, unless you hit green |
| 15 | Gargoyle Gus | A stone statue that comes to life | Stays frozen until you kick, then slams across the goal |

Bosses, continued:

| Level | Boss name | Costume or monster | Signature move |
| --- | --- | --- | --- |
| 18 | Phantom Phil | A friendly ghost goalkeeper | Flickers in and out, and turns solid just before the kick |
| 21 | Clockwork Clown | A wind-up toy clown made of brass and tin | The timing ring speeds up and slows down |
| 24 | Hydra Hugo | A friendly three-headed monster | One of the three heads sleeps on each kick, and a shot into its third of the goal cannot be saved. The awake heads save everything except green shots |
| 27 | Zorp the Alien | A green alien with a magnet | Pulls shots halfway toward the middle unless they are green |

Zorp is the final boss. Beating him ends the game: a screen says "You beat the game! Congratulations! Time to go pro.", and a tap returns to the home screen.

Boss fight presentation:

- **Intro card:** a boss level opens with a red banner showing "BOSS FIGHT", the boss's name, a one-line description and the boss's picture. It starts the level on a tap or space after a short pause, or by itself after 4 seconds.
- **Own sounds:** each boss has its own sound for its signature move: Rexo roars, Kraken squelches, Frostbite chimes on his ice kick, the Titan zaps and Gus slams.
- **Own stadium:** each boss level uses its own stadium picture instead of the standard one.
- **Boss defeated:** beating a boss shows "BOSS DEFEATED!" with extra confetti, fireworks and a short fanfare.

## 7. Levels and scoring

- 5 kicks per level. Score 3 or more goals to win it; with fewer than 3 you lose the level and play it again.
- A scoreboard shows 5 dots (green for a goal, red for a miss or a save).
- Each profile remembers the highest level reached and the best score per level.
- The game ends after the final boss at level 27, with a congratulations screen that says it is time to go pro.

## 8. National team looks

- Players choose a national team look, built from that country's flag colours and a simple kit design (stripes, halves, or a plain shirt).
- No official crests or sponsor logos are used. The team picker names one real player for each team, shown as text only.
- Teams and players in the team picker: Inter Miami is Lionel Messi, FC Barcelona is Lamine Yamal, and Sweden is Viktor Gyökeres. Shirt numbers: 10 for Inter Miami and FC Barcelona, 17 for Sweden.
- In the game now: Inter Miami, FC Barcelona and Sweden. Ideas for later: Brazil, Argentina, England, Germany, France, Spain, Netherlands, Italy, Japan, USA, Nigeria. This list is easy to extend.
- The kicker's shirt, shorts and socks update on the field and in the profile picker.

## 9. Screens

1. **Who's playing?** The profile picker, with a button to add a new player.
2. **Pick your team.** A grid of team looks.
3. **Level select or Next level.** Shows progress.
4. **The pitch.** Goal, keeper, ball, aim target, timing ring and scoreboard.
5. **Boss intro.** Boss levels only: the banner with the boss's name, description and picture.
6. **Level result.** Win or retry, with confetti on a win. After the final boss, a congratulations screen says the game is won.
7. **Settings.** Music on or off, effects on or off.

## 10. Audio

- **Music:** a looping upbeat tune on the menus and a calmer one during play. All music is created for this game, using simple synthesised tracks, so nothing is borrowed from existing songs.
- **Sound effects:** ball kick, net ripple, crowd cheer, crowd groan, post hit, whistle, and a keeper save.
- **Boss sounds:** each boss has its own sound for its signature move, and a short fanfare plays when a boss is beaten.
- Music and effects have separate on or off switches.
- Browsers block sound until the first tap, so the first screen asks the player to tap to start.

## 11. Look and feel

- The long-term goal is a realistic look, like a real football match in a FIFA-style game: a floodlit stadium, a detailed pitch, a roaring crowd, and lifelike players and keepers. Boss keepers are the exception, with their own costumes and monster looks.
- The game uses realistic image files for the stadium, pitch, ball, keeper, kickers and bosses. Anything missing is drawn in code, so the game keeps working while images are added one at a time.
- Short animations: kick, dive, net ripple, confetti.
- **Kick feedback:** the screen shakes on a save, the ball slows into the net on a goal, the ball leaves a trail (golden on a perfect green shot), and phones vibrate on a goal or a save.

## 12. Technical approach

- The game is a single HTML file (penalty-shootout-fixed.html) with plain JavaScript and the canvas. The audio is synthesised in the browser, and images are built into the file with embed-assets.html. No libraries or installs.
- One input system based on pointer events, which covers mouse and touch.
- The canvas scales to the screen size, in landscape.
- Profiles and progress are stored in the browser on the device.
- The game logic (keeper AI, scoring, difficulty) is kept separate from the drawing, so it is easy to tweak.

## 13. Build plan

1. Pitch, goal and ball on screen, scaling for laptop and phone.
2. Aim target with mouse and touch.
3. Timing ring and kick power and accuracy.
4. Ball flight and goal, save and miss detection.
5. Keeper movement and the difficulty ladder.
6. Five-kick levels, scoreboard and win or retry.
7. Profiles, team looks and saved progress.
8. Music, sound effects and polish.

Version 2 then swaps the drawn shapes for realistic image files and final boss artwork. Each step of version 1 produces something playable, so the kids can test it and give feedback at every stage.

## 14. Assets

The game loads images from an `assets` folder placed next to `penalty-shootout-fixed.html`. If an image is missing, the game keeps using the drawn version, so images can be added one at a time and the game never breaks. Pictures built into the game file work on a phone or a shared link. Pictures in the assets folder only show when the game is run from a computer, so to make a picture show everywhere, build it into the game file (see below).

| File | What it shows | Notes |
| --- | --- | --- |
| stadium.jpg | Night stadium stands, crowd and floodlights | Wide 16:9, no pitch or goal |
| pitch.jpg | Mown grass from behind the penalty spot | Wide 16:9, no lines |
| goal.png | Goal frame and net from the front | Transparent background |
| ball.png | Football | Transparent background |
| keeper-stand.png | Goalkeeper facing us, arms spread | Transparent background, vertical 1:2 |
| keeper-dive-left.png | Goalkeeper in a dive to his left | Transparent background, wide 2:1. The game flips it for the right side |

Boss pictures: boss-rexo.png, boss-kraken.png, boss-frostbite.png, boss-titan.png, boss-gus.png, boss-phil.png, boss-clown.png, boss-hugo.png and boss-zorp.png, one standing picture per boss. Every boss also has a dive picture (boss-NAME-dive.png) and its own stadium (stadium-NAME-boss.jpeg), where NAME is rexo, kraken, frostbite, titan, gus, phil, clown, hugo or zorp.

Boss dive pictures: each boss can also have a dive picture, drawn diving to the left. The game flips it for dives to the right, as it does for the human keeper, and draws it about 1.35 times as wide as the boss's standing picture is tall. Until a boss has one, the game tilts its standing picture into the dive.

| File | What it shows | Notes |
| --- | --- | --- |
| boss-rexo-dive.png | Rexo the T-Rex diving to his left | Transparent background, wide 2:1 |
| boss-kraken-dive.png | Kraken Keeper diving to its left | Transparent background, wide 2:1 |
| boss-frostbite-dive.png | Frostbite diving to his left | Transparent background, wide 2:1 |
| boss-titan-dive.png | The Titan diving to his left | Transparent background, wide 2:1 |
| boss-gus-dive.png | Gargoyle Gus diving to his left | Transparent background, wide 2:1 |

Optional pictures with Hydra Hugo's sleeping head: three versions of his standing picture, one for each head that can be asleep: boss-hugo-sleep-left.png, boss-hugo-sleep-middle.png and boss-hugo-sleep-right.png (left and right as the player sees them). The game uses the matching one while Hugo stands. The game also tints the sleeping head's third of the goal blue and shows "Zzz" over it.

Optional tutorial pictures for the camera tutorial: tutorial-point.png, tutorial-fist.png and tutorial-open.png, one hand picture for each step.

Team and stadium pictures:

| File | What it shows | Notes |
| --- | --- | --- |
| kicker-miami.png, kicker-barcelona.png, kicker-sweden.png | The team's kicker from behind, standing | Transparent background, vertical 1:2 |
| kicker-miami-kick.png, kicker-barcelona-kick.png, kicker-sweden-kick.png | The same kicker mid-kick | Transparent background |
| stadium-rexo-boss.jpeg, stadium-kraken-boss.jpeg, stadium-frostbite-boss.jpeg, stadium-titan-boss.jpeg, stadium-gus-boss.jpeg | A different stadium for each boss level | Wide 16:9, no pitch or goal. Used instead of stadium.jpg on that boss's level; if a file is missing the standard stadium is used |

Building the pictures into the game file:

1. Open embed-assets.html in Chrome or Edge. It runs in the browser, with no installs, and nothing is uploaded.
2. Choose the game file, then select the pictures from the assets folder.
3. Click the build button. A new game file downloads with the pictures inside, and the original is untouched. Pictures already in the game stay unless a new one has the same name.

The tool crops each picture to the figure, removes the green edge left by background removal, and shrinks it to the right size.

Rules for all images:

- One style: a photorealistic night match under floodlights, so the pieces match.
- No logos, text, sponsors or real player likeness. Kits are plain colours.
- Transparent PNGs: generate on a solid green background (#00ff00), then remove it with a background-remover.
- Animation comes from code moving, rotating, flipping and swapping still images.
- The published link cannot load an assets folder, so build the pictures into the game file with embed-assets.html. The file must stay under about 16 MB.

## 15. Open questions

- Which national teams should be in the first list?
- Which names, costumes and monsters should the boss keepers have? Design them together using the boss table.
- Later idea: a 2-player mode where one kicks and one keeps.
