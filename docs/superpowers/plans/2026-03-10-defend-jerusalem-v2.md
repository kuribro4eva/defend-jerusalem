# Defend Jerusalem v2 Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rebuild the Defend Jerusalem activity with 16:9 container, centered castle, per-round destruction/reset, bridge transition, and verse-by-verse Phase 2 with golden aura.

**Architecture:** Single HTML file with embedded CSS and JS. 16:9 letterbox container with %-based positioning. Phase 1 is "Castle Defense" (generic), Phase 2 transitions to "Jerusalem" (scriptural). All animations are CSS keyframes + class toggles.

**Tech Stack:** HTML, CSS (animations, transitions), vanilla JS, Google Fonts (Cinzel + Inter)

**Spec:** `docs/superpowers/specs/2026-03-10-defend-jerusalem-v2-design.md`

**Existing file to rewrite:** `index.html` (current v1)

**Test method:** Playwright MCP browser at `http://localhost:8321` — take screenshots at each milestone.

---

## Chunk 1: Container + City Layout

### Task 1: 16:9 Container with Fullscreen Button

**Files:**
- Modify: `index.html` (full rewrite)

- [ ] **Step 1: Write the outer shell**

Replace entire `index.html` with the new structure:
- `<body>` with black background
- `<div id="game">` with `aspect-ratio: 16/9`, centered via flexbox, `max-width: 100vw`, `max-height: 100vh`, `position: relative`, `overflow: hidden`
- Fullscreen button: `<button id="fullscreen-btn">` positioned absolute top-right, z-index 200, calls `document.getElementById('game').requestFullscreen()`
- `<div id="scene">` inside game container, `position: absolute; inset: 0`
- Sky, stars, ground, dark-overlay divs inside scene
- Google Fonts import for Cinzel + Inter

CSS for `#game`:
```css
#game {
  aspect-ratio: 16 / 9;
  max-width: 100vw;
  max-height: 100vh;
  position: relative;
  overflow: hidden;
  background: #0a0a1a;
}
body {
  margin: 0;
  background: #000;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
  width: 100vw;
  overflow: hidden;
}
```

Fullscreen button CSS:
```css
#fullscreen-btn {
  position: absolute;
  top: 2%;
  right: 2%;
  z-index: 200;
  background: rgba(40, 35, 25, 0.7);
  border: 1px solid #5a5040;
  color: #a09080;
  padding: 6px 14px;
  font-family: 'Inter', sans-serif;
  font-size: 12px;
  border-radius: 3px;
  cursor: pointer;
}
```

- [ ] **Step 2: Verify container renders at 16:9**

Open `http://localhost:8321` in Playwright. Take screenshot. Verify black bars appear and the game container is 16:9.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: 16:9 container shell with fullscreen button"
```

### Task 2: Centered City as Focal Point

- [ ] **Step 1: Build the city at 55% vertical position**

City container positioned at `top: 55%; left: 50%; transform: translate(-50%, -100%)` (bottom of city sits at 55% mark).

Larger city than v1:
- Wall: 500px wide, 50px tall
- 18 merlons on wall
- Gate centered in wall
- 2 towers (left/right, 90px tall)
- 7 buildings ranging 80-130px tall, spread across 450px

Ground: `bottom: 0; height: 30%` of game container.

Stars: generated in top 45% of scene.

All positions use `%` or are relative to `#city-container`.

- [ ] **Step 2: Add health bar at top center**

Health bar at `top: 4%; left: 50%; transform: translateX(-50%)`. Width: 35% of container.

Label above bar: "Castle Defense" (Phase 1 label, will change to "Jerusalem" in Phase 2).

- [ ] **Step 3: Verify city is centered and prominent**

Take screenshot. City should be the visual focal point in the center-bottom area, health bar at top, sky and stars above.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: centered city layout with health bar"
```

## Chunk 2: Phase 1 Game Loop

### Task 3: Title Screen + Wave UI

- [ ] **Step 1: Build title screen**

Title panel centered in scene:
- "Castle Defense" in Cinzel 48px, gold color
- "Can you protect the castle?" in Inter 16px, muted
- "Begin" button (class `btn`)

- [ ] **Step 2: Build wave attack panel**

Attack panel positioned at `bottom: 8%` of game container:
- Wave title (Cinzel 24px, red)
- Wave subtitle (Inter 13px, muted)
- 3 choice buttons in a row

Result text positioned at `top: 25%` center — shows failure message after each round.

- [ ] **Step 3: Wire up title → wave 1 transition**

Click Begin → hide title panel, show wave 1 ("Armies Attack" with 3 options).

- [ ] **Step 4: Verify in browser**

Take screenshot of title screen and wave 1.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: title screen and wave UI"
```

### Task 4: Per-Round Destroy + Reset Loop

- [ ] **Step 1: Define wave data**

