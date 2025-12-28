# 🐾 Cozy Winter Pet

A little friend for a cozy season. Take care of your winter companion and keep it happy through the holidays. Slow, gentle, and warm.

**Tech stack:** HTML + CSS + JavaScript + LocalStorage for save state + ElevenLabs Voice API (optional)

---

## How it's actually built

**Simple, realistic version:**

1. A pixel art pet on your screen with:
   - Animated idle state (breathing, blinking)
   - Mood indicator (happy / neutral / sad / sleepy)
   - Status bars (hunger, happiness, energy)

2. Three main actions:
   - **Feed** 🍪 — Increases hunger meter, plays eating animation
   - **Play** 🎾 — Increases happiness, decreases energy, plays play animation
   - **Sleep** 😴 — Restores energy, pet closes eyes and snoozes

3. Pet state changes over time:
   - Gets hungry every 30 minutes (real time)
   - Gets sleepy every 2 hours
   - Gets bored if you don't play
   - Reacts with animations and (optional) voice

4. Persistence:
   - Save state in LocalStorage
   - Pet "remembers" last interaction
   - If you return after days, pet might be sad (but forgiving)

5. Optional voice:
   - Pet "talks" using ElevenLabs API
   - Says things like "I'm hungry!" or "That was fun!"
   - Cute, gentle voice

**This is a single-page app, no backend needed (except optional voice API).**

---

## Implementation details

### Pet state machine

```javascript
const pet = {
  name: "Snowy",
  hunger: 50,      // 0-100 (0 = starving, 100 = full)
  happiness: 50,   // 0-100
  energy: 50,      // 0-100
  lastUpdate: Date.now(),
  state: 'idle'    // idle | eating | playing | sleeping
};

// State decay over time
function updatePet() {
  const now = Date.now();
  const minutesPassed = (now - pet.lastUpdate) / 1000 / 60;

  // Gradual decay
  pet.hunger = Math.max(0, pet.hunger - minutesPassed * 0.5);
  pet.happiness = Math.max(0, pet.happiness - minutesPassed * 0.3);
  pet.energy = Math.max(0, pet.energy - minutesPassed * 0.2);

  pet.lastUpdate = now;
  savePet();
  updateUI();
}

// Run every minute
setInterval(updatePet, 60000);
```

### Actions

```javascript
function feedPet() {
  if (pet.hunger >= 90) {
    speak("I'm already full!");
    return;
  }

  pet.hunger = Math.min(100, pet.hunger + 30);
  pet.happiness = Math.min(100, pet.happiness + 5);
  pet.state = 'eating';

  playAnimation('eating');
  speak("Yum! Thank you!");

  setTimeout(() => {
    pet.state = 'idle';
    savePet();
  }, 3000);
}

function playWithPet() {
  if (pet.energy < 20) {
    speak("I'm too tired... let me sleep first.");
    return;
  }

  pet.happiness = Math.min(100, pet.happiness + 25);
  pet.energy = Math.max(0, pet.energy - 20);
  pet.state = 'playing';

  playAnimation('playing');
  speak("This is fun!");

  setTimeout(() => {
    pet.state = 'idle';
    savePet();
  }, 4000);
}

function sleepPet() {
  pet.state = 'sleeping';
  pet.energy = Math.min(100, pet.energy + 40);
  pet.happiness = Math.min(100, pet.happiness + 10);

  playAnimation('sleeping');
  speak("Zzz... good night...");

  setTimeout(() => {
    pet.state = 'idle';
    speak("I feel refreshed!");
    savePet();
  }, 6000);
}
```

### Mood system

