# 🎁 Present Panic!

Too many gifts. Not enough time. Catch the right presents before Christmas chaos takes over. Fast, festive, and surprisingly stressful.

**Tech stack:** HTML Canvas + JavaScript

---

## How it's actually built

**Simple, realistic version:**

1. Screen layout:
   - Presents fall from top of screen
   - Player controls a basket at the bottom
   - Move left/right to catch

2. Present types:
   - **Good presents** 🎁 (wrapped nicely) — +10 points
   - **Bad presents** 💣 (bombs, coal, broken boxes) — -20 points or lose life
   - **Bonus presents** ⭐ (golden, rare) — +50 points

3. Game mechanics:
   - Falling speed increases over time
   - More presents spawn as game progresses
   - Combo system (catch 5 good ones in a row = bonus)
   - 3 lives (lose when you catch 3 bad ones)

4. Controls:
   - **Desktop:** Arrow keys or mouse
   - **Mobile:** Touch/swipe
   - **Gamepad:** D-pad or analog stick

5. End condition:
   - Lost all 3 lives
   - Time runs out (optional: 90 second mode)
   - High score tracked

**Can be built in one HTML file with Canvas API.**

---

## Implementation details

### Game loop structure

```javascript
const game = {
  score: 0,
  lives: 3,
  level: 1,
  combo: 0,
  presents: [],
  basket: { x: 400, y: 550, width: 80, height: 40 },
  gameState: 'playing' // playing | paused | gameOver
};

function gameLoop() {
  if (game.gameState !== 'playing') return;

  update();
  render();
  requestAnimationFrame(gameLoop);
}

function update() {
  spawnPresents();
  updatePresents();
  checkCollisions();
  updateDifficulty();
}

function render() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  drawBackground();
  drawPresents();
  drawBasket();
  drawUI();
}
```

### Present object

```javascript
class Present {
  constructor(x, type) {
    this.x = x;
    this.y = -50; // Start above screen
    this.width = 40;
    this.height = 40;
    this.type = type; // 'good' | 'bad' | 'bonus'
    this.speed = 2 + (game.level * 0.3); // Speed increases with level
    this.active = true;
  }

  update() {
    this.y += this.speed;

    // Remove if off screen
    if (this.y > canvas.height) {
      this.active = false;

      // Missed a good present = combo broken
      if (this.type === 'good') {
        game.combo = 0;
      }
    }
  }

  draw(ctx) {
    // Draw based on type
    switch(this.type) {
      case 'good':
        ctx.fillStyle = '#e74c3c'; // Red present
        ctx.fillRect(this.x, this.y, this.width, this.height);
        // Ribbon
        ctx.fillStyle = '#f39c12';
        ctx.fillRect(this.x + 15, this.y, 10, this.height);
        ctx.fillRect(this.x, this.y + 15, this.width, 10);
        break;

      case 'bad':
        ctx.fillStyle = '#2c3e50'; // Dark/coal
        ctx.fillRect(this.x, this.y, this.width, this.height);
        // Draw X or danger symbol
        ctx.strokeStyle = '#e74c3c';
        ctx.lineWidth = 3;
        ctx.beginPath();
        ctx.moveTo(this.x + 10, this.y + 10);
        ctx.lineTo(this.x + 30, this.y + 30);
        ctx.moveTo(this.x + 30, this.y + 10);
        ctx.lineTo(this.x + 10, this.y + 30);
        ctx.stroke();
        break;

      case 'bonus':
        // Golden star present
        ctx.fillStyle = '#f1c40f';
        ctx.beginPath();
        ctx.arc(this.x + 20, this.y + 20, 20, 0, Math.PI * 2);
        ctx.fill();
        // Star
        drawStar(ctx, this.x + 20, this.y + 20, 5, 15, 7);
        break;
    }
  }
}
```

### Spawning system

