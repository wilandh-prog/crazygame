# Claude Code Prompt: Build a Successful CrazyGames Browser Game

You are building an HTML5 browser game for publication on CrazyGames.com — a platform with 30M+ monthly users. The game must be technically excellent, immediately fun, and fully compliant with CrazyGames' submission requirements.

Work through the following steps sequentially. Complete each step fully before moving to the next. After each step, summarize what you decided/built and confirm before proceeding.

---

## STEP 1: Research & Game Concept Selection

**Goal:** Determine the optimal game concept — one that performs well on CrazyGames AND is technically feasible for you to build as a single-file or small-project HTML5 game.

### Research tasks:
1. Visit https://www.crazygames.com/hot and https://www.crazygames.com/ to identify what's currently trending
2. Visit https://docs.crazygames.com/requirements/quality/ to understand quality guidelines
3. Visit https://docs.crazygames.com/requirements/gameplay/ for gameplay requirements
4. Visit https://docs.crazygames.com/requirements/technical/ for technical requirements

### Analysis criteria:
- **Platform fit:** CrazyGames favors: idle/clicker, merge, .io-style, physics/ragdoll, driving/stunt, puzzle, and action games. Simple controls, instant fun, short session loops.
- **Technical feasibility:** You must be able to build the entire game. This means: no server-side multiplayer, no complex 3D (simple 2D or 2.5D only), no massive asset pipelines. Pure HTML5/JS with Canvas or a lightweight framework.
- **Retention hooks:** The game MUST have progression (unlocks, upgrades, levels, scores). CrazyGames measures average playtime, conversion to gameplay, and retention — games that fail these metrics get rejected after Basic Launch.
- **Differentiation:** Must not be a direct clone. Needs a unique twist or visual identity.

### Tech stack decision:
Based on your research, choose ONE:
- **Phaser 3** (via CDN) — best for 2D games with physics, sprites, animations
- **Pure Canvas/JS** — best for simple mechanics (idle, clicker, puzzle)
- **Three.js** (via CDN) — only if the concept demands simple 3D

Justify your choice.

### Deliverable:
Write a concise Game Design Document (GDD) covering:
- Game title and genre
- Core mechanic (1-2 sentences)
- Progression/retention loop
- Target session length
- Tech stack with justification
- Why this will work on CrazyGames specifically

**Do not proceed until the GDD is written and confirmed.**

---

## STEP 2: Build the Core Game

**Goal:** Create a fully playable game with core mechanics, basic progression, and CrazyGames SDK integration from the start.

### Technical requirements (non-negotiable):
- **Initial download ≤ 50MB** (target ≤ 20MB for mobile homepage eligibility)
- **Total files ≤ 250MB**
- **16:9 aspect ratio** for the game frame
- **Responsive:** Must work in variable iframe sizes. Test at common resolutions.
- **Keyboard layout awareness:** Don't hardcode WASD — French keyboards use AZERTY
- **Prevent default scroll:** Arrow keys and spacebar must not scroll the parent page
- **Touch support** if targeting mobile (strongly recommended for reach)
- **No external API calls** — everything self-contained
- **PEGI 12 compliant** — suitable for ages 12+

### CrazyGames SDK Integration (integrate NOW, not later):
Include the SDK script in `index.html`:
```html
<script src="https://sdk.crazygames.com/crazygames-sdk-v3.js"></script>
```

Initialize before game starts:
```javascript
const crazysdk = window.CrazyGames.SDK;
await crazysdk.init();
```

Implement these SDK hooks:
- **`crazysdk.game.gameplayStart()`** — call when player enters actual gameplay (not menus)
- **`crazysdk.game.gameplayStop()`** — call when player leaves gameplay (death, pause, menu)
- **`crazysdk.game.sdkGameLoadingStart()`** — call when loading begins
- **`crazysdk.game.sdkGameLoadingStop()`** — call when loading completes
- **Environment check:** Only call SDK methods in `local` or `crazygames` environments:
```javascript
const env = crazysdk.environment;
if (env === 'crazygames' || env === 'local') {
    // safe to use SDK
}
```

### Save system using CrazyGames Data module:
```javascript
// Save
crazysdk.data.setItem('saveData', JSON.stringify(gameState));
// Load
const saved = crazysdk.data.getItem('saveData');
```
- Has same API as localStorage — use as drop-in replacement
- 1MB data limit
- Falls back gracefully when SDK is disabled

### Ad integration:
Place ads at natural break points (between levels, after death, on upgrade screens):
```javascript
const callbacks = {
    adStarted: () => { pauseGame(); muteAudio(); },
    adFinished: () => { resumeGame(); unmuteAudio(); },
    adError: (error) => { resumeGame(); unmuteAudio(); }
};
// Midgame ad (between levels/on death)
crazysdk.ad.requestAd("midgame", callbacks);
// Rewarded ad (optional — player chooses to watch for bonus)
crazysdk.ad.requestAd("rewarded", callbacks);
```

**Rules for ads:**
- NEVER show an ad before the first gameplay session
- NEVER interrupt active gameplay
- Minimum 3 minutes between midgame ads
- Always pause game and mute audio during ads
- Rewarded ads must give a meaningful reward