```javascript
function getMood() {
  const avg = (pet.hunger + pet.happiness + pet.energy) / 3;

  if (avg >= 70) return 'happy';
  if (avg >= 40) return 'neutral';
  if (avg >= 20) return 'sad';
  return 'critical';
}

function getMoodEmoji() {
  switch(getMood()) {
    case 'happy': return '😊';
    case 'neutral': return '😐';
    case 'sad': return '😢';
    case 'critical': return '💔';
  }
}

function getMoodMessage() {
  const mood = getMood();

  if (mood === 'happy') return "I'm so happy with you!";
  if (mood === 'neutral') return "I'm doing okay.";
  if (mood === 'sad') return "I could use some attention...";
  return "Please don't forget about me...";
}
```

---

## Visual design

### UI layout

```
┌──────────────────────────────────┐
│                                  │
│          🐾 Snowy 🐾            │
│                                  │
│      ┌──────────────┐            │
│      │              │            │
│      │   [PET ART]  │   😊       │
│      │              │            │
│      └──────────────┘            │
│                                  │
│  🍖 Hunger:  [████████░░] 80%    │
│  ❤️  Happy:   [██████████] 100%  │
│  ⚡ Energy:  [█████░░░░░] 50%    │
│                                  │
│  ┌────┐  ┌────┐  ┌────┐         │
│  │Feed│  │Play│  │Rest│         │
│  └────┘  └────┘  └────┘         │
│                                  │
│  💬 "I'm having a great day!"    │
│                                  │
│  Last visit: 5 minutes ago       │
└──────────────────────────────────┘
```

### Pixel art sprite (simple)

**Idle animation** (2 frames, alternate every 500ms):
```
Frame 1:          Frame 2:
  ∧ ∧              ∧ ∧
 (◕ ◕)           (◕ ◕)
  > <              > <
 /   \            /   \
```

**Eating animation** (food appears, pet bobs):
```
  🍪
  ∧ ∧
 (◕ ◕)
  > <
 /   \
```

**Playing animation** (pet bounces, ball bounces):
```
    ∧ ∧     🎾
   (◕ ◕)
    > <
   /   \
```

**Sleeping animation** (closed eyes, zzz):
```
  ∧ ∧   zzz
 (- -)
  > <
 /   \
```

You can draw these in:
- Aseprite (paid, best for pixel art)
- Piskel (free, browser-based)
- Or just use CSS animations with Unicode/emoji

---

## Voice implementation (ElevenLabs)

### Basic setup

```javascript
const ELEVENLABS_API_KEY = 'your_key_here';
const VOICE_ID = 'your_voice_id'; // Pick a cute, gentle voice

async function speak(text) {
  if (!enableVoice) return; // User can disable voice

  try {
    const response = await fetch(
      `https://api.elevenlabs.io/v1/text-to-speech/${VOICE_ID}`,
      {
        method: 'POST',
        headers: {
          'Accept': 'audio/mpeg',
          'Content-Type': 'application/json',
          'xi-api-key': ELEVENLABS_API_KEY
        },
        body: JSON.stringify({
          text: text,
          model_id: 'eleven_monolingual_v1',
          voice_settings: {
            stability: 0.5,
            similarity_boost: 0.5
          }
        })
      }
    );

    if (response.ok) {
      const audioBlob = await response.blob();
      const audioUrl = URL.createObjectURL(audioBlob);
      const audio = new Audio(audioUrl);
      audio.play();

      // Show text bubble while speaking
      showSpeechBubble(text);
    }
  } catch (error) {
    console.error('Voice failed:', error);
    // Fallback to text-only
    showSpeechBubble(text);
  }
}