```javascript
let spawnTimer = 0;
const spawnInterval = 60; // frames between spawns

function spawnPresents() {
  spawnTimer++;

  if (spawnTimer >= spawnInterval - (game.level * 5)) {
    spawnTimer = 0;

    // Random position
    const x = Math.random() * (canvas.width - 40);

    // Weighted random type
    const rand = Math.random();
    let type;

    if (rand < 0.7) {
      type = 'good';
    } else if (rand < 0.95) {
      type = 'bad';
    } else {
      type = 'bonus';
    }

    game.presents.push(new Present(x, type));
  }

  // Remove inactive presents
  game.presents = game.presents.filter(p => p.active);
}
```

### Collision detection

```javascript
function checkCollisions() {
  game.presents.forEach(present => {
    if (!present.active) return;

    // Check if present overlaps with basket
    if (present.y + present.height >= game.basket.y &&
        present.y <= game.basket.y + game.basket.height &&
        present.x + present.width >= game.basket.x &&
        present.x <= game.basket.x + game.basket.width) {

      catchPresent(present);
      present.active = false;
    }
  });
}

function catchPresent(present) {
  switch(present.type) {
    case 'good':
      game.score += 10;
      game.combo++;
      playSound('catch');
      createParticles(present.x, present.y, '#2ecc71');

      // Combo bonus
      if (game.combo >= 5) {
        game.score += game.combo * 2;
        showComboText(game.combo);
      }
      break;

    case 'bad':
      game.lives--;
      game.combo = 0;
      playSound('bad');
      shakeScreen();
      createParticles(present.x, present.y, '#e74c3c');

      if (game.lives <= 0) {
        gameOver();
      }
      break;

    case 'bonus':
      game.score += 50;
      playSound('bonus');
      createParticles(present.x, present.y, '#f1c40f', 20);
      showBonusText('+50!');
      break;
  }
}
```

### Basket controls

```javascript
// Mouse control
canvas.addEventListener('mousemove', (e) => {
  const rect = canvas.getBoundingClientRect();
  game.basket.x = e.clientX - rect.left - game.basket.width / 2;

  // Keep in bounds
  game.basket.x = Math.max(0, Math.min(canvas.width - game.basket.width, game.basket.x));
});

// Keyboard control
const keys = {};
window.addEventListener('keydown', (e) => keys[e.key] = true);
window.addEventListener('keyup', (e) => keys[e.key] = false);

function updateBasket() {
  const speed = 8;

  if (keys['ArrowLeft']) {
    game.basket.x -= speed;
  }
  if (keys['ArrowRight']) {
    game.basket.x += speed;
  }

  // Keep in bounds
  game.basket.x = Math.max(0, Math.min(canvas.width - game.basket.width, game.basket.x));
}

// Touch control
let touchStartX = 0;
canvas.addEventListener('touchstart', (e) => {
  touchStartX = e.touches[0].clientX;
});

canvas.addEventListener('touchmove', (e) => {
  e.preventDefault();
  const touchX = e.touches[0].clientX;
  const deltaX = touchX - touchStartX;

  game.basket.x += deltaX * 0.5;
  game.basket.x = Math.max(0, Math.min(canvas.width - game.basket.width, game.basket.x));

  touchStartX = touchX;
});
```

### Difficulty progression

```javascript
function updateDifficulty() {
  // Level up every 200 points
  const newLevel = Math.floor(game.score / 200) + 1;

  if (newLevel > game.level) {
    game.level = newLevel;
    showLevelUpText();
    playSound('levelup');
  }
}

// As level increases:
// - Present fall speed increases
// - More bad presents spawn
// - Spawn rate increases
```

---

## Visual design

### Screen layout

```
┌────────────────────────────────┐
│  Score: 1250    Lives: ❤️❤️❤️  │
│  Level: 3       Combo: x5      │
├────────────────────────────────┤
│                                │
│    🎁   💣      ⭐             │
│                                │
│        🎁          💣          │
│                                │
│            🎁                  │
│                                │
│                                │
│          🎁      🎁            │
│                                │
│                                │
│           [Basket]             │
└────────────────────────────────┘
```

### Art style options

**Option 1: Pixel art**
- 16x16 pixel presents
- Retro aesthetic
- Chunky pixels
- Limited color palette

**Option 2: Flat design**
- Simple geometric shapes
- Bright colors
- Clean ribbons
- Modern look