### Game settings listener:
```javascript
crazysdk.game.addSettingsChangeListener((settings) => {
    if (settings.muteAudio) muteAllAudio();
    if (settings.disableChat) disableChat(); // if applicable
});
```

### CSS for the game body:
```css
body {
    margin: 0;
    padding: 0;
    overflow: hidden;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
}
```

### Core game checklist:
- [ ] Game loads in < 3 seconds locally
- [ ] Core mechanic is fun within 5 seconds of starting
- [ ] Onboarding is visual (keyboard overlay / gesture hints), not text walls
- [ ] Max 1 click from load to gameplay
- [ ] Progression system works (scores, levels, unlocks)
- [ ] Save/load via CrazyGames Data module
- [ ] SDK lifecycle events fire correctly
- [ ] Ad placements at natural breaks
- [ ] No scroll leak to parent page
- [ ] Responsive to different iframe sizes

### Deliverable:
A fully playable `index.html` (single file or minimal file set) with working SDK integration.

---

## STEP 3: Visual Polish & Juice

**Goal:** Transform the functional game into something visually distinctive and satisfying. CrazyGames explicitly states: "Graphics should be of high quality. High resolution — quality games are visually pleasing."

### Visual improvements:
- **Consistent art style** — pick one and commit (pixel art, flat/geometric, cartoon, neon)
- **Particle effects** — explosions, trails, sparkles, dust. Add them everywhere something happens
- **Screen shake** on impacts
- **Tweened animations** — nothing should snap; everything eases in/out
- **Color palette** — use a deliberate, limited palette (check coolors.co or similar)
- **UI polish** — buttons have hover/press states, scores animate up, progress bars fill smoothly
- **Background** — not just a flat color. Parallax, gradients, subtle patterns

### Audio:
- Add sound effects for all player actions (jump, click, collect, die, level up)
- Add background music (looping, not annoying)
- Respect the SDK `muteAudio` setting
- Use Web Audio API or Howler.js via CDN

### "Happy time" celebration:
When the player achieves something notable (high score, level complete, achievement):
```javascript
crazysdk.game.happytime();
```
This triggers a confetti animation on the CrazyGames wrapper — use it sparingly for genuine achievements.

### Performance:
- Target 60fps on mid-range hardware
- Profile and optimize draw calls
- Use sprite sheets instead of individual images where possible
- Lazy-load non-critical assets

### Deliverable:
The same game, now visually polished and satisfying to interact with. Every interaction should have feedback (visual + audio).

---

## STEP 4: Retention & Engagement Optimization

**Goal:** Maximize the metrics CrazyGames evaluates: average playtime, conversion to gameplay, and retention.

### Implement:
- **Meta-progression** — something that persists between sessions (currency, unlocks, upgrades)
- **Daily/session rewards** — incentive to return
- **Difficulty curve** — easy start, gradual ramp, occasional spikes
- **"One more round" hooks** — show what's almost unlocked, show the next challenge
- **Rewarded ad value** — make the reward from watching an ad genuinely useful (2x coins, extra life, skip level)
- **Multiple game modes** if feasible (endless, challenge, timed)
- **Leaderboard display** (local is fine — CrazyGames doesn't provide a global leaderboard API for Basic Launch)

### Anti-patterns to avoid:
- Pay walls or hard gates (it's a free browser game)
- Ads that feel punishing
- Long waits with nothing to do
- Unexplained mechanics
- Dead ends where player has nothing left to achieve

---

## STEP 5: Submission Preparation

**Goal:** Package the game for CrazyGames submission.

### Required assets:
Create or describe needed assets:
- **Game cover image:** 512x512 PNG (square thumbnail)
- **Wide cover:** 1280x720 PNG (16:9 promotional image)
- **Gameplay video:** 15-30 second MP4 showing actual gameplay (if possible, describe what to capture)
- **Game description:** 2-3 sentences, catchy, describes the core loop
- **Controls description:** Clear, concise control scheme
- **Tags/categories:** Select appropriate CrazyGames categories

### Pre-submission checklist:
- [ ] Total file size < 250MB, initial load < 50MB (ideally < 20MB)
- [ ] File count < 1500
- [ ] 16:9 aspect ratio
- [ ] Works on Chrome, Firefox, Safari, Edge
- [ ] Mobile touch controls work (if mobile-enabled)
- [ ] SDK init, gameplayStart/Stop, loading events all fire
- [ ] Ads integrated at natural break points
- [ ] Data module saves/loads correctly
- [ ] No console errors
- [ ] No external domain requests
- [ ] PEGI 12 compliant content
- [ ] No app store links
- [ ] Unique game name (not generic like "Chess" or "Puzzle")
- [ ] Default browser scroll prevented for game keys

### Deliverable:
A zip-ready folder with all game files, plus a submission metadata document with title, description, controls, and category recommendations.

---

## General rules for all steps:
- Write clean, well-structured code with comments
- Test after every significant change
- If something isn't working, debug it before moving on
- Prioritize fun over feature count
- When in doubt, keep it simple — a polished simple game beats an unfinished complex one
- All SDK calls must be wrapped in environment checks to work in development