function showSpeechBubble(text) {
  const bubble = document.getElementById('speech-bubble');
  bubble.textContent = text;
  bubble.classList.add('visible');

  setTimeout(() => {
    bubble.classList.remove('visible');
  }, 3000);
}
```

### Voice personality phrases

```javascript
const voiceLines = {
  greeting: [
    "Oh! You're back! I missed you!",
    "Hi! I've been waiting for you!",
    "Welcome back! How was your day?"
  ],
  hungry: [
    "My tummy is rumbling...",
    "I'm getting hungry!",
    "Could we have a snack?",
    "Food time, maybe?"
  ],
  happy: [
    "I'm so happy right now!",
    "This is the best!",
    "You're the best friend ever!",
    "Life is good!"
  ],
  sleepy: [
    "I'm getting sleepy...",
    "Can we rest for a bit?",
    "My eyes are heavy...",
    "Nap time?"
  ],
  playful: [
    "Let's play!",
    "I have so much energy!",
    "Can we do something fun?",
    "Playtime! Playtime!"
  ],
  eating: [
    "Nom nom nom!",
    "This is delicious!",
    "Yummy!",
    "Thank you for the food!"
  ],
  afterPlay: [
    "That was so much fun!",
    "Let's do it again!",
    "I love playing with you!",
    "Best game ever!"
  ],
  wakeUp: [
    "Good morning!",
    "I feel refreshed!",
    "That was a nice nap!",
    "I'm ready for the day!"
  ]
};

function getRandomLine(category) {
  const lines = voiceLines[category];
  return lines[Math.floor(Math.random() * lines.length)];
}
```

### Voice timing

```javascript
// Pet speaks proactively based on state
setInterval(() => {
  if (pet.hunger < 30 && pet.state === 'idle') {
    speak(getRandomLine('hungry'));
  } else if (pet.energy < 20 && pet.state === 'idle') {
    speak(getRandomLine('sleepy'));
  } else if (pet.happiness > 80 && Math.random() > 0.9) {
    speak(getRandomLine('happy'));
  }
}, 120000); // Check every 2 minutes
```

---

## Persistence (LocalStorage)

```javascript
function savePet() {
  localStorage.setItem('cozyPet', JSON.stringify({
    name: pet.name,
    hunger: pet.hunger,
    happiness: pet.happiness,
    energy: pet.energy,
    lastUpdate: pet.lastUpdate,
    state: pet.state
  }));
}

function loadPet() {
  const saved = localStorage.getItem('cozyPet');

  if (saved) {
    const data = JSON.parse(saved);
    Object.assign(pet, data);

    // Handle time away
    const hoursAway = (Date.now() - pet.lastUpdate) / 1000 / 60 / 60;

    if (hoursAway > 24) {
      speak("You were gone for so long! I missed you!");
      pet.happiness = Math.max(20, pet.happiness - 30);
    } else if (hoursAway > 6) {
      speak("Welcome back! I was getting worried!");
    } else {
      speak(getRandomLine('greeting'));
    }

    updatePet(); // Apply time-based decay
  } else {
    // First time meeting
    speak("Hi! I'm Snowy! Nice to meet you!");
  }
}