**Option 3: Hand-drawn**
- Sketchy style
- Wobbly lines
- Warm, cozy feel
- Unique character

---

## Features (simple → fancy)

### Basic version (2-3 hours)
- Simple falling mechanics
- 2 present types (good/bad)
- Basic scoring
- Mouse controls
- Game over screen

### Enhanced version (5-6 hours)
- Multiple present types
- Combo system
- Power-ups (slow time, magnet, shield)
- Particle effects
- Sound effects
- Difficulty progression
- Leaderboard

### Deluxe version (weekend)
- Multiple game modes
- Achievements
- Holiday themes
- Custom background music
- Multiplayer (split screen)
- Daily challenges

---

## Game modes

### Classic
- Endless mode
- 3 lives
- Increasing difficulty
- High score tracking

### Time Attack
- 90 seconds
- Unlimited lives
- As many points as possible
- Speed bonus for fast catches

### Survival
- No lives
- One bad present = game over
- Pure precision mode
- Tension builds

### Chaos Mode
- Everything falls super fast
- Tons of presents on screen
- Madness

---

## Power-ups (optional)

**Slow Motion** ⏱
- All presents fall at 50% speed for 5 seconds
- Spawns rarely

**Magnet** 🧲
- Good presents attracted to basket automatically
- Bad presents still hurt
- Lasts 8 seconds

**Shield** 🛡
- Blocks next bad present
- Visual shield around basket
- One-time use

**Double Points** 2️⃣
- All points x2 for 10 seconds
- Stack combos for huge scores

**Santa's Helper** 🎅
- Auto-catch mode
- Basket follows presents perfectly for 5 seconds
- Rare spawn

---

## Particle effects

### Catch effect
```javascript
function createParticles(x, y, color, count = 10) {
  for (let i = 0; i < count; i++) {
    particles.push({
      x: x + 20,
      y: y + 20,
      vx: (Math.random() - 0.5) * 6,
      vy: (Math.random() - 0.5) * 6,
      life: 30,
      color: color,
      size: Math.random() * 4 + 2
    });
  }
}

function updateParticles() {
  particles.forEach(p => {
    p.x += p.vx;
    p.y += p.vy;
    p.life--;
    p.size *= 0.95;
  });

  particles = particles.filter(p => p.life > 0);
}
```

### Screen shake
```javascript
let shakeAmount = 0;

function shakeScreen() {
  shakeAmount = 10;
}

function updateShake() {
  if (shakeAmount > 0) {
    ctx.save();
    ctx.translate(
      (Math.random() - 0.5) * shakeAmount,
      (Math.random() - 0.5) * shakeAmount
    );
    shakeAmount *= 0.9;
  }
}

// In render, call ctx.restore() at end
```

---

## Sound design

**Sounds needed:**
- `catch.mp3` — Satisfying pop when catching good present
- `bad.mp3` — Crash/explosion for bad present
- `bonus.mp3` — Sparkly chime for bonus
- `levelup.mp3` — Uplifting jingle
- `gameover.mp3` — Sad trombone
- `bgm.mp3` — Upbeat holiday music loop

**Free sources:**
- freesound.org
- zapsplat.com
- Generate with AI (ElevenLabs has sound effects API)

---

## Combo system details

```javascript
const comboMultipliers = {
  5: 'Nice!',
  10: 'Great!',
  15: 'Amazing!',
  20: 'Incredible!',
  25: 'UNSTOPPABLE!'
};

function showComboText(combo) {
  if (comboMultipliers[combo]) {
    displayFloatingText(comboMultipliers[combo], canvas.width / 2, 100);
  }
}

function displayFloatingText(text, x, y) {
  floatingTexts.push({
    text: text,
    x: x,
    y: y,
    life: 60,
    fontSize: 32,
    alpha: 1
  });
}

function updateFloatingTexts() {
  floatingTexts.forEach(t => {
    t.y -= 2;
    t.life--;
    t.alpha = t.life / 60;
  });

  floatingTexts = floatingTexts.filter(t => t.life > 0);
}
```

---

## Leaderboard (LocalStorage)

