# Deep Dive — Compiler Prompt, Activation Moment, Weekly Digest

**Date:** 2026-05-14
**Stage:** Brainstorm round 2. Still pre-design.
**Source:** Founder follow-up after consolidating round 1 critique.

This doc goes deep on the three areas the founders flagged for further
exploration after agreeing on wedge audience (Obsidian power user), output
artifact (wiki + digest sequenced), object model (pages now, claims later),
trust UX (librarian not author), and pricing (usage-tiered).

---

## 1. The Compiler Prompt Design

### North star: librarian, not author

The librarian metaphor (founders introduced) is correct. Push it harder than
intuition suggests. A librarian:
- Catalogs and cross-references; does not write new content
- Preserves the voice of each source; does not homogenize
- Says "I don't know" or "see source X" without shame

LLMs are constitutionally bad at this. They are sycophantic, hate empty
space, and want to demonstrate competence by synthesizing. A weak prompt
("be conservative") produces a model that *sounds* conservative but *acts*
confidently. This will not be detected in casual review. It will be detected
months in when a user catches a fabricated claim.

### Banned operations (the prompt's spine)

The prompt should be organized around an explicit forbidden-list, not vague
guidance. Specifically forbidden:

- **Asserting any claim without an inline citation to a specific source
  quote.** Anchored on the sentence, not loose at the paragraph end.
- **Cross-source synthesis without flagging it as synthesis.** Combined
  claims must be visually marked as synthesis and segregated from
  source-grounded prose.
- **Dropping hedges or epistemic markers.** "Some evidence suggests" must
  not become "evidence shows." Most insidious failure mode — the model
  thinks it's improving prose. It's manufacturing certainty.
- **Inferring causation from correlation.** "X is associated with Y" must
  not become "X causes Y."
- **Generalizing from N=1.** Single anecdote → general claim is forbidden.
- **Treating user-authored notes as factual.** User hypotheses stay
  hypotheses. They get attributed to the user. They never become flat
  wiki facts.
- **Resolving disagreements between sources.** Conflicts get *shown*,
  not adjudicated.
- **Removing temporal context.** "As of [date]" markers stay. Knowledge
  isn't eternal.

These are framed as *violations the compiler must self-check before
writing*, with explicit examples of each. Not "try to avoid."

### Two outputs per compilation event

Most important architectural point: the compiler produces **two** artifacts
per event, not one.

- **The wiki page** — the rendered artifact for normal reading.
- **The compilation log** — structured record of: every claim extracted,
  the source quote that grounded it, every claim considered but rejected
  and why, every uncertainty, every judgment call.

User sees the wiki normally. The log is right there during review. This
is the substrate for trust *and* the eval data that becomes the moat.

### Intermediate "claim ledger" pattern

The compiler should not go directly source → prose. Pipeline:

```
source → atomic claims with citations → prose generated from the ledger
```

Benefits:
- The ledger is auditable
- Cross-source dedup happens trivially
- Forces grounding work before nice sentences
- Errors traceable: bad prose → bad ledger entry → misread source quote
- Contradiction detection becomes free (two ledger entries on same
  entity that conflict → flag)

### Self-skeptic pass

After generating wiki content, run a separate critic prompt — possibly a
different model — whose only job: "find any sentence here not directly
grounded in the provided source quotes." Catches the model's natural
tendency to add reasonable-sounding connective tissue.

Costs more inference. Worth it. This is the layer that prevents trust
collapse.

### Voice and register specifics

- **Forbidden words unless source uses them**: proves, demonstrates,
  shows that, establishes
- **Preferred attribution verbs**: argues, observes, claims, reports,
  suggests
- **Permission to be brief**: "If a section is thin, leave it thin. Empty
  space is honest." LLMs hate this — must be explicitly permitted.
- **Permission to say "I don't know"**: "[no sources in this vault
  address X]"
- **Author voice preservation**: "Karpathy argues" not "It is the case
  that"

### The synthesis vs assertion line

Founders asked for the line. Test: **could the user disagree with the
statement and be right?**

Hierarchy of safety:
- "Source A says X" — citation, not synthesis. Safe.
- "Sources A, B, C all describe X" — meta-observation about corpus. Safe
  if true.
- "X tends to be true in domain Y" — generalization. Safe only if a
  source explicitly generalizes.
- "X causes Y" — assertion. Safe only if a source explicitly makes the
  causal claim.
