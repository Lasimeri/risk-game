# Risk Game — Web-Based Strategy Board Game

## 1. Project Overview

**Project Name:** risk-game
**Type:** Turn-based strategy board game (single HTML file, browser-based)
**Core Functionality:** A web-based implementation of the classic Risk board game with 2-6 players, featuring territory conquest through dice-based combat, a simplified world map, and AI opponents.
**Target Users:** Casual strategy game players in a browser

---

## 2. Visual & Rendering Specification

### Scene Setup
- **Rendering:** SVG-based map with clickable territories
- **Layout:** Full-viewport game board with sidebar for game state info
- **Responsive:** Scales to fit viewport, minimum 1024x768

### Visual Style
- **Theme:** Dark military/strategic aesthetic
- **Color Palette:**
  - Background: `#1a1a2e` (deep navy)
  - Map base: `#16213e` (dark blue-gray)
  - Territory borders: `#0f3460` (muted blue)
  - Accent: `#e94560` (crimson red)
  - Text: `#eaeaea` (off-white)
  - Player colors: Red `#e94560`, Blue `#4ecca3`, Green `#ffd369`, Purple `#9b59b6`, Orange `#e67e22`, Cyan `#00bcd4`
- **Typography:** "Cinzel" (Google Fonts) for headers, "Roboto Mono" for numbers/stats

### Map Design
- **Simplified World Map:** 6 continents, ~42 territories total
  - North America (8 territories)
  - South America (4 territories)
  - Europe (7 territories)
  - Africa (6 territories)
  - Asia (12 territories)
  - Oceania (5 territories)
- **Territory Rendering:** SVG paths with fill color based on owner, stroke for borders
- **Hover State:** Territory brightens, shows tooltip with name + army count
- **Selected State:** Pulsing border in player color
- **Attack Path:** Dashed line between attacking and defending territory

### UI Elements
- **Top Bar:** Current phase, current player indicator, turn counter
- **Right Sidebar (280px):**
  - Player list (name, color, territories owned, armies to place)
  - Action buttons (End Phase, End Turn)
  - Combat log (last 5 events)
- **Dice Display:** Modal overlay showing attacker/defender dice (max 3 each)
- **Fortify Modal:** Dropdown to select source territory, amount, destination
- **Victory Modal:** Full-screen overlay announcing winner

### Animations
- Territory conquest: 300ms color transition
- Dice roll: 600ms shake animation before reveal
- Army placement: Subtle pulse on territory when armies added
- Phase transition: Fade overlay 400ms

---

## 3. Game Mechanics Specification

### Players
- 2-6 players (selectable at game start)
- Each player assigned a unique color
- Human player is always Player 1 (Red)

### Game Phases (per turn)

**1. Reinforce Phase**
- Player receives armies = max(3, floor(territories_owned / 3)) + continent_bonus
- Click owned territory to place armies (1 per click, or click+drag for批量)
- Must place all armies before proceeding
- Button: "End Reinforce" → transitions to Attack

**2. Attack Phase**
- Click owned territory (must have 2+ armies) → becomes attacker
- Click adjacent enemy territory → initiates combat
- Combat Resolution:
  - Attacker rolls min(3, armies - 1) dice
  - Defender rolls min(2, armies) dice
  - Compare highest dice: highest wins, loser removes 1 army
  - Compare second highest (if both have 2+): highest wins, loser removes 1 army
  - Ties go to defender
- Attacker can continue attacking or click "End Attack"
- Attacker may fortify after attacking (optional)
- Button: "End Attack" → transitions to Fortify

**3. Fortify Phase**
- Select one owned territory (source, must have 2+ armies)
- Select adjacent owned territory (destination)
- Choose armies to move (slider or input)
- All armies must be moved (cannot leave 0)
- Button: "End Fortify" → ends turn, next player begins

### Combat Rules
- Attacker must have at least 2 armies (1 stays behind)
- Defender can roll 1-2 dice based on armies (1 if 1 army, 2 if 2+)
- Attacker wins tie-breaker comparison
- After attacker wins, defender loses 1 army per lost comparison
- Territory captured when defender reaches 0 armies
- Attacker moves min(1, armies_attacking - 1) armies into captured territory
- Attacker cannot attack same defender territory more than once per turn

### Reinforcement Bonuses
- **Continent Bonuses:**
  - North America: +5
  - South America: +2
  - Europe: +5
  - Africa: +3
  - Asia: +7
  - Oceania: +2

### AI Behavior (Simple)
- **Reinforce:** Place armies on territories with most enemies nearby (border territories)
- **Attack:** Always attack if odds are favorable (attacker dice > defender dice)
- **Fortify:** Move armies from interior territories to borders

### Win Condition
- Control all 42 territories → game ends
- Player who eliminates another player gets their territories
- If human player loses, "DEFEAT" modal with "Play Again" button
- If human player wins, "VICTORY" modal with "Play Again" button

---

## 4. Interaction Specification

### Mouse Controls
- **Left Click:** Select territory (for attack target, fortify source/dest, reinforce placement)
- **Right Click:** Deselect / cancel current action
- **Hover:** Show territory tooltip (name, owner, armies)

### Phase Flow
1. Game start screen: Select number of players (2-6), click "Start Game"
2. Initial setup: Distribute territories randomly, each gets 1 army per territory
3. Reinforce phase begins with Player 1
4. Player completes phase → auto-advance to next phase
5. After Fortify → next player's Reinforce phase
6. Repeat until victory condition

### Keyboard
- `Escape`: Cancel current selection / close modal
- `Space`: End current phase (shortcut for button)

---

## 5. Technical Implementation

### Structure
- Single `index.html` file (no build step, no dependencies except Google Fonts CDN)
- Embedded CSS in `<style>` tag
- Embedded JavaScript in `<script>` tag
- SVG map inline in HTML

### State Management
```javascript
const gameState = {
  phase: 'reinforce', // 'reinforce' | 'attack' | 'fortify'
  currentPlayer: 0,  // index into players array
  turn: 1,
  players: [...],     // { id, name, color, territories: Set, eliminated }
  territories: Map(), // territoryId -> { owner, armies }
  selectedTerritory: null,
  attackTarget: null,
  fortifySource: null,
  fortifyDest: null,
  combatLog: [],
  gameOver: false,
  winner: null
};
```

### Map Data Structure
```javascript
const territories = {
  'north_america': {
    name: 'Alaska',
    continent: 'north_america',
    adjacent: ['kamchatka', 'northwest_territory', 'alberta'],
    path: 'M...' // SVG path data
  },
  // ...
};
```

### Performance
- No heavy computations; pure JavaScript
- SVG rendering is lightweight
- Event delegation on map container for territory clicks

---

## 6. Acceptance Criteria

1. Game loads in browser with start screen showing player count selector
2. Clicking "Start Game" distributes territories randomly and begins Reinforce phase
3. Clicking owned territories during Reinforce places armies
4. Clicking attacker territory then adjacent enemy territory shows dice combat
5. Dice roll resolves correctly with proper army removal
6. Territory capture works correctly with army transfer
7. Phase transitions work: Reinforce → Attack → Fortify → next player
8. AI players take their turns automatically with visible actions
9. Victory/defeat modal appears when game ends
10. "Play Again" resets the game to initial state
11. All 42 territories are clickable and show correct hover/selected states
12. Combat log updates with each attack result