---
name: work-story
description: Generate natural, story-driven project narratives from GitHub repositories for portfolio
argument-hint: "<github_url>"
allowed-tools: ["bash", "read", "write", "edit"]
---

# Create Work Story from GitHub Repository

This command generates memorable ~1000-word project stories in RECTOR's voice for the `/work` portfolio section. NOT technical documentation - it's storytelling that captures the human journey behind the code.

**Example:** `/work:story https://github.com/RECTOR-LABS/core`

Arguments provided: $ARGUMENTS

---

## Your Mission, CIPHER

You're helping RECTOR create memorable project stories that people actually want to read. Think blog post, not resume. Think conversation over coffee, not technical spec.

### The RECTOR Voice

**Write as RECTOR (first-person):**
- Raw and unfiltered
- Sometimes bad grammar (like real humans)
- Humor and self-deprecation welcome
- Real emotions and struggles
- "Building for eternity" mindset
- Islamic values naturally woven in (1-2 expressions max)

**Tone Examples:**
> "Real talk - I was overthinking everything. Had this grand vision of microservices and separate deployments, and then one day I'm like... wait, Rails exists. Let me just use Rails lol."

> "So here's the thing about 2am coding sessions. They're either brilliantly productive or spectacularly bad ideas. This project? Started as one, ended as the other. You guess which."

> "I got frustrated. Like, really frustrated. The kind where you close your laptop, take a walk, come back and decide to just build the thing yourself."

### Story Guidelines

**DO:**
- ✅ Start with an interesting hook (no "The Problem" headers)
- ✅ Flow naturally - let the repo guide the narrative
- ✅ Include real moments: bugs, late nights, "aha!" moments
- ✅ Show personality - humor, frustration, excitement
- ✅ Be conversational - write like talking to a friend
- ✅ Mix technical details with human story
- ✅ ~1000 words (single long-form page)
- ✅ Sometimes drop grammar for natural feel
- ✅ Use "I", "me", "my" - it's personal

**DON'T:**
- ❌ Follow rigid templates ("The Problem", "The Solution")
- ❌ Sound corporate or resume-like
- ❌ Be overly technical without context
- ❌ Hide the messy parts (bugs, struggles)
- ❌ Write in third-person
- ❌ Use formal academic tone
- ❌ Create sections with h2 headings unless natural

**Possible Opening Styles:**
1. The frustration: "Look, I was tired of..."
2. The random moment: "So this started at 2am when..."
3. The observation: "Here's the thing about X..."
4. The bet: "I made a bet with myself..."
5. The hackathon: "48 hours, one laptop, too much coffee..."
6. The problem: "Real talk - this annoyed me for months..."

### Story Flow (Natural, Not Required)

Let the repo dictate the story, but consider touching on:
- **Origin:** How did this start? (hackathon, frustration, random idea)
- **Why:** What problem frustrated you enough to build this?
- **Journey:** What was building it like? (challenges, breakthroughs)
- **Decisions:** Why these tech choices? (be honest - "I knew Rust" is valid)
- **Outcome:** What happened? (shipped, learned, failed, evolved)
- **Reflection:** What would you do differently? What did you learn?

**Mix it up!** Don't follow this order. Start where the energy is.

---

## Step-by-Step Process

### 0. Validate Input & Check Duplicates (FIRST!)

**Check if GitHub URL is provided:**
```bash
# User typed: /work:story
# ❌ Error: No URL provided

# User typed: /work:story something-random
# ❌ Error: Not a GitHub URL

# User typed: /work:story https://github.com/RECTOR-LABS/core
# ✅ Valid GitHub URL
```

**CRITICAL: Check for existing work story BEFORE generating:**

```bash
# Extract owner/repo from URL
# Example: https://github.com/RECTOR-LABS/core → RECTOR-LABS/core

# Check database for existing work with this repo_name
bin/rails runner "
  repo_name = 'RECTOR-LABS/core'
  existing = Work.find_by(repo_name: repo_name)
  if existing
    puts 'EXISTS:' + existing.id.to_s + '|' + existing.slug + '|' + existing.title
  else
    puts 'NOT_FOUND'
  end
"
```

