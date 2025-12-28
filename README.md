# 🎄 Vibecoding Projects for Christmas & New Year

**Instead of arguing over Monopoly money, build something together.**

This is a collection of cozy family tech projects — small, meaningful tools that turn your holiday gatherings into moments of connection, laughter, and maybe a little chaos (the good kind).

No "AI demo sludge." No dashboards. No growth metrics. Just simple things that make being together more fun.

---

## The idea

Holidays with family are weird. You love each other, but after two days, someone's arguing about board game rules, someone else is doomscrolling, and the kids are bored.

**These projects give you something else to do:**
- Activities that *everyone* can participate in (ages 6–86)
- Tools that solve actual family problems (who does the dishes? which movie do we watch?)
- Rituals you can repeat every year
- Reasons to laugh together instead of staring at phones separately

None of these are products. They're **digital craft projects** — like baking cookies together, but with code.

---

## The projects

**Two categories:** Family activities that bring everyone together for meaningful moments, and arcade games for quick competitive fun.

### 🎄 [Family Year Rewind](./project_family_year_rewind_claude.md)
A yearly ritual in one click. Answer a few simple questions and turn your family's year into a cinematic recap — complete with funny titles, heartfelt moments, and memories you forgot you shared. Laugh, reflect, and maybe cry a little. Best played with tea, candles, and no phones.

**Tech:** Google Forms/Tally → Supabase → ChatGPT API → React/Next.js → PDF export

---

### 🧠 [Family Decision Oracle](./project_family_decision_oracle_claude.md)
The spirits have spoken. When family debates go nowhere, summon the Oracle. Cast your votes in secret and receive a final verdict — delivered with ancient wisdom, unnecessary drama, and zero accountability. No arguments. Only destiny.

**Tech:** Simple web UI → Supabase for votes → ChatGPT API for persona → Optional sound effects

---

### 🎯 [Guess the Number](./project_guess_the_number_claude.md)
Too high. Too low. Just one more try. A classic guessing game for all ages. Take turns, trust your intuition, and race to crack the mystery number with the fewest attempts. Simple rules. Endless "WAIT, LET ME TRY".

**Tech:** HTML + CSS + Vanilla JavaScript + LocalStorage

---

### 🎭 [Two Truths & a Lie](./project_two_truths_and_a_lie_claude.md)
You think you know them. Enter three stories — two real, one fake. Can the others spot the lie? A simple game that reveals surprising memories, hidden talents, and stories you've never heard before.

**Tech:** Simple web UI (Vanilla JS or React) + LocalStorage + Optional Supabase for multi-device

---

## Holiday Arcade Games

Sometimes you just need something quick, competitive, and fun. These pixel-style games are perfect for killing 5 minutes or hosting impromptu tournaments.

### ❄️ [Snowball Royale](./project_snowball_royale_claude.md)
Cold hands. Warm rivalry. Dodge, throw, and survive in the ultimate winter snowball fight. Easy to play. Hard to stay friends.

**Tech:** HTML Canvas + JavaScript + Gamepad API (optional)

---

### 🐾 [Cozy Winter Pet](./project_cozy_winter_pet_claude.md)
A little friend for a cozy season. Take care of your winter companion and keep it happy through the holidays. Slow, gentle, and warm.

**Tech:** HTML + CSS + JavaScript + LocalStorage + ElevenLabs Voice API (optional)

---

### 🎁 [Present Panic!](./project_present_panic_claude.md)
Too many gifts. Not enough time. Catch the right presents before Christmas chaos takes over. Fast, festive, and surprisingly stressful.

**Tech:** HTML Canvas + JavaScript

---

## Which one should you build?

**If your family is...**

### Family activities
- **Emotional and sentimental** → Family Year Rewind
  - You like traditions, photo albums, and heartfelt moments
  - Someone will definitely cry (in a good way)

- **Argumentative (in a loving way)** → Decision Oracle
  - You debate everything: which restaurant, which movie, who's right
  - You need a neutral third party (a mystical AI entity)