```js
var waves = [
  {
    title: "Armies Attack",
    subtitle: "Enemy forces charge the walls",
    choices: ["Reinforce Walls", "Send Archers", "Call for Allies"],
    results: [
      "The walls hold briefly... but the enemy breaks through.",
      "Arrows fly, but there are too many soldiers.",
      "No allies answer. You are alone."
    ],
    defenseAnim: "defense-walls"  // blue wall glow
  },
  {
    title: "Siege & Famine",
    subtitle: "Supply lines are cut off",
    choices: ["Ration Supplies", "Dig a Tunnel", "Negotiate Peace"],
    results: [
      "Rations run low. The people grow weak.",
      "The tunnel collapses. No supplies get through.",
      "The enemy refuses. They want total surrender."
    ],
    defenseAnim: "defense-supplies"  // green building pulse
  },
  {
    title: "Betrayal From Within",
    subtitle: "Division spreads among the people",
    choices: ["Unite the People", "Root Out Spies", "Strengthen the Guard"],
    results: [
      "Fear is stronger than your words. People lose hope.",
      "Suspicion turns neighbor against neighbor.",
      "The guards are exhausted. Morale crumbles."
    ],
    defenseAnim: "defense-unity"  // warm glow across city
  },
  {
    title: "The Final Assault",
    subtitle: "Everything they have, all at once",
    choices: ["Last Stand", "Defend the Gate", "Set a Trap"],
    results: [
      "You fight bravely... but it's not enough.",
      "The gate splinters. They pour through.",
      "The trap springs but barely slows them down."
    ],
    defenseAnim: "defense-combat"  // red glow + sparks
  }
];
```

- [ ] **Step 2: Implement per-round flow**

```
selectDefense(choiceIndex):
  1. Disable buttons, highlight chosen
  2. After 500ms: hide attack panel
  3. Play defense animation (1s) — add defenseAnim class to city-container
  4. After 1s: remove defense class, play attack (shake + arrows + fire)
  5. After 400ms: health drops to 0, city destroyed (all buildings/wall/towers get .fallen)
  6. After 1.5s: show result text
  7. After 3.5s: hide result text
  8. If NOT last wave:
     - After 4.5s: reset city (remove .fallen from all), health bar back to 100%
     - After 5.5s: show next wave
  9. If last wave:
     - After 4.5s: show bridge screen
```

- [ ] **Step 3: Implement city destroy + reset functions**

```js
function destroyCity() {
  document.querySelectorAll('.building').forEach(function(b) {
    b.classList.add('fallen');
  });
  document.querySelectorAll('.tower').forEach(function(t) {
    t.classList.add('fallen');
  });
  document.getElementById('wall').classList.add('fallen');
}

function resetCity() {
  document.querySelectorAll('.building').forEach(function(b) {
    b.classList.remove('fallen', 'cracked');
  });
  document.querySelectorAll('.tower').forEach(function(t) {
    t.classList.remove('fallen', 'cracked');
  });
  document.getElementById('wall').classList.remove('fallen', 'cracked');
  health = 100;
  var bar = document.getElementById('health-bar');
  bar.style.width = '100%';
  bar.classList.remove('low');
}
```

- [ ] **Step 4: Verify full 4-wave loop in browser**

Play through all 4 waves. Verify: castle destroys each round, resets between rounds, stays destroyed after round 4.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: per-round destroy and reset game loop"
```

### Task 5: Defense Animations (4 categories)

- [ ] **Step 1: Add CSS keyframes for 4 defense types**

```css
/* Wave 1: Wall glow blue */
@keyframes defense-walls {
  0% { box-shadow: none; }
  50% { box-shadow: 0 0 30px rgba(100, 150, 255, 0.6), inset 0 0 15px rgba(100, 150, 255, 0.3); }
  100% { box-shadow: none; }
}
#city-container.defense-walls #wall {
  animation: defense-walls 1s ease-in-out;
}

/* Wave 2: Green pulse on buildings */
@keyframes defense-supplies {
  0% { filter: brightness(1); }
  50% { filter: brightness(1.4) hue-rotate(-20deg); }
  100% { filter: brightness(1); }
}
#city-container.defense-supplies .building {
  animation: defense-supplies 1s ease-in-out;
}

/* Wave 3: Warm glow across city */
@keyframes defense-unity {
  0% { box-shadow: none; }
  50% { box-shadow: 0 0 40px rgba(255, 180, 80, 0.5); }
  100% { box-shadow: none; }
}
#city-container.defense-unity {
  animation: defense-unity 1s ease-in-out;
}

/* Wave 4: Red glow + sparks */
@keyframes defense-combat {
  0% { box-shadow: none; }
  30% { box-shadow: 0 0 25px rgba(255, 60, 30, 0.6); }
  60% { box-shadow: 0 0 35px rgba(255, 100, 30, 0.8); }
  100% { box-shadow: none; }
}
#city-container.defense-combat #wall {
  animation: defense-combat 1s ease-in-out;
}
```

- [ ] **Step 2: Wire defense animation into selectDefense flow**

In `selectDefense`, after hiding buttons and before attack:
```js
var city = document.getElementById('city-container');
city.classList.add(wave.defenseAnim);
setTimeout(function() {
  city.classList.remove(wave.defenseAnim);
  playAttack(wave, choiceIndex);
}, 1000);
```

- [ ] **Step 3: Verify each wave's defense animation**

Play through all 4 waves, screenshot each defense animation.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: 4 category defense animations"
```