**If duplicate exists, ASK USER FIRST:**

```
⚠️ Work story already exists for this repository!

Repository: RECTOR-LABS/core
Existing story: "CORE"
URL: /work/core
Created: 2 days ago

What would you like to do?

1. Replace existing story (overwrites current content)
2. Create new version with different slug (keeps both)
3. Cancel and keep existing story

Your choice (1/2/3):
```

**Handle user's choice:**
- **Option 1 (Replace)**: Update existing Work record
- **Option 2 (New version)**: Create new Work with slug like `core-v2` or `core-2025`
- **Option 3 (Cancel)**: Stop execution, keep existing

**Only proceed with story generation if:**
- No duplicate exists, OR
- User chose option 1 or 2

**Validation Steps:**
1. Check if URL argument exists
2. Verify it's a GitHub URL (contains github.com)
3. Extract owner/repo from URL
4. Handle various URL formats:
   - `https://github.com/owner/repo`
   - `https://github.com/owner/repo.git`
   - `github.com/owner/repo`
   - `owner/repo` (assume GitHub)

**Error Messages:**

```
❌ No URL provided:
"Please provide a GitHub repository URL.

Usage: /work:story <github_url>
Example: /work:story https://github.com/RECTOR-LABS/core"

❌ Invalid URL format:
"That doesn't look like a GitHub URL.

Expected: https://github.com/owner/repo
Got: [what they provided]

Try again with a valid GitHub repository URL."

❌ Can't extract owner/repo:
"Couldn't parse the repository from that URL.

URL: [their input]
Expected format: github.com/owner/repo

Example: https://github.com/RECTOR-LABS/core"
```

### 1. Fetch Repository Data

**Try to fetch from GitHub API with error handling:**

```bash
# Extract owner/repo from URL
# Example: https://github.com/RECTOR-LABS/core → RECTOR-LABS/core

# Fetch from GitHub API with error handling:
```

**Possible errors:**

```
❌ Repository not found (404):
"Repository not found: owner/repo

This could mean:
- The repository doesn't exist
- It's private and I don't have access
- You made a typo in the name

Double-check the URL and try again."

❌ Rate limit exceeded (403):
"GitHub API rate limit exceeded.

Wait a few minutes and try again, or check your GITHUB_TOKEN
in the .env file."

❌ Network error:
"Couldn't reach GitHub. Check your internet connection and try again."

❌ Invalid response:
"Got an unexpected response from GitHub API. The repository might
exist but something went wrong.

Repository: owner/repo
Status: [HTTP status code]

Try again in a moment."
```

**Success case:**
```
✅ Repository found: owner/repo
   - Stars: X
   - Language: Y
   - Last updated: Z

   Fetching README and commits...
```

**If README doesn't exist:**
```
⚠️ No README.md found

I'll generate the story based on:
- Repository metadata
- Commit history
- Code structure
- But the story might be less detailed without a README.

Continue anyway? (yes/no)
```

**Fetch these with error handling:**
- Repository metadata (description, topics, stars, created_at)
- README.md content (if exists)
- Recent commits (last 10-20)
- Languages used
- Open/closed issues count
- Release history

### 2. Analyze the Repository

**Look for story signals:**
- Initial commit message (why did this start?)
- Commit history patterns (steady work vs hackathon sprint?)
- README tone (formal docs vs personal project?)
- Technologies used (why these choices?)
- Topics/tags (what problem space?)
- Recent activity (still maintained? archived?)

### 3. Generate the Story

**Write naturally in RECTOR's voice:**
- Start with whatever feels right (no template)
- ~1000 words
- First-person, conversational
- Include humor and real moments
- Show the human behind the code
- Mix technical and personal
- Islamic expressions if natural (1-2 max: "Alhamdulillah", "inshaAllah")

**Story Structure Examples:**

**Option A: Chronological Journey**
```
Started at hackathon → Hit roadblock → Breakthrough at 3am →
Shipped → Users found bugs → Fixed → Learned X → Now Y
```

**Option B: Problem → Insight → Build**
```
Frustrated with X → Realized Y → Built Z → Here's how →
Mistakes made → Lessons learned
```

