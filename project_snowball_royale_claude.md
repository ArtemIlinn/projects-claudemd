# ❄️ Snowball Royale

Cold hands. Warm rivalry. Dodge, throw, and survive in the ultimate winter snowball fight. Easy to play. Hard to stay friends.

**Tech stack:** HTML Canvas + JavaScript + Gamepad API (optional)

---

## How it's actually built

**Simple, realistic version:**

1. Top-down 2D arena with:
   - Players (colored circles or simple sprites)
   - Trees/obstacles (static rectangles)
   - Snowballs (flying circles)

2. Each player can:
   - Move in 4 or 8 directions
   - Throw snowballs (aim + shoot)
   - Hide behind trees (blocks snowballs)

3. Game mechanics:
   - Each player has 3 health
   - Get hit = lose 1 health
   - Last player standing wins
   - Round lasts max 2 minutes

4. Controls:
   - **2 players:** Keyboard (WASD + Space for P1, Arrows + Enter for P2)
   - **4 players:** Mix of keyboard + gamepads
   - **Touch:** Virtual joystick for mobile

5. End condition:
   - One player survives
   - Or time runs out (highest health wins)

**This can be one HTML file with canvas and vanilla JavaScript.**

---

## Implementation details

### Game loop structure

```javascript
const game = {
  players: [],
  snowballs: [],
  obstacles: [],
  gameState: 'waiting' // waiting | playing | ended
};

function gameLoop() {
  update();
  render();
  requestAnimationFrame(gameLoop);
}

function update() {
  updatePlayers();
  updateSnowballs();
  checkCollisions();
  checkWinCondition();
}

function render() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  drawObstacles();
  drawSnowballs();
  drawPlayers();
  drawUI();
}
```

### Player object

```javascript
class Player {
  constructor(x, y, color, controls) {
    this.x = x;
    this.y = y;
    this.color = color;
    this.health = 3;
    this.speed = 3;
    this.controls = controls; // { up, down, left, right, shoot }
    this.facingAngle = 0;
    this.isAlive = true;
    this.cooldown = 0; // Snowball throw cooldown
  }

  update() {
    // Movement
    if (keys[this.controls.up]) this.y -= this.speed;
    if (keys[this.controls.down]) this.y += this.speed;
    if (keys[this.controls.left]) this.x -= this.speed;
    if (keys[this.controls.right]) this.x += this.speed;

    // Keep in bounds
    this.x = Math.max(20, Math.min(canvas.width - 20, this.x));
    this.y = Math.max(20, Math.min(canvas.height - 20, this.y));

    // Shooting
    if (keys[this.controls.shoot] && this.cooldown === 0) {
      this.throwSnowball();
      this.cooldown = 30; // 30 frames before next throw
    }

    if (this.cooldown > 0) this.cooldown--;
  }

  throwSnowball() {
    const angle = this.facingAngle;
    const speed = 8;
    snowballs.push(new Snowball(
      this.x,
      this.y,
      Math.cos(angle) * speed,
      Math.sin(angle) * speed,
      this
    ));
  }

  takeDamage() {
    this.health--;
    if (this.health <= 0) {
      this.isAlive = false;
    }
  }

  draw(ctx) {
    if (!this.isAlive) return;

    // Draw player circle
    ctx.fillStyle = this.color;
    ctx.beginPath();
    ctx.arc(this.x, this.y, 15, 0, Math.PI * 2);
    ctx.fill();

    // Draw facing direction indicator
    ctx.strokeStyle = '#fff';
    ctx.lineWidth = 2;
    ctx.beginPath();
    ctx.moveTo(this.x, this.y);
    ctx.lineTo(
      this.x + Math.cos(this.facingAngle) * 20,
      this.y + Math.sin(this.facingAngle) * 20
    );
    ctx.stroke();

    // Draw health
    for (let i = 0; i < this.health; i++) {
      ctx.fillStyle = '#fff';
      ctx.fillRect(this.x - 15 + i * 12, this.y - 25, 10, 3);
    }
  }
}
```

### Snowball object

```javascript
class Snowball {
  constructor(x, y, vx, vy, owner) {
    this.x = x;
    this.y = y;
    this.vx = vx;
    this.vy = vy;
    this.owner = owner;
    this.radius = 5;
    this.active = true;
  }

  update() {
    this.x += this.vx;
    this.y += this.vy;

    // Remove if out of bounds
    if (this.x < 0 || this.x > canvas.width ||
        this.y < 0 || this.y > canvas.height) {
      this.active = false;
    }
  }

  draw(ctx) {
    ctx.fillStyle = '#fff';
    ctx.beginPath();
    ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
    ctx.fill();

    // Add shadow for depth
    ctx.fillStyle = 'rgba(0, 0, 0, 0.2)';
    ctx.beginPath();
    ctx.arc(this.x + 2, this.y + 2, this.radius, 0, Math.PI * 2);
    ctx.fill();
  }
}
```

### Collision detection