- **Mixed ages (kids + adults)** → Guess the Number
  - Need something everyone can play without explanation
  - Quick, satisfying, endlessly replayable

- **Chatty and curious** → Two Truths & a Lie
  - You like storytelling and learning new things about each other
  - You have at least 4 people who want to participate

### Arcade games
- **Competitive siblings** → Snowball Royale
  - You want to throw things at each other (digitally)
  - 2-4 players, one screen, chaos ensues
  - Perfect for settling disputes

- **Need a gentle moment** → Cozy Winter Pet
  - You want something calm and low-stakes
  - A tiny companion to care for between activities
  - Optional adorable voice interactions

- **Quick reflexes & high scores** → Present Panic!
  - You like arcade-style challenges
  - Fast-paced, immediately replayable
  - Great for solo play or score competitions

**Honest answer:** Pick the one that sounds fun to YOU. If you're excited to build it, it'll be better.

---

## How to use these projects

### Option 1: Build it yourself
- Each project has a full guide (click the links above)
- Estimated build time: 2–6 hours depending on experience
- Can be a solo project or pair-program with a family member
- Great for learning: simple enough to finish, complex enough to be interesting

### Option 2: Build it together
- Make it a family coding session
- One person codes, others design/test/add ideas
- Kids can pick colors, adults handle logic
- The building is part of the holiday memory

### Option 3: Use it as a template
- Fork the idea, make it yours
- Swap the Oracle persona for your family's inside jokes
- Change the Year Rewind questions to fit your traditions
- Add family-specific features

---

## Philosophy: Vibecoding

These projects follow a design philosophy I'm calling **vibecoding:**

**What it is:**
- Code that creates an *experience*, not just functionality
- Tools that bring people together, not separate them
- Small, finished projects (not MVPs that scale to infinity)
- Built with love, not growth metrics

**What it's not:**
- A startup idea
- A portfolio project to impress recruiters
- Something with a roadmap and v2 plans
- Optimized for monetization

**In practice:**
- Use the simplest tech that works (vanilla JS is fine!)
- Prioritize emotion over features
- Finished is better than perfect
- Make it cozy (dark mode, nice fonts, little details)

---

## Anti-features (things these projects DON'T have)

❌ User accounts
❌ Cloud sync (unless you want multi-device)
❌ Analytics
❌ Notifications
❌ Subscriptions
❌ Growth hacking
❌ Social sharing ("Share your rewind on Twitter!")
❌ Gamification (beyond natural game mechanics)

**Why?** Because family time should be *offline by default*. Privacy-first. No extractive patterns.

---

## Cost to run these

All of these can be **basically free:**

- **Hosting:** Netlify/Vercel free tier (or run locally)
- **Database:** Supabase free tier (if needed)
- **AI calls:** ~$0.01–0.05 per use (ChatGPT API for family activities)
- **Voice API:** ~$5/month for ElevenLabs (only for Cozy Winter Pet, optional)
- **Domain:** Optional (~$12/year if you want a custom URL)

**Arcade games:** $0 (100% free, no APIs, can run offline)
**Family activities:** Maybe $5–20/year if you use them weekly

Compare that to:
- One board game: $30–60
- Movie tickets for a family: $50–80
- Streaming service: $15/month

---

## When to use these

**Perfect for:**
- Christmas Eve when everyone's together but don't know what to do
- New Year's Eve as a reflection activity
- Long dinners where conversation lulls
- Rainy holiday afternoons
- Family reunions with cousins you haven't seen in a year
- "Device-free time" alternatives (ironic, but it works)
- Quick 5-minute breaks between activities (arcade games)
- When kids need something to do while adults chat

**Not great for:**
- Two-person households (family activities need 3+ people; arcade games work for 1-4)
- Families with very young kids under 6 (though Guess the Number and Present Panic! can work)
- Super formal gatherings
- When someone's having a genuinely hard time (read the room)

---

## How to introduce these to your family

**Don't:**
- "I built an app, you all have to use it now"
- Make it feel like homework
- Force participation
- Hype it up too much