- "Given X (from A) and Y (from B), Z follows" — synthesis. **Must be
  flagged**, not mixed into prose. Belongs in a "Connections I noticed"
  section the user reviews.

Key insight: **synthesis is valuable but not wiki content.** Synthesis
goes in the digest, the "wondering" section, the review queue. The wiki
itself stays librarian-conservative. The compiler can do bold synthesis
— it just doesn't get to put it directly into wiki pages.

This maps cleanly onto the librarian metaphor. A librarian writes book
reviews and recommendations (synthesis, opinion), but the catalog is
just facts. Same compiler, two registers.

### Predicted outcome when this ships

Wiki pages will be *boring*. Sourced, hedged, attributed, full of "Smith
(2024) argues" prefixes. This will feel underwhelming compared to free
GPT prose. **That's correct.** The wiki is a reference, not a magazine.
Wikipedia is boring. People trust Wikipedia *because* it's boring.

Flair lives in the digest. Wiki stays librarian. Digest is columnist.

---

## 2. The Activation Moment

The bet: "compile your messy vault in 60 seconds." The bet is mostly
right. The framing has a trap.

### What a real messy vault contains

Obsidian power-user vault, 2-3 years deep:
- 500–3000 notes, lengths from one-liner stubs to 5000-word essays
- Inconsistent naming variants for same concept
- Multiple labels for same idea (PKM, Second Brain, Zettelkasten…)
- Half-finished notes with `TODO`
- Stub notes — title only
- Daily notes mixing personal and professional content
- Quotes interleaved with original thinking, unclear attribution
- Templates filled in once and abandoned
- Old notes the user has forgotten and may now disagree with
- Plugin-generated dynamic content (Dataview, Templater)
- Broken links to renamed/deleted notes
- Embarrassing drafts the user doesn't want surfaced
- Embedded PDFs, images, sometimes audio
- Periodic, evergreen, and literature notes treated identically

This is not a corpus. It's an archaeological site.

### Why the 60-second compile framing is dangerous

If you actually compile this in 60 seconds, two outcomes — both bad:

1. **Garbage**: compiler chokes, produces polished-looking wiki full of
   subtle errors. User trusts it. Catches first error a week in. Trust
   collapse.
2. **Vapor**: compiler is so conservative it doesn't change anything.
   No wow.

The 60-second framing forces option 1 because option 2 isn't a wow.
Trap.

### The reframing: insight in 60 seconds, compile is opt-in

Deliver a 60-second moment of undeniable value **without modifying
anything**. The compile is the next step the user *asks for*.

Activation moves from "you got a compiled vault" to **"holy shit, look
how much structure was hidden in my chaos."** Different aesthetic:
revealing latent structure, not asserting new structure.

### Five panels for the 60-second moment

All non-destructive, all generated from embeddings + lightweight
clustering + a single LLM pass per cluster:

1. **Duplicates panel**: "23 groups of notes that might be the same
   concept. Click to merge or keep separate." Immediately useful.
   Showcases the compiler's eye.
2. **Orphans panel**: "89 notes have no incoming links. Some are gems
   you forgot. Some can probably be deleted." Surfacing forgotten value
   is under-rated.
3. **Emerging-topics map**: "12 topic clusters I see. Your two largest
   are X and Y. Want pages for these?" Visual concept graph. Beats
   prose claims.
4. **You-contradict-yourself panel**: "4 pairs of notes seem to make
   opposite claims. Want me to flag for review?" People love being
   shown their own inconsistencies.
5. **What-I-refused-to-touch report**: "I left 89 notes alone — they
   look like personal/journal content. Tell me what categories should
   be in scope." The compiler's first action is to *not act*.
   Trust-building move.

The compiler hasn't compiled anything. It has *seen* the vault. The
user can feel that.

### Compile is a separate, deliberate step

Even after opt-in, it shouldn't be one big batch:

1. Pick a topic cluster (or "all of category X") to compile first
2. Compiler proposes new pages, edits, links — side-by-side with
   originals
3. User reviews at topic level. Approves, rejects, edits.
4. Compiler learns from corrections, adjusts heuristics for next pass

User experiences compilation as **collaboration**, not transformation.
Every approval increases trust.

### Where the compiler writes

Never modify originals on first compile. Create a `compiled/` folder.
Originals untouched. Compiled wiki references them, fully reversible by
deleting the folder. Trust earned → user opts into letting compiler
edit originals.

This avoids the "I just lost three years of notes" support ticket that
ends the company.