```javascript
function checkCollisions() {
  snowballs.forEach(snowball => {
    // Check snowball vs players
    players.forEach(player => {
      if (player === snowball.owner) return; // Can't hit yourself
      if (!player.isAlive) return;

      const dx = player.x - snowball.x;
      const dy = player.y - snowball.y;
      const distance = Math.sqrt(dx * dx + dy * dy);

      if (distance < player.radius + snowball.radius) {
        player.takeDamage();
        snowball.active = false;
        playHitSound();
        createHitParticles(snowball.x, snowball.y);
      }
    });

    // Check snowball vs obstacles
    obstacles.forEach(obstacle => {
      if (circleRectCollision(snowball, obstacle)) {
        snowball.active = false;
        createSnowPuff(snowball.x, snowball.y);
      }
    });
  });

  // Remove inactive snowballs
  snowballs = snowballs.filter(s => s.active);
}

function circleRectCollision(circle, rect) {
  const closestX = Math.max(rect.x, Math.min(circle.x, rect.x + rect.width));
  const closestY = Math.max(rect.y, Math.min(circle.y, rect.y + rect.height));
  const dx = circle.x - closestX;
  const dy = circle.y - closestY;
  return (dx * dx + dy * dy) < (circle.radius * circle.radius);
}
```

---

## Features (simple → fancy)

### Basic version (1 hour build)
- 2 players on keyboard
- Simple circle graphics
- Basic snowball throwing
- Health system
- Win/lose screen

### Enhanced version (3-4 hours)
- 4 players with gamepad support
- Pixel art sprites
- Trees as obstacles
- Power-ups (faster snowballs, rapid fire, shield)
- Snow particle effects
- Background music + sound effects

### Deluxe version (weekend project)
- Tournament bracket for multiple rounds
- Different arenas (forest, playground, ice lake)
- Special moves (snowball charged shot)
- Replay system
- Local leaderboard
- Customizable player colors/skins

---

## Visual style options

### Option 1: Minimalist
```
Players: Colored circles
Snowballs: White circles
Trees: Brown/green rectangles
Background: White with light blue tint
```

### Option 2: Pixel art
```
Players: 16x16 pixel characters
Snowballs: 8x8 spinning snowball sprite
Trees: Detailed pine tree sprites
Background: Snowy ground texture
```

### Option 3: Cozy illustrated
```
Players: Cute bundled-up kids
Snowballs: Fluffy white spheres with sparkles
Trees: Hand-drawn winter trees
Background: Watercolor winter scene
```

---

## Control schemes

### 2-Player keyboard
```
Player 1:
  W/A/S/D - Move
  Space - Throw snowball
  Mouse - Aim direction

Player 2:
  Arrow keys - Move
  Enter - Throw snowball
  Mouse - Aim direction (if not using gamepad)
```

### 4-Player mixed
```
Player 1 & 2: Keyboard (as above)
Player 3 & 4: Gamepads
  Left stick - Move
  Right stick - Aim
  A button - Throw
```

### Mobile/Touch
```
Left virtual joystick - Move
Right side tap - Auto-aim & throw at nearest player
```

---

## Power-ups (optional)

**Speed Boost** ⚡
- Move 2x faster for 5 seconds
- Spawns every 20 seconds

**Rapid Fire** 🔥
- No cooldown on snowball throws
- Lasts 5 seconds

**Shield** 🛡
- Blocks next 2 hits
- Visual bubble around player

**Big Snowball** 💥
- Next snowball is 3x size, deals 2 damage
- One-time use

**Freeze** ❄️
- Freezes all other players for 2 seconds
- Rare spawn

---

## Game modes

### Classic
- Last one standing wins
- 3 health each
- First to 3 wins (best of 5 rounds)

### Time Attack
- 90 seconds
- Most hits wins
- Respawn on death

### Team Battle
- 2v2
- Shared health pool
- Coordinate attacks

### King of the Hill
- Center circle scores points
- Stay in circle = gain points
- First to 100 wins

---

## Arena designs

### Forest Arena
```
┌─────────────────────────┐
│  🌲      🌲      🌲     │
│                         │
│     🌲        🌲        │
│                         │
│  🌲      🌲      🌲     │
└─────────────────────────┘
Trees block snowballs but not players
```

### Ice Lake
```
┌─────────────────────────┐
│                         │
│   🧊  Players slide!  🧊│
│                         │
│   🧊  Less friction  🧊 │
│                         │
└─────────────────────────┘
Slippery movement physics
```

### Playground
```
┌─────────────────────────┐
│  🏠        ⛄        🏠  │
│                         │
│        🎄  🎄           │
│                         │
│  🏠                 🏠  │
└─────────────────────────┘
Mix of structures and decorations
```

---

## Particle effects

### Hit effect
```javascript
function createHitParticles(x, y) {
  for (let i = 0; i < 8; i++) {
    const angle = (Math.PI * 2 / 8) * i;
    particles.push({
      x, y,
      vx: Math.cos(angle) * 3,
      vy: Math.sin(angle) * 3,
      life: 20,
      color: '#fff'
    });
  }
}
```