// Load on page load
window.addEventListener('load', loadPet);
```

---

## Features (simple → fancy)

### Basic version (2-3 hours)
- Simple pixel art pet
- 3 actions (feed, play, sleep)
- Basic stats
- LocalStorage save
- Text-only responses

### Enhanced version (6-8 hours)
- Multiple animation states
- ElevenLabs voice integration
- Personality phrases
- Seasonal variations (Christmas hat, New Year's decorations)
- Mini-games (pet catches snowflakes)

### Deluxe version (weekend project)
- Multiple pets to choose from
- Unlockable accessories
- Weather reactions (gets excited if it snows IRL)
- Calendar system (remembers holidays)
- Photo mode (screenshot your pet)
- Growth system (pet grows over weeks)

---

## Seasonal variations

### Christmas mode
- Pet wears Santa hat
- Background has Christmas tree
- Special food: Gingerbread cookies
- Voice: "Merry Christmas!"

### New Year mode
- Party hat
- Confetti background
- Champagne glass (sparkling juice for pet)
- Voice: "Happy New Year! Let's make it great!"

### Winter general
- Snowflakes falling in background
- Pet has scarf
- Hot cocoa as food option
- Cozy fireplace in background

---

## Mini-games (optional extras)

### Snowflake Catch
```javascript
// Pet tries to catch falling snowflakes
// Click snowflakes before they hit ground
// Each catch increases happiness
```

### Fetch
```javascript
// Throw a ball across screen
// Pet runs to get it and brings it back
// Increases happiness, decreases energy
```

### Hide & Seek
```javascript
// Pet hides behind objects
// Find and click within time limit
// Cute "You found me!" reaction
```

---

## Why this works

**Psychologically:**
- Low stakes (pet doesn't "die" if neglected)
- Positive reinforcement (always happy to see you)
- Ritual building (check in daily)
- Gentle responsibility without pressure

**Emotionally:**
- Cute and comforting
- Non-judgmental companion
- Always available
- Simple joy

**Practically:**
- Takes 2 minutes a day
- No guilt if you forget
- Runs in browser tab
- Works offline (except voice)

---

## Design principles

**Keep it gentle:**
- No death mechanic (pet just gets sad, never gone)
- No urgent notifications (gentle reminders only)
- Slow decay (hours, not minutes)
- Forgiving (always happy when you return)

**Keep it simple:**
- 3 core actions, that's it
- No complex systems
- No grinding or optimization
- Just vibes

**Keep it cozy:**
- Warm color palette
- Soft animations
- Gentle voice
- Calm music (optional)

---

## Accessibility

- High contrast mode
- Larger text option
- Keyboard controls (Tab + Enter)
- Screen reader support
- No time pressure
- Voice can be disabled

---

## Cost estimate

**Basic version:** $0
- LocalStorage: Free
- Hosting: Free (Netlify/Vercel)

**With voice:**
- ElevenLabs: $5/month gets you 30,000 characters
- If pet speaks ~50 words/day = ~250 chars/day
- That's 120 days of daily play
- Basically: $5 for the whole holiday season

**Total: $0-5 depending on voice**

---

## File structure

```
cozy-winter-pet/
├── index.html
├── styles.css
├── pet.js
├── voice.js
├── sprites/
│   ├── idle.png
│   ├── eating.png
│   ├── playing.png
│   └── sleeping.png
├── sounds/
│   ├── eat.mp3
│   ├── play.mp3
│   └── sleep.mp3
└── backgrounds/
    └── winter-room.png
```

---

## Common pitfalls to avoid

❌ **Too needy** — pet shouldn't constantly demand attention
❌ **Guilt-tripping** — no "I'm dying!" messages
❌ **Too many stats** — keep it simple (3 is enough)
❌ **Annoying voice** — pick a genuinely pleasant voice
❌ **Busy UI** — calm, spacious design

---

## The ritual (how to use it)

**Morning:**
- Open tab
- Check pet's status
- Feed if hungry
- Quick play session (1 min)

**During day:**
- Pet lives in a pinned browser tab
- Occasionally says something cute
- You smile and move on

**Evening:**
- Check in before bed
- Make sure pet is content
- Put pet to sleep
- Feel cozy

**The magic:**
- It's there when you need something gentle
- Never demands, just exists
- Small daily ritual
- Unexpectedly comforting during stressful times

---

## Pro tips

1. **Subtle animations** — slow, breathing-like movements
2. **Ambient sounds** — soft background (crackling fire, wind)
3. **Seasonal updates** — keeps it fresh across holidays
4. **Hidden interactions** — clicking on pet makes it giggle
5. **Memory system** — pet remembers conversations (LocalStorage)

---

## Honest take

This is **not a game**. This is a **digital companion**.

Like a plant, but:
- Responds to you
- Talks (optionally)
- Never dies (just gets sad)
- Exists in a tab

Why it matters:
- The holidays can be lonely even in a crowd
- Sometimes you need something low-stakes to care for
- A small routine brings comfort
- It's an excuse to take a 2-minute break

**This is a $0 project that provides $1000 worth of gentle vibes.**

Build it in an afternoon. Customize the personality. Let it live in your browser all winter. See if it makes you smile.

The tech is simple. The emotional value is real.