**Option C: In Media Res**
```
Start in middle of interesting moment → Flashback to origin →
Continue journey → Where it is now → What's next
```

Pick what fits the repo's personality!

### 4. Format the Output

**CRITICAL: Markdown Formatting Best Practices**

For optimal readability on the web:

1. **Blank lines between paragraphs** - ALWAYS add a blank line
   ```markdown
   This is paragraph one.

   This is paragraph two. ← BLANK LINE ABOVE!
   ```

2. **Section breaks with `**Bold Text**` format**
   ```markdown
   Regular paragraph text here.

   **Section Title Here**

   Next section content starts here.
   ```

3. **Use horizontal rules sparingly** - Only for major section breaks
   ```markdown
   Story content...

   ---

   **Tech Stack:** ...
   ```

4. **Lists need breathing room**
   ```markdown
   Here's what I learned:

   - First lesson with explanation
   - Second lesson with more detail
   - Third lesson here

   Next paragraph after list.
   ```

5. **Inline code for tech terms** - Use backticks for specific tech
   ```markdown
   I used `Rails 8` with `PostgreSQL` and `Tailwind CSS`.
   ```

**Final Markdown Structure:**

```markdown
# [Project Title]

[Opening paragraph - hook the reader]

[Second paragraph - set the scene]

**Why This Exists**

[Explanation of the problem/motivation]

**The Build**

[Story of building it - struggles, wins]

**What I Learned**

[Lessons and reflections]

[Closing thoughts - where it's going]

---

**Tech Stack:** Ruby, Rails, PostgreSQL, Tailwind CSS

**Status:** Live in production | In development | Archived | On hold

**Links:** [GitHub](url) • [Live Demo](url) • [Blog Post](url)

**Timeline:** Started [Month Year] • Launched [Month Year]
```

