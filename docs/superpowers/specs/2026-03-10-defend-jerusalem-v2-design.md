# Defend Jerusalem v2 Design

## Context
BSF Lesson 22 Build Community activity for Zoom online class. Interactive web app where leader screen-shares and students shout/chat their defense choices. Teaching point: human effort cannot save — only God can. Lands on Main Truth: "Jesus will defeat His enemies and reign as King forever without rival."

## Container
- 16:9 letterbox container, centered on screen, black bars if needed
- Fullscreen button top-right corner (uses `element.requestFullscreen()`)
- All positioning uses % relative to container, not viewport

## Phase 1: "Castle Defense"
- Header label: "Castle Defense"
- Castle sits at ~55% from top (visual focal point)
- Health bar at top, resets to 100% each round

### Waves

| Wave | Title | Options | Defense Animation |
|---|---|---|---|
| 1 | Armies Attack | Reinforce Walls / Send Archers / Call for Allies | Wall glows blue briefly |
| 2 | Siege & Famine | Ration Supplies / Dig a Tunnel / Negotiate Peace | Green pulse on buildings |
| 3 | Betrayal Within | Unite the People / Root Out Spies / Strengthen the Guard | Warm glow spreads across city |
| 4 | Final Assault | Last Stand / Defend the Gate / Set a Trap | Red glow + sparks on wall |

### Per-round flow
1. Wave title appears. 3 options shown.
2. Student picks one -> defense animation plays (1s)
3. Attack hits (shake, arrows/fire) -> castle fully destroyed (buildings fall, rubble)
4. Health bar drops to 0
5. Brief pause on rubble (2s)
6. Castle and health bar reset to full. Next wave.
7. Round 4: castle stays destroyed. No reset.

### Bridge screen
- Dark overlay
- Text: "No human defense could save this castle. But there is a city that will never fall."
- Button: "Defend Jerusalem"

## Phase 2: "Defend Jerusalem"
- Label changes to "Jerusalem"
- City rebuilds, health bar resets to 100%
- **Same wave UI as Phase 1** to create visual contrast:
  - Wave title: "All Nations Attack Jerusalem"
  - Subtitle: "All the nations of the earth are gathered against her." — Zechariah 12:3
  - **One button only**: "Trust in God's Promises" (where 3 options used to be)
- Click the button → golden aura appears, attack hits but city holds (arrows dissolve)
- Then verses play one at a time above city:

### Verse sequence
1. "I will make Jerusalem an immovable rock for all the nations." - Zechariah 12:3
   - Aura visible. Hold 6s. Fade out verse.
2. "A fountain will be opened to cleanse them from sin and impurity." - Zechariah 13:1
   - Water flows at base. Aura strengthens. Hold 6s. Fade out verse.
3. "The LORD will be king over the whole earth." - Zechariah 14:9
   - Dawn sky. Health bar turns gold: "Jerusalem Restored." Hold 6s. Fade out verse.
4. Main Truth fades in: "Jesus will defeat His enemies and reign as King forever without rival."
   - Stays on screen. Discussion launch.

### Protection aura
- CSS `box-shadow` on `#city-container` with `animation: pulse 3s ease-in-out infinite`
- Starts subtle on verse 1, grows brighter on verse 2, full brightness on verse 3
- Warm golden color, not literal shield graphic

## Technical approach
- Single HTML file, no dependencies beyond Google Fonts
- CSS animations only (keyframes + class toggles), no canvas/WebGL
- 4 defense animations (one per wave category)
- Attack animations: screen shake, arrows, fire/smoke (carried from v1)
- All strings hardcoded (no user input, no XSS surface)