### Snow puff (obstacle hit)
```javascript
function createSnowPuff(x, y) {
  for (let i = 0; i < 12; i++) {
    particles.push({
      x: x + (Math.random() - 0.5) * 10,
      y: y + (Math.random() - 0.5) * 10,
      vx: (Math.random() - 0.5) * 2,
      vy: (Math.random() - 0.5) * 2,
      life: 30,
      size: Math.random() * 3 + 2,
      color: '#e8f4ff'
    });
  }
}
```

---

## Sound design

**Sounds you need:**
- Snowball throw (whoosh)
- Snowball hit player (soft thud)
- Snowball hit tree (splat)
- Player eliminated (defeat chime)
- Victory fanfare
- Background music (upbeat winter theme)

**Free resources:**
- freesound.org
- zapsplat.com
- Generate with ElevenLabs sound effects API

---

## File structure

```
snowball-royale/
├── index.html
├── styles.css
├── game.js
├── player.js
├── snowball.js
├── particles.js
├── collision.js
├── sounds/
│   ├── throw.mp3
│   ├── hit.mp3
│   ├── splat.mp3
│   └── music.mp3
└── sprites/
    ├── player-red.png
    ├── player-blue.png
    ├── tree.png
    └── snowball.png
```

---

## Why this works

**For kids:**
- Instantly understandable (throw snowballs = fun)
- No complex rules
- Quick rounds (2-3 minutes)
- Visual feedback is satisfying

**For adults:**
- Competitive without being stressful
- Strategy (positioning, timing)
- Nostalgia (real snowball fights)
- Perfect "one more round" energy

**For mixed groups:**
- Skill ceiling low enough for casual players
- But high enough for interesting tactics
- Luck plays a small role (fair)
- Laughing together when chaos erupts

---

## Balancing tips

1. **Snowball speed** — fast enough to hit, slow enough to dodge
2. **Cooldown** — prevents button mashing, rewards timing
3. **Movement speed** — should feel responsive but not too fast
4. **Arena size** — big enough to run, small enough to not hide forever
5. **Health** — 3 hits is sweet spot (quick games, but room for comeback)

---

## Accessibility considerations

- Colorblind mode (patterns + colors for player identification)
- Visual hit indicators (screen shake + flash)
- Sound cues for all actions
- Keyboard + gamepad + touch support
- Adjustable game speed (for younger players)

---

## Common pitfalls to avoid

❌ **Snowballs too fast** — becomes impossible to dodge
❌ **No cooldown** — turns into button mashing spam
❌ **Too much health** — rounds drag on forever
❌ **Tiny arena** — nowhere to strategize
❌ **Confusing controls** — should be intuitive in 5 seconds

---

## The ritual (how to use it)

**Setup:**
- Pull it up on laptop connected to TV (big screen is best)
- Or huddle around one monitor
- 2-4 players grab controllers/keyboard positions

**Play:**
- Best of 5 rounds
- Winner stays, loser rotates (if more than 4 people)
- Trash talk is mandatory
- Dramatic announcements ("SARAH IS DOWN!")

**After:**
- Save the winner's name to the hall of fame
- Rematch demands
- "Wait, one more round, I wasn't ready"

**The magic:**
- Simple enough that Grandma can play
- Chaotic enough that everyone's screaming
- Quick enough to play between other activities
- Repeatable infinitely

---

## Cost estimate

- Hosting: Free (can run offline, no server needed)
- Assets: Free (draw yourself or use free sprites)
- Sounds: Free (freesound.org)
- Gamepad API: Built into browser

**Total: $0**

---

## Variants & mods

**Snowball Soccer**
- Put a ball in the middle
- Hit it with snowballs to score goals
- First to 3 goals wins

**Hot Potato**
- One player starts "it"
- Hit someone to pass "it"
- Timer counts down
- Don't be "it" when timer ends

**Infection Mode**
- One player is "frozen"
- If they hit you, you become frozen too
- Last unfrozen player wins

**Capture the Flag**
- Teams try to grab flag from other side
- Snowballs stun players for 3 seconds
- First team to capture 3 times wins

---

## Pro tips

1. **Add screen shake on hits** — makes impacts feel powerful
2. **Show trajectory line** — helps with aiming (optional handicap for kids)
3. **Victory animation** — winner does a little dance
4. **Slow-motion on final hit** — dramatic finish
5. **Round intro countdown** — builds anticipation ("3... 2... 1... FIGHT!")

---

## Honest take

This is **not an original game idea**. Top-down snowball fights have been done a million times.

But that's exactly why it works:
- Everyone instantly gets it
- No tutorial needed
- Pure, simple fun
- Easy to build in a weekend

The magic isn't in innovation. The magic is in:
- Building it yourself
- Customizing it for your family
- The moment when your little cousin hits your older sibling and wins
- The trash talk
- The rematch energy

**Build it simple. Play it loud. Iterate based on what makes your family laugh.**

This is a 4-hour project that'll get played for 40 hours.