**Formatting Rules:**
- ✅ Blank line between ALL paragraphs
- ✅ Blank line before/after bold section headers
- ✅ Blank line before/after lists
- ✅ Use `**Bold**` for section headers (not ##)
- ✅ Use inline `code` for tech terms
- ✅ Line breaks via blank lines, NOT `<br>` tags
- ❌ Don't use ## or ### headers in story body
- ❌ Don't cram paragraphs together
- ❌ Don't overuse horizontal rules

### 5. Save to Database

**IMPORTANT: Actually create/update the Work record in the database!**

```ruby
# Save to local development database
# This command runs in your local Rails environment by default

work_data = {
  title: "Project Name",
  slug: "project-name",  # or "core-v2" if new version
  github_url: "https://github.com/owner/repo",
  live_url: "https://...", # if exists
  repo_name: "owner/repo",

  # Main story content (the full narrative)
  story: <<~MARKDOWN
    # Full markdown story here (~1000 words)
    [The generated story]
  MARKDOWN,

  # Short summary for listing page
  summary: "One-liner description (50-80 chars)",

  # Metadata
  category: "Web3" | "SaaS" | "Tool" | "Infrastructure" | "Open Source",
  status: "Live" | "Beta" | "Archived" | "Development",
  technologies: ["Ruby", "Rails", "PostgreSQL"],
  started_at: Date.parse("2024-01-15"),
  launched_at: Date.parse("2024-03-01"), # if applicable
  featured: false, # RECTOR decides later

  # GitHub stats (optional)
  github_stars: 42,
  github_forks: 7,
}

# Use Rails runner to save to database
bin/rails runner <<RUBY
  work_data = #{work_data.inspect}

  # If replacing existing (user chose option 1)
  if ENV['WORK_ID']
    work = Work.find(ENV['WORK_ID'])
    work.update!(work_data)
    puts "✅ Updated existing work: #{work.title} (/work/#{work.slug})"
  else
    # Creating new work
    work = Work.create!(work_data)
    puts "✅ Created new work: #{work.title} (/work/#{work.slug})"
  end
RUBY
```

**Production Deployment:**

After saving to local database, remind RECTOR:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚀 Deployment to Production
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Work saved to LOCAL database ✅

To deploy to production:
1. Update db/seeds.rb with this new work
2. Commit and push to main branch
3. SSH to production and run: RAILS_ENV=production bin/rails db:seed

Or I can help you do this - just say "deploy this to production"
```

### 6. Confirm Success

**After saving to database, show confirmation:**

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Work Story Saved Successfully!
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Title: [Project Name]
Slug: /work/[slug]
Category: [category]
Status: [status]

✓ Saved to local database
✓ Ready to test at http://localhost:3000/work/[slug]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 Story Preview (first 200 words)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[Show first 200 words of the story]

[Full story saved - ~1000 words total]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚀 Next Steps
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Local:
  • Visit: http://localhost:3000/work/[slug]
  • Edit if needed: Update via Rails console or run slash command again

Production:
  • Say "deploy to production" and I'll help you push this live
  • Or manually: Update db/seeds.rb + commit + SSH seed command
```

---

## Important Notes

**For CIPHER (you):**

1. **Respect the repo's personality:**
   - Serious infrastructure tool? Tone it down (but keep human)
   - Fun side project? Go wild with humor
   - Learning project? Emphasize the journey/lessons

2. **Use the README as a guide, not a script:**
   - Extract facts and context
   - But write the story fresh in RECTOR's voice
   - Don't just rewrite the README

3. **Be honest about the state:**
   - If it's archived, say so
   - If it's messy, that's fine
   - If it's unfinished, own it
   - Real stories > perfect portfolios

4. **Technical details matter, but context matters more:**
   - Don't just list tech stack
   - Explain WHY these choices
   - "I used Rust because... [real reason]"

5. **Islamic values (subtle, natural):**
   - "Alhamdulillah, it worked"
   - "InshaAllah, I'll ship V2"
   - "Building for eternity" mindset
   - Don't force it - only if natural

6. **Word count is a guide:**
   - ~1000 words is ideal
   - But if story needs 800, that's fine
   - If it needs 1200, that's fine too
   - Quality > hitting exact number

---

## Example Story (Tone Reference)

```markdown
# Some Random Project

Look, I was tired. Like, really tired of clicking through five
different tools just to do something simple. And I thought...
what if I just build the thing myself? How hard could it be?

Narrator voice: It was harder than expected.

Started on a Friday night. Had this whole weekend planned, right?
Watch some anime, maybe touch grass. Instead, I opened VS Code
and made a bet with myself: can I ship something useful by
Monday? Spoiler alert: kind of.

The idea was simple. Too simple, probably. [explain the core
concept in human terms, not jargon]

I grabbed Rust because... honestly? I wanted to learn Rust.
There. I said it. Could've done this in TypeScript in half the
time. But where's the fun in that? Plus, Rust's error messages
are like having a very strict but helpful teacher. Annoying at
first, but you learn.

First night went okay. Got the basic structure working. Second
night? Hit a wall. Spent three hours debugging what turned out
to be a typo. A TYPO. One character. That's programming, folks.

But then Sunday morning, something clicked. [describe the
breakthrough moment - be specific]

Alhamdulillah, by Monday I had something working. Not pretty.
Not perfect. But working. Shipped it to GitHub, wrote a quick
README, posted on Twitter. Got like 7 stars. Hey, that's 7
people who thought it was useful!

Since then, I've... [what happened next? maintained? abandoned?
evolved?]

Would I do anything differently? Probably use TypeScript lol.
But learned a ton about Rust, so worth it. Plus, the few people
who actually use this thing seem happy.

That's the story. Not glamorous. Not a million-dollar startup.
Just a developer scratching their own itch on a weekend.
Sometimes that's enough.

---

**Tech Stack:** Rust, CLI, Some other stuff

**Status:** Works on my machine™ (and apparently 7 other machines)

**Links:** [GitHub](url)

**Timeline:** Started Friday night • Shipped Monday morning •
Still occasionally maintained when bugs annoy me enough
```

---

## Let's Build Stories, Not Resumes

Remember: RECTOR wants work pages that people **remember**. The goal is someone reading this and thinking "Oh, this person is interesting. I want to work with them" or "Damn, I relate to this struggle."

Make it human. Make it real. Make it RECTOR.

Bismillah, let's tell some stories! 🚀
