# 🎭 Two Truths & a Lie (Digital Host)

You think you know them. Enter three stories — two real, one fake. Can the others spot the lie? A simple game that reveals surprising memories, hidden talents, and stories you've never heard before.

**Tech stack:** Simple web UI (Vanilla JS or React) + LocalStorage for scores + Optional Supabase for multi-device

---

## How it's actually built

**Simple, realistic version:**

1. One player enters:
   - Their name
   - Three statements (2 truths + 1 lie)

2. App:
   - Shuffles the statements (so order doesn't give it away)
   - Displays to the group (on shared screen or everyone's phone)

3. Everyone votes:
   - Click the statement they think is the lie
   - Votes are hidden until everyone chooses

4. Reveal:
   - Show which was the lie
   - Highlight who guessed correctly
   - Update scores automatically

5. Next player's turn

**You remove:**
- Rule explaining
- Score arguing
- "Wait, whose turn is it?"

---

## Implementation details

### Core flow

```
1. Player 1 enters their statements
2. App shows:
   Statement A: "I once met a celebrity at a gas station"
   Statement B: "I can solve a Rubik's cube in under 2 minutes"
   Statement C: "I've been to 12 countries"
3. Each person clicks their guess
4. When all votes are in, reveal:
   ✓ True: Statement A
   ✓ True: Statement C
   ✗ LIE: Statement B (gotcha!)
5. Show who guessed right:
   - Mom ✓
   - Dad ✓
   - Sarah ✗
6. Update scoreboard
7. Next player
```

### Game logic

```javascript
// Store one round
let currentRound = {
  player: "Sarah",
  statements: [
    { text: "I met a celebrity", isLie: false },
    { text: "I solve Rubik's cubes fast", isLie: true },
    { text: "I've been to 12 countries", isLie: false }
  ],
  votes: []
};

function submitVote(voter, statementIndex) {
  currentRound.votes.push({ voter, guess: statementIndex });
}

function revealResults() {
  const lieIndex = currentRound.statements.findIndex(s => s.isLie);

  currentRound.votes.forEach(vote => {
    if (vote.guess === lieIndex) {
      addPoint(vote.voter); // They guessed correctly
    }
  });

  return {
    lie: currentRound.statements[lieIndex].text,
    correctGuessers: currentRound.votes
      .filter(v => v.guess === lieIndex)
      .map(v => v.voter)
  };
}
```

---

## Features (simple → fancy)

### Basic version
- One device, pass it around
- Manual turn order
- Simple point tally
- LocalStorage scores

### Enhanced version
- Multi-device support (everyone votes on their phone)
- Automatic turn rotation
- Timer per round (30 seconds to vote)
- Game history ("Remember when Dad said he climbed a mountain?")

### Deluxe version
- Photo upload per statement (proves the truth after reveal)
- AI lie detector (analyzes statement patterns, shows "confidence meter")
- Themed rounds ("Childhood memories" / "Travel stories" / "Skills")
- Hall of fame (best lies, most fooled, etc.)

---

## UI design

### Player input screen
```
┌─────────────────────────────────────┐
│  Your turn, Sarah! 🎭               │
├─────────────────────────────────────┤
│  Enter 2 truths and 1 lie:          │
│                                     │
│  1. [ I met a celebrity at a...   ] │
│                                     │
│  2. [ I can solve a Rubik's cube  ] │
│                                     │
│  3. [ I've been to 12 countries   ] │
│                                     │
│         [Submit]                    │
└─────────────────────────────────────┘
```

### Voting screen
```
┌─────────────────────────────────────┐
│  Sarah's statements:                │
├─────────────────────────────────────┤
│  Which one is the LIE?              │
│                                     │
│  ○ I met a celebrity at a gas       │
│    station in 2019                  │
│                                     │
│  ○ I can solve a Rubik's cube in    │
│    under 2 minutes                  │
│                                     │
│  ○ I've been to 12 countries        │
│                                     │
│  Waiting for: Mom, Dad              │
└─────────────────────────────────────┘
```

### Reveal screen
```
┌─────────────────────────────────────┐
│  The LIE was... 🥁                  │
├─────────────────────────────────────┤
│                                     │
│  ✗ "I can solve a Rubik's cube      │
│     in under 2 minutes"             │
│                                     │
│  Sarah: "Okay I tried once and      │
│  gave up after 20 minutes 😅"       │
│                                     │
│  Who guessed correctly:             │
│  ✓ Mom                              │
│  ✓ Dad                              │
│  ✗ Grandma                          │
│                                     │
│  [Next Player]                      │
└─────────────────────────────────────┘
```

---

## Tech implementation

### Option 1: Single device (simplest)

```javascript
// All players share one device
let players = ["Mom", "Dad", "Sarah", "Grandma"];
let scores = { Mom: 0, Dad: 0, Sarah: 0, Grandma: 0 };
let currentPlayerIndex = 0;

function nextTurn() {
  currentPlayerIndex = (currentPlayerIndex + 1) % players.length;
  return players[currentPlayerIndex];
}

// Store in LocalStorage
localStorage.setItem('gameState', JSON.stringify({ players, scores }));
```

### Option 2: Multi-device (sync via Supabase)

```javascript
// Create a game session
const gameId = generateGameCode(); // e.g., "XJ4K"

// Players join with code
supabase
  .from('games')
  .insert({
    id: gameId,
    players: ["Mom", "Dad", "Sarah"],
    current_round: {...}
  });

// Real-time voting
supabase
  .from('votes')
  .on('INSERT', (payload) => {
    updateVoteCount(payload.new);
  })
  .subscribe();
```

### File structure

```
two-truths-and-a-lie/
├── index.html
├── styles.css
├── game.js
├── components/
│   ├── player-input.js
│   ├── voting-screen.js
│   └── reveal-screen.js
└── utils/
    ├── scoring.js
    └── storage.js
```

---

## Why this works

**Psychologically:**
- Reveals unexpected stories (you learn new things about family)
- Creates intimacy (sharing personal truths)
- Generates laughter (the lie reveal is always funny)
- No pressure (everyone takes a turn, no one "loses")

**Socially:**
- Works for any group size (3–10 people)
- Mixed ages (kids can play if statements are simple)
- Breaks ice (perfect for extended family who don't see each other often)
- Creates stories ("Remember when Uncle Bob said he wrestled a bear?")

**Mechanically:**
- Simple rules (even Grandma gets it immediately)
- Fast rounds (5 minutes per person)
- Natural conversation starter (after reveal, discussion flows)
- Scales infinitely (can play for 30 minutes or 3 hours)

---

## Variations

### 1. **Themed rounds**
- "Childhood memories"
- "Work stories"
- "Food experiences"
- "Skills and talents"
- "Travel adventures"

### 2. **Photo proof mode**
- After reveal, liar must show evidence for the truths
- Creates a mini storytelling moment

### 3. **Point variations**
- Bonus points for fooling everyone
- Double points if you guess a hard lie
- Penalty points for the most obvious lie

### 4. **Team mode**
- Split into teams
- Each team collectively guesses
- Most points wins

### 5. **AI-enhanced version**
- After voting, AI analyzes all statements
- Shows "lie probability" for each
- Players can choose to trust AI or their gut
- See who's better: humans or AI

---

## Pro tips

1. **Set a statement length limit** (100 characters) — keeps it snappy
2. **Read statements out loud** — helps everyone absorb them
3. **Allow discussion before voting** — "Wait, when did you go to Japan??"
4. **Make the liar explain** — after reveal, they tell the story behind the truths
5. **Save the best lies** — at end of game, vote on "Most Convincing Lie"

---

## Common pitfalls to avoid

❌ **Don't let players edit after submitting** — they'll second-guess forever
❌ **Don't show votes before everyone's in** — creates peer pressure
❌ **Don't make it competitive** — focus on stories, not winning
❌ **Don't allow vague statements** — "I like pizza" (too easy)
❌ **Don't rush the reveal** — let the tension build

---

## The ritual (how to use it)

**Before you start:**
- Gather everyone in one room
- Explain the rules (takes 30 seconds)
- Decide turn order
- Set a round limit (e.g., everyone goes once)

**During the game:**
- When it's your turn, take 2 minutes to think
- Submit your statements
- Everyone else votes (no discussion yet)
- After all votes are in, reveal
- Storytelling moment: explain the truths
- Move to next player

**After the game:**
- Award "Best Liar"
- Award "Best Detective"
- Save the game history
- Plan to play again next year

**The magic happens when:**
- Someone reveals a truth that surprises everyone
- A lie is so good, everyone falls for it
- You learn something about someone you thought you knew

---

## Accessibility considerations

- Large, readable text
- Color-blind friendly (don't rely only on red/green)
- Keyboard navigation
- Screen reader support ("Statement 1 of 3: I met a celebrity")
- No timer pressure by default

---

## Cost estimate

**Single-device version:** $0 (runs in browser, no backend)
**Multi-device version:**
- Supabase free tier (plenty for family use)
- Hosting: Free (Netlify/Vercel)

Total: Basically free

---

## Honest take

This game has existed forever. People play it at parties, team retreats, family dinners.

The digital version just:
- Removes the manual scorekeeping
- Shuffles statements (prevents "third one is always the lie" patterns)
- Tracks votes secretly (no bandwagoning)
- Creates a game history you can look back on

But the **real value** is not the app. It's:
- The moment when Dad reveals a story from college no one knew
- The laughter when someone's "lie" is so absurd it's obviously true
- The quiet bonding when someone shares something vulnerable
- The tradition you build around it

Build it simple. Keep it focused. Let the stories be the star.

This is not a product. This is a **conversation starter in app form**.