```javascript
function saveScore(score) {
  const scores = JSON.parse(localStorage.getItem('presentPanicScores') || '[]');

  scores.push({
    score: score,
    date: new Date().toISOString(),
    level: game.level
  });

  // Keep top 10
  scores.sort((a, b) => b.score - a.score);
  scores.splice(10);

  localStorage.setItem('presentPanicScores', JSON.stringify(scores));
}

function displayLeaderboard() {
  const scores = JSON.parse(localStorage.getItem('presentPanicScores') || '[]');

  // Render leaderboard UI
  // Show rank, score, level, date
}
```

---

## Why this works

**Psychologically:**
- Instant feedback loop (catch = points immediately)
- Simple mechanic, hard to master
- "Just one more try" energy
- Risk/reward decision-making

**Mechanically:**
- Easy to learn (move basket, catch good things)
- Controls are responsive
- Difficulty ramps naturally
- Clear win condition (high score)

**Emotionally:**
- Festive theme = holiday mood
- Satisfying catch sound
- Panic = adrenaline = fun
- Combo system = skill expression

---

## Accessibility

- Adjustable game speed in settings
- High contrast mode
- Sound toggle
- Keyboard, mouse, touch, and gamepad support
- Colorblind-friendly indicators (shapes + colors)
- Pause functionality

---

## Common pitfalls to avoid

❌ **Too fast too soon** — difficulty should ramp gradually
❌ **Unfair spawns** — don't spawn presents directly above basket
❌ **Laggy controls** — basket movement must feel instant
❌ **Unclear visuals** — good vs bad should be obvious
❌ **No feedback** — add juice (particles, shake, sound)

---

## The ritual (how to use it)

**Quick play:**
- Open during a break
- "Let me beat my high score"
- 2-3 minute sessions
- Competitive with family members

**Party mode:**
- Project on TV
- Take turns
- Highest score wins
- Cheer/groan together

**Challenge mode:**
- Daily high score competition
- Screenshot your score
- Family tournament bracket

**The magic:**
- Simple enough for anyone
- Competitive enough for bragging rights
- Short sessions = easy to fit in
- Holiday theme = seasonal fun

---

## Cost estimate

- Hosting: Free (runs in browser, no server)
- Assets: Free (draw or use free sprites)
- Sounds: Free (freesound.org)
- Total: $0

---

## Pro tips

1. **Juice is everything** — add screen shake, particles, sound for every action
2. **Responsive controls** — basket should feel like an extension of your hand
3. **Visual feedback** — show combo counter growing, flash on catch
4. **Difficulty curve** — increase speed slowly, not suddenly
5. **Mobile-first** — touch controls should feel as good as mouse

---

## File structure

```
present-panic/
├── index.html
├── styles.css
├── game.js
├── present.js
├── particles.js
├── leaderboard.js
├── sounds/
│   ├── catch.mp3
│   ├── bad.mp3
│   ├── bonus.mp3
│   └── bgm.mp3
└── sprites/
    ├── basket.png
    ├── present-good.png
    ├── present-bad.png
    └── present-bonus.png
```

---

## Variants & mods

**Cookie Catch**
- Catch cookies instead of presents
- Milk as power-up
- Avoid burnt cookies

**Snowflake Collector**
- Catch snowflakes
- Avoid ice chunks
- Winter theme

**Easter Eggs**
- Seasonal variation for spring
- Catch eggs, avoid rotten ones

**Space Debris**
- Sci-fi theme
- Catch asteroids
- Avoid space junk

---

## Honest take

This is a **one-mechanic game**. Move left/right, catch things. That's it.

But it works because:
- The mechanic is satisfying
- The difficulty progression keeps you engaged
- The combo system rewards skill
- The festive theme adds charm

**This is a 3-hour build that people will play for 30 minutes total across the holidays.**

And that's perfect.

Not every project needs to be deep. Sometimes you just need:
- A way to kill 5 minutes
- A high score to beat
- An excuse to feel festive
- Something to compete over with siblings

Build it fast. Add juice. Make it feel good to play. Done.

**The goal is not complexity. The goal is one perfect mechanic executed well.**
