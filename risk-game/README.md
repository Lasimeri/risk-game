# Risk Game

A web-based strategy board game inspired by classic Risk. Play against AI opponents or friends in your browser.

## How to Play

Open `index.html` in any modern web browser. No server or build step required.

### Game Rules

**Objective:** Conquer all 43 territories to win.

**Phases:**

1. **Reinforce** — Click your territories to place armies. You receive armies based on territories owned (minimum 3) plus continent bonuses.

2. **Attack** — Click one of your territories (with 2+ armies), then click an adjacent enemy territory to attack. Dice are rolled automatically.

3. **Fortify** — Move armies from one territory to an adjacent one you control. Click source, then destination, choose armies, then confirm.

**Turn Structure:** After fortifying, play passes to the next player. AI players take their turns automatically.

### Controls

- **Left Click:** Select territory / attack target
- **Right Click:** Cancel selection
- **Space:** End current phase
- **Escape:** Close fortify modal

### Territory Map

6 continents with bonuses:
| Continent | Bonus | Territories |
|-----------|-------|-------------|
| Asia | +7 | 12 |
| North America | +5 | 8 |
| Europe | +5 | 7 |
| Africa | +3 | 6 |
| South America | +2 | 4 |
| Oceania | +2 | 5 |

### Combat

- Attacker rolls up to 3 dice (must leave 1 army behind)
- Defender rolls up to 2 dice
- Compare highest dice: highest wins, loser removes 1 army
- Ties go to defender
- Capture occurs when defender reaches 0 armies

## Features

- 2-6 player support with AI opponents
- Real-time army count display on territories
- Continent ownership tracking with bonuses
- Dice combat with visual feedback
- Sound effects (optional, via Web Audio API)
- Combat log with battle history
- Dark military-themed UI

## Technical Details

Single HTML file with embedded CSS and JavaScript. No external dependencies except Google Fonts (optional).

- **Language:** Vanilla JavaScript
- **Rendering:** SVG-based map
- **Audio:** Web Audio API
- **Fonts:** System fonts (no external dependencies)

## License

MIT