**Do:**
- "I made a thing, wanna try it?"
- Start with the simplest one (Guess the Number or Snowball Royale)
- Let people opt in naturally
- If it flops, laugh it off and play cards instead

**The magic happens when:**
- Someone who was skeptical gets really into it
- A quiet family member opens up during Two Truths
- The Oracle makes a decision and everyone actually accepts it
- You pull up last year's Rewind and compare
- Siblings start trash-talking over Snowball Royale scores
- Someone checks on the Cozy Winter Pet unprompted

---

## Extending the idea

Once you've built one, you might want to make more. Here are variations:

**Other family vibecoding ideas:**
- **Family Time Capsule** — everyone records a video message, encrypted until next Christmas
- **Recipe Oracle** — family submits recipes, AI generates a weekly dinner plan
- **Compliment Generator** — random kind messages for each family member
- **Story Chain** — collaborative storytelling game (each person adds one sentence)
- **Photo Roulette** — AI picks random old family photos and asks "what was happening here?"

**More arcade game ideas:**
- **Cookie Decorator** — Tetris-style but with decorating cookies
- **Holiday Memory Match** — Memory card game with family photos
- **Snowman Builder** — Stack falling snow pieces to build the tallest snowman
- **Gingerbread Runner** — Endless runner avoiding obstacles in a candy world
- **Ornament Drop** — Physics puzzle placing ornaments on a tree

**The pattern:**
1. Find a common family friction point or boring moment
2. Make a tiny tool that adds joy or removes frustration
3. Use it once, see if it sticks
4. If it does, make it an annual tradition

---

## Contributing

This is not a repo for contributions (not a product, remember?).

**But you can:**
- Fork these ideas and make your own version
- Share what you built (tag it #vibecoding if you want)
- Adapt them for your culture/language/family dynamics
- Send me your variations (I'd love to see them)

**The spirit of this:** These are recipes, not franchises. Adapt freely. Make them yours.

---

## Why I made this

Because I'm tired of:
- Families sitting in the same room on separate devices
- "Quality time" that's just watching TV together
- Board games that end in arguments
- Tech that extracts value instead of creating it

I wanted to build things that:
- Use AI for good (creativity, fun, connection)
- Are small enough to finish
- Don't require VC funding to justify
- Would make my own family smile

If even one family uses one of these projects and has a good evening together, this succeeded.

---

## Final thoughts

**This is not about replacing human connection with apps.**

It's about using tech as a tool to *create space* for connection:
- The Oracle removes the friction of indecision
- The Rewind gives you a reason to reflect together
- The arcade games give you something to do besides scroll
- The Winter Pet gives you a gentle ritual
- The activities create shared stories you'll remember

The best outcome is: you use these for 30 minutes, laugh together, then put the devices away and just talk.

**Build something. Use it with people you love. Repeat next year.**

That's the whole vision.

---

## License

Do whatever you want with these ideas. Seriously.

- Build them for your family (obviously)
- Adapt them for your friend group
- Use them at work retreats (if your coworkers are cool)
- Teach them in a class
- Sell them as a product (though... why?)

**Just don't:**
- Be weird and extractive with people's data
- Turn them into surveillance tools
- Add dark patterns

**Keep them cozy. Keep them kind.**

---

## Questions?

**"Can I build these if I'm not a developer?"**
Yes. Start with Guess the Number (it's one HTML file). Google everything. You'll learn.

**"Will my family actually use these?"**
Maybe! Depends on your family. Try the simplest one first. If they hate it, you spent a few hours learning to code. Not a loss.

**"Can I monetize this?"**
You can, but it'll lose the magic. These work *because* they're personal and non-commercial. But you do you.

**"What if I want to add features?"**
Go for it! Just resist the urge to over-engineer. These are meant to be small and finished.

**"Are you going to maintain these?"**
Nope. These are finished projects. They work as-is. Fork and evolve them yourself.

---

**Happy holidays. Build something cozy. Spend time with people you love.**

🎄✨

---

*Made with care (and a little AI help) by someone who thinks technology should make life more human, not less.*