### Bonus: the activation moment is also marketing

The 60-second insight panel is the **best demo asset** and the **best
onboarding gate**. Drop a sample vault, see it instantly. TikTok-able
moment, tweet-able screenshot, landing-page video. The first wow is
the marketing artifact.

---

## 3. The Weekly Digest

Retention engine and viral surface — both, in tension.

### Why most automated summaries fail

Standard pattern: "Added 12 notes this week. Top tags: AI,
productivity. Most-edited: My Notes." This is a **changelog**. Nobody
forwards a changelog.

Failure clusters:
- Generic (could be anyone's)
- Bureaucratic (notification tone)
- Comprehensive (no editorial)
- Backward-looking (no implication)
- Voiceless (sounds like a system)
- Padded (forced richness on quiet weeks)

### The forwardable test

**Would the user screenshot a single sentence and post it?** If no, not
good enough.

That requires:
- A specific insight at the top
- An angle, not a summary
- Connection-making the user wouldn't have noticed alone
- Surfacing the past — "you almost forgot…"
- A short, scannable changelog at the bottom (not the headline)

### Concrete digest skeleton

1. **Subject line**: specific to the week — "This week you got obsessed
   with agency." Drives open rate.
2. **The lede**: one sentence with the week's core insight. The
   forwardable line. Optimize hard.
3. **Connections you might have missed**: 1-3 cross-source
   observations. "You read X about Y, and your note from June about Z
   directly contradicts this."
4. **You might have forgotten**: 1-2 surfaced old notes that became
   relevant again.
5. **You might want to explore**: 1-2 prompts. "You've read a lot about
   RAG but haven't engaged with the criticism — here are two contrarian
   sources." Suggestive, not pushy.
6. **What changed in your wiki**: bullets, scannable. Link to review
   queue. Short. Not the headline.

Whole thing reads in 90 seconds. First 30 seconds contain the value.

### The voice problem

Hardest part. Can't sound like AI. AI-flavored writing — "It's worth
noting that," "Interestingly," "These developments suggest" — kills
forwardability instantly.

Voice target: **smart friend who read everything you read and has
thoughts.** Direct, specific, opinionated, sometimes funny, never
padded.

Digest prompt is *wildly different* from the compiler prompt. Compiler
is constrained librarian; digest is unleashed columnist (writing about
a corpus the user already trusts).

Useful constraint: digest sentences must either (a) make a specific
factual reference to a note/source, or (b) make a specific
recommendation. Generic commentary is banned. Forces concreteness.

### The "nothing happened" problem

Quiet week → digest must be honest. "Quiet week. The most interesting
thing was [one item]. See you next Monday."

Forced richness on slow weeks erodes trust in the rich weeks.
Counterintuitive — engagement metrics will say pad — but honesty pays
off long-term.

### Cadence experiments

- **Weekly** — default expectation, retention rhythm. Ship first.
- **Monthly long-form** — real reading-pattern shifts take that long
  to be visible. "Knowledge state of the month" essay format. Probably
  more substantive and forwardable.
- **Quarterly retrospective** — "how your understanding of X evolved
  this quarter." Nothing else on the market does this. Possibly the
  highest-value email of all.

### The viral mechanic worth building

Beyond forwarding the email: **let users opt into making the synthesis
section public** as a personal-blog-style post. "Here's what I read
this week and the connection nobody made." That's a tweet. That's
content. That's marketing.

The product becomes a **passive content engine** for the user. They
get shareable content from their reading; you get distribution.
Potentially a bigger growth lever than the digest itself.

Constraint: only synthesis is shareable. Wiki content stays private.
Synthesis is the place where the *user's interpretation* lives, so it
feels like their content, not the tool's content.

---

## Three observations on the consolidated direction

**1. The "librarian compiler + columnist digest" split is the actual
architecture.** The compiler is constrained and conservative; the
digest is unleashed and opinionated. Same model, two prompts, two
registers. Users get safety in their wiki and excitement in their
inbox. This is the real product insight — much more than the agent
fleet.

**2. The activation moment is "the tool sees me," not "the tool wrote
my wiki."** Duplicates panel, orphans panel, topic map — these are the
wow. Being seen is more valuable than being served. Compile follows
naturally once trust is established.

**3. The compilation log is your moat.** Every user correction becomes
training signal. Over a year, the largest dataset on earth of "what
humans accept as good knowledge synthesis from their own sources."
Not replicable by an open-source clone of the compiler.
