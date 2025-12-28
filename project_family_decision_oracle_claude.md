# 🧠 Family Decision Oracle

The spirits have spoken. When family debates go nowhere, summon the Oracle. Cast your votes in secret and receive a final verdict — delivered with ancient wisdom, unnecessary drama, and zero accountability. No arguments. Only destiny.

**Tech stack:** Simple web UI (HTML/CSS/JS or React) → Supabase for votes → ChatGPT API for persona → Optional sound effects

---

## How it's actually built

**Simple, realistic version:**

1. Single screen with:
   - Question input ("Which movie should we watch?")
   - Option inputs ("The Godfather" / "Elf" / "Die Hard")
   - Voting buttons for each family member

2. Everyone votes privately:
   - Can use same laptop (click your choice, next person)
   - Or everyone votes on their phone
   - Votes are hidden until all are in

3. When voting ends, hit "Consult the Oracle"

4. AI persona generates:
   - Final decision (picks based on votes + randomness)
   - Dramatic explanation in character
   - Mystical lore about why this choice is destiny
   - Optional: "consequences" for those who doubted

5. Result is read aloud like a prophecy

**Key trick: blame is outsourced to the Oracle. No one can argue with mystical forces.**

---

## Implementation details

### Core flow

```
1. Host creates question: "Where should we eat dinner?"
2. Adds options: "Italian" / "Sushi" / "Pizza" / "Thai"
3. Share link or pass device around
4. Each person secretly votes
5. Hit "Summon Oracle"
6. Wait 5 seconds (dramatic music/animation)
7. Oracle delivers verdict in full character
```

### AI prompt structure

```
You are the Family Decision Oracle, an ancient mystical entity
summoned to resolve mortal disputes.

Question: {question}
Options: {options}
Votes: {vote_breakdown}

Deliver your verdict in this format:
1. Dramatic opening ("The threads of fate have been woven...")
2. The chosen answer (can be majority vote or surprise twist)
3. Mystical reasoning (weave in elements of the options)
4. A warning or blessing for the family
5. Closing proclamation ("So it is written, so it shall be")

Tone: Pompous, mystical, slightly theatrical, gently funny
Length: 4-6 sentences
```

### Example output

```
The Oracle has consulted the celestial patterns...

*After much deliberation with the spirits of indecision past*

THE VERDICT: Sushi

The stars aligned when three souls whispered "sushi" into the void,
while the lone pizza advocate's energy was admirable but ultimately
drowned by the tide of cosmic consensus. The Oracle decrees that
raw fish shall grace your table this eve, for Mercury is in
retrograde and only sushi can balance the energies. Those who
doubted shall order extra edamame as penance.

So it is written. So it shall be. 🔮
```

---

## Tech choices

### Option 1: Single-device (simplest)
- One laptop/tablet in the middle of table
- Each person taps their choice
- Vote is recorded but not shown
- Works with zero setup

```javascript
// Literally this simple:
let votes = [];

function vote(person, choice) {
  votes.push({ person, choice, timestamp: Date.now() });
  showNextVoterScreen();
}

function summonOracle() {
  const breakdown = countVotes(votes);
  callOpenAI(breakdown).then(displayVerdict);
}
```

### Option 2: Multi-device (fancier)
- Host creates "session"
- Generates 4-digit code
- Everyone joins on their phone
- Votes sync in real-time
- Needs Supabase or similar

### Tech stack breakdown
- **Frontend:** Plain HTML/CSS/JS (or React if you want)
- **Styling:** Dark theme, mystical vibes, candles/smoke animations
- **Backend:** Supabase for vote storage (if multi-device)
- **AI:** ChatGPT API with system prompt for Oracle persona
- **Audio:** Free mystical sound effects (optional, from freesound.org)

### Folder structure
```
decision-oracle/
├── index.html
├── styles.css         # Dark, mystical theme
├── app.js            # Vote logic + OpenAI call
├── oracle-prompts.js # Different persona variations
└── assets/
    ├── sounds/
    │   ├── summon.mp3
    │   └── verdict.mp3
    └── bg.jpg        # Mystical background
```

---

## Oracle personas (variations)

You can have different Oracle characters:

**1. The Mystic Oracle (default)**
- Ancient, wise, slightly cryptic
- "The threads of fate..."

**2. The Sassy Oracle**
- Eye-rolling cosmic entity
- "Ugh, mortals. Fine. Sushi. Happy now?"

**3. The Overly Dramatic Oracle**
- Shakespearean, theatrical
- "HARK! The heavens WEEP with the weight of this choice!"

**4. The Brutally Honest Oracle**
- No mysticism, just facts
- "Three of you voted sushi. Democracy wins. Move on."

**5. The Chaotic Oracle**
- Ignores votes, picks randomly
- "Votes? I don't care about votes. THAI FOOD. Because I said so."

Let users pick the persona before summoning.

---

## Why this works

**Psychologically:**
- Removes personal responsibility for the decision
- Makes boring choices fun
- Defuses arguments (can't argue with destiny)
- Creates a shared ritual

**Practically:**
- Faster than debating for 20 minutes
- Secret votes prevent peer pressure
- AI adds entertainment value
- Reusable for ANY decision

**Emotionally:**
- Turns frustration into laughter
- "The Oracle has spoken" becomes an inside joke
- Even the loser can't be mad at mystical forces

---

## Use cases

**Perfect for:**
- Which movie to watch
- Where to eat dinner
- Which board game to play
- Who does the dishes (put names as options)
- Vacation destination debates
- "Should we leave now or in 10 minutes" (time-sensitive chaos)

**Not great for:**
- Serious decisions (don't Oracle your medical choices)
- Two-person households (loses the group energy)
- Situations where someone has a veto (dietary restrictions, etc.)

---

## Cost estimate
- Hosting: Free (Netlify/Vercel)
- Supabase: Free tier
- ChatGPT API: ~$0.01 per verdict (GPT-4o-mini is fine here)

**Total:** Basically free for family use.

---

## The ritual (how to use it)

**When debate starts:**
- Someone says "Should we Oracle this?"
- Everyone agrees or groans
- Pull up the Oracle on phone/laptop

**During voting:**
- No peeking
- No lobbying
- Just vote your truth

**After the verdict:**
- Read it out loud in a dramatic voice
- Laugh at the AI's reasoning
- Accept your fate
- Move on with your life

**The magic:** You get a decision + entertainment + no resentment.

---

## Pro tips

1. **Let the loser read the verdict** — gives them agency
2. **Add a "reroll" button** — but costs something (someone does dishes)
3. **Save past verdicts** — creates a funny history
4. **Themed music** — plays during the "summoning" animation
5. **Physical token** — whoever holds the crystal gets to summon

---

## Honest take

This is a **conflict resolution tool disguised as a game**.

Families argue about dumb stuff constantly. Not because the stakes are high, but because no one wants to feel overruled. The Oracle:
- Gives everyone a voice (secret vote)
- Makes the final call (removes blame)
- Adds humor (reduces tension)

Build it as a single HTML file if you want. Host it on Netlify. Use it for a decade. It's that simple.

The tech is not the point. The ritual is the point.