### Task 6: Attack Animations (carried from v1)

- [ ] **Step 1: Port arrow spawning from v1**

Same `spawnArrows()` function but positions calculated as % of game container, not viewport.

- [ ] **Step 2: Port fire + smoke from v1**

Same `spawnFires()` function, % positions. Fire on waves 3 and 4.

- [ ] **Step 3: Screen shake**

Same shake keyframe on `#scene` (not `#game` — the container stays still, contents shake).

- [ ] **Step 4: Verify attacks look good in 16:9**

Play through waves, verify arrows/fire/shake are visible and centered on city.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: attack animations ported to 16:9 container"
```

## Chunk 3: Bridge + Phase 2

### Task 7: Bridge Screen

- [ ] **Step 1: Build bridge panel**

New panel `#bridge-panel`, centered in scene:
- Text: "No human defense could save this castle." (Cinzel 28px, muted red)
- Pause
- Text: "But there is a city that will never fall." (Cinzel 24px, gold)
- Button: "Defend Jerusalem" (class `btn`)

Dark overlay active behind it.

- [ ] **Step 2: Wire bridge into game flow**

After wave 4 castle stays destroyed → dark overlay fades in → bridge panel appears.

Click "Defend Jerusalem" → start Phase 2.

- [ ] **Step 3: Verify bridge screen**

Take screenshot of bridge.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: bridge transition screen"
```

### Task 8: Phase 2 — Verse by Verse with Aura

- [ ] **Step 1: Build verse display panel**

`#verse-panel` positioned at `top: 15%`, centered, `max-width: 70%`:
- Semi-transparent background `rgba(10, 10, 26, 0.7)`
- Single verse text (Cinzel 24px, gold) + reference (Inter 13px, muted)
- Fades in/out via `.visible` class with CSS transitions

- [ ] **Step 2: Add golden aura CSS**

```css
@keyframes aura-pulse {
  0%, 100% { box-shadow: 0 0 20px rgba(200, 170, 100, 0.2), 0 0 40px rgba(200, 170, 100, 0.1); }
  50% { box-shadow: 0 0 30px rgba(200, 170, 100, 0.4), 0 0 60px rgba(200, 170, 100, 0.2); }
}
#city-container.aura-1 { animation: aura-pulse 3s ease-in-out infinite; }
#city-container.aura-2 { animation: aura-pulse 3s ease-in-out infinite;
  box-shadow: 0 0 30px rgba(200, 170, 100, 0.3), 0 0 50px rgba(200, 170, 100, 0.15); }
#city-container.aura-3 { animation: aura-pulse 3s ease-in-out infinite;
  box-shadow: 0 0 40px rgba(200, 170, 100, 0.5), 0 0 70px rgba(200, 170, 100, 0.25); }
```

- [ ] **Step 3: Implement Phase 2 sequence**

```
startPhase2():
  1. Change label from "Castle Defense" to "Jerusalem"
  2. Hide health bar
  3. Show verse panel

  At 500ms:
    - Show verse 1 text ("I will make Jerusalem an immovable rock...")
    - Rebuild city (remove .fallen, add .restored)
    - Add .aura-1 to city
    - Lift dark overlay

  At 6500ms:
    - Fade out verse 1

  At 7500ms:
    - Show verse 2 ("A fountain will be opened...")
    - Add water flowing
    - Upgrade to .aura-2

  At 13500ms:
    - Fade out verse 2

  At 14500ms:
    - Show verse 3 ("The LORD will be king...")
    - Dawn sky
    - Show health bar as gold "Jerusalem Restored"
    - Upgrade to .aura-3

  At 20500ms:
    - Fade out verse 3

  At 22000ms:
    - Show Main Truth: "Jesus will defeat His enemies and reign as King forever without rival."
    - Stays on screen. Done.
```

- [ ] **Step 4: Verify full Phase 2 sequence**

Play through entire game into Phase 2. Screenshot each verse state and final Main Truth screen.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: verse-by-verse Phase 2 with golden aura"
```

### Task 9: Final Polish + Push

- [ ] **Step 1: Full playthrough test**

Play entire game start to finish in Playwright. Verify:
- Title screen → Begin
- 4 waves: defense anim → attack → destroy → reset (rounds 1-3), stay destroyed (round 4)
- Bridge screen
- Phase 2: 3 verses one at a time, aura, dawn, Main Truth
- Fullscreen button works

- [ ] **Step 2: Push to GitHub Pages**

```bash
git push origin main
```

Verify deployment at `https://kuribro4eva.github.io/defend-jerusalem/`

- [ ] **Step 3: Commit any final fixes**
