# 🎄 Family Year Rewind

A yearly ritual in one click. Answer a few simple questions and turn your family's year into a cinematic recap — complete with funny titles, heartfelt moments, and memories you forgot you shared. Laugh, reflect, and maybe cry a little. Best played with tea, candles, and no phones.

**Tech stack:** Google Forms/Tally → Supabase → ChatGPT API → React/Next.js → PDF export

---

## How it's actually built

**Simple, realistic version:**

1. Create a single form with 5–7 questions (Google Form / Tally)
   - "What was your biggest win this year?"
   - "What made you laugh the hardest?"
   - "What was your most memorable meal?"
   - "What did you learn about yourself?"
   - "What are you leaving behind in 2024?"

2. Each family member fills it once (phone-friendly, takes 5 minutes)

3. Answers are stored in a table (Supabase or Google Sheets)

4. One "Generate Rewind" button:
   - AI groups answers by person
   - Generates for each person:
     - A cinematic title ("Sarah's Year of Brave Choices")
     - A short "year arc" (2-3 sentences)
     - Optional roast/wholesome tone toggle

5. Output:
   - One scrollable web page with everyone's story
   - OR a printable PDF
   - You read it out loud together (this is the magic part)

**No real-time stuff. No accounts. One-shot generation.**

---

## Implementation details

### Form questions (example set)
```
1. What was your biggest win this year?
2. What made you laugh the hardest?
3. What was your most memorable meal or trip?
4. What did you learn about yourself?
5. What are you leaving behind in 2024?
6. What are you bringing into 2025?
7. One word to describe your year:
```

### AI prompt structure
```
You are creating a warm, cinematic year-in-review for a family.

For each person, generate:
1. A movie-style title (e.g., "Dad's Year of Unexpected Adventures")
2. A 2-3 sentence narrative arc that weaves their answers together
3. Tone: [wholesome / gently roasting / mystical]

Format as a beautiful, readable web page with sections for each family member.
```

### Tech choices
- **Form:** Google Forms (free, everyone knows how to use it) or Tally (prettier)
- **Storage:** Supabase (simple table with columns: name, q1, q2, q3, etc.)
- **Backend:** Simple Next.js API route that:
  - Fetches all responses
  - Sends to ChatGPT with prompt
  - Returns formatted HTML/markdown
- **Frontend:** Single page that displays the rewind with nice typography
- **Export:** Use `html2pdf.js` or similar for PDF generation

### Folder structure
```
family-year-rewind/
├── app/
│   ├── page.tsx           # Main form + display page
│   ├── api/
│   │   └── generate/
│   │       └── route.ts   # ChatGPT call
├── lib/
│   └── supabase.ts        # DB connection
├── prompts/
│   └── rewind.txt         # AI prompt template
└── public/
    └── styles.css         # Cozy typography
```

---

## The ritual (how to use it)

**Before Christmas dinner:**
- Send the form link to everyone
- Set a deadline (e.g., "fill by Dec 24th 8pm")
- No peeking at others' answers

**On Christmas evening:**
- After dinner, everyone sits together
- Someone hits "Generate Rewind"
- Wait 30 seconds (build anticipation)
- Read each person's rewind out loud
- Laugh, cry, argue about the AI's interpretation
- Save the PDF for the family archive

**The magic:**
- Forces everyone to reflect (rare in busy families)
- AI makes it feel special (not just reading raw answers)
- Creates a shared artifact you'll look back on
- Works for 3 people or 30 people

---

## Why this works

1. **Low effort, high emotion** — 5 minutes to fill, but meaningful output
2. **Outsources curation** — AI finds the thread in chaotic answers
3. **No comparison** — everyone gets their own story, no competition
4. **Replayable** — becomes an annual tradition
5. **Offline-friendly** — can work without internet after generation

---

## Variations

- **Roast mode:** AI highlights funny contradictions ("Sarah said she's done with drama, then told 3 drama stories")
- **Time capsule:** Save answers encrypted, reveal next year
- **Grandparent edition:** Use voice recording instead of text
- **Photo integration:** Upload one photo per question, AI describes the visual story

---

## Cost estimate
- Google Forms: Free
- Supabase: Free tier (plenty for one family)
- ChatGPT API: ~$0.02 per rewind (GPT-4o)
- Hosting: Vercel free tier

**Total:** Basically free for personal use.

---

## Honest take

This is not a product. This is a **ritual tool**.

The value isn't in the tech — it's in:
- Getting everyone to pause and reflect
- The moment when someone's rewind makes them tear up
- Finding out your sibling had a hard year you didn't know about
- Laughing at the AI's weird phrasing

Build it simple. Make it cozy. Use it once a year. That's enough.
