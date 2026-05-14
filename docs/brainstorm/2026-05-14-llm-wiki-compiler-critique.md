# LLM Wiki Compiler — Idea Critique & Pushback

**Date:** 2026-05-14
**Stage:** Pre-design. Idea refinement, not specification.
**Source:** Initial product brief from founders.

This doc captures pushback, gaps, and reframings on the initial brief. It is
**not** a design doc, plan, or task list — those come later, after the wedge
audience and core artifact are nailed down.

---

## Where the brief is wrong (or thin)

### 1. The "fleet of five agents" is engineer-flavored, not user-flavored
Five agents = five places state can drift, five surfaces to debug, five things
the user has to mentally model. The user does not care about the architecture.
They care: "did my note land in the right place, and can I trust what got
written?"

If you can't sketch the fleet on a napkin and defend why *each one* must be a
separate process (vs. stages inside one compiler), it's premature complexity.

**Recommendation:** Collapse to one compiler with internal stages, plus one
user-facing **review queue** that all stages post into.

### 2. "Wiki feels alive after 30 days" is a retention disaster
Most PKM tools lose users in week 1. A 30-day-to-magic value prop will produce
brutal activation rates.

You need a **minute-one wow**. Likely candidate: "point it at your existing
messy Obsidian vault, get a cleanly compiled version back in 60 seconds." That
is an *importer*. The compiler-fleet is the retention loop. Don't conflate them.

### 3. "Non-technical Obsidian user" is a contradiction
Obsidian users self-selected for fiddling. They cleared every setup wall
already. Truly non-technical PKM-curious humans use Apple Notes, Notion, Google
Docs, or nothing.

Pick one:
- **(a) Obsidian wedge** — admit you're selling to power users who want to
  offload maintenance. Smaller TAM, faster validation, higher willingness to
  pay. This is the realistic v1.
- **(b) Non-technical wedge** — input is Kindle highlights + YouTube history +
  Gmail + voice memos. Different product entirely.

The current pitch tries to be both. It can't.

### 4. "Privacy by design — content never persists" conflicts with "wiki grows in the background"
A background compiler needs either persistent server-side access to vault
contents, or a local-first agent. If local-first, the managed backend gets
thin. If server-side, content sits during compilation windows.

Honest version: "we hold content while processing, sync results back, drop
it." That is reasonable, not strict zero-persistence. The PKM crowd will sniff
out hand-waving immediately. Be precise.

### 5. $12/month for unlimited compilation is a negative-margin trap
A heavy user dropping 100 PDFs a month burns $30+ in inference alone. Mem.ai
learned this. Either tier on usage (sources/month, compute/month) or price
higher. Look at how Granola and NotebookLM handle this.

### 6. "Open source the MCP layer, charge for the backend" — moat is undefined
If the connector layer is open source and the compiler logic is the secret
sauce, what stops a competent team cloning the compiler in a quarter?

Real moat candidates:
- **Eval data** from millions of compilation decisions + user corrections
  (real, durable).
- **Integration breadth** — Obsidian + Notion + Drive + Readwise + Pocket +
  Kindle + … (boring but defensive).
- **The social/network layer** if it ever materializes.

The agent code is not the moat.

---

## What the brief misses entirely

### A. The trust loop is the actual product
When an LLM rewrites notes that matter, users need to audit, undo, and feel
safe. The intake curator is on the right track but the harder question is:
**what does post-compilation review look like?**

- Git-style diffs per compilation event?
- One-click revert per page?
- "Here is the source quote that justified this edit"?

This is a top-three design constraint, not a feature. One bad hallucination
cited later and that user is gone forever — and tells every PKM influencer
about it.

### B. The "what do I do with my wiki" loop is missing
Building the wiki is half the job. *Using* it is the other half. If reading
happens in Obsidian's existing UI, you've built a maintenance bot for someone
else's product, and value capture gets harder.

The product gets much more interesting if compilation produces **new
artifacts**:
- Weekly synthesis essays
- "What changed in your understanding this month"
- Ad-hoc Q&A grounded in your sources
- A "things you almost forgot" surface

The wiki is the substrate. The **synthesis output** is what people pay for and
recommend.

### C. Voice memos are the most underrated input mentioned
Most thinkers don't write — they talk to themselves. Voice memo dropped in →
cleanly compiled into the right page with backlinks → magical loop nobody
owns. Granola owns meeting notes. Nobody owns *personal voice thinking*. This
might be the wedge feature, not just one bullet on an input list.

### D. NotebookLM is the unnamed primary competitor
Forget Obsidian plugins. The real comp is **NotebookLM**, ChatGPT Projects,
Claude Projects. All converging on "give me sources, ask questions."

The differentiator vs. them is the **persistent, structured, growing artifact**
that lives in a tool the user already trusts (Obsidian). NotebookLM has no
graph, no persistence model, no "this is mine forever" feeling. That's the
wedge against them — but they have to be named as the comp internally.

### E. Mem.ai is the second unnamed competitor and the cautionary tale
Chasing AI-native second brain since 2020. Well-funded. Struggled with
retention. Their mistake: tried to *replace* Notion. The right move is
*augmenter* of tools people already love. Read their post-mortems before
spending a dollar.

### F. The social layer instinct (build second) is right; reasoning is wrong
Stated reason: "validate first." Real reason: a meaningful social graph needs
~10K users with 30+ day wikis to be interesting — that's far out.

But **architecturally social must come first**. Compilation should produce
embeddings, concept fingerprints, and topic graphs from day one even if not
exposed. Otherwise retrofitting later means recompiling everyone's history.

Schema-first, productized-second.

### G. The object model decision shapes everything
Is a *wiki page* the primitive, or are *entities + claims + sources* the
primitive with pages as a render layer?

Karpathy used pages because humans grok them. The right underlying model is
probably claims-with-citations, with pages as a view. This determines:
- How contradictions are handled (two sources disagree on a claim)
- How the social layer works (overlap is on concepts, not pages)
- Whether alternate views are possible later (timeline, person, topic)

Get this right early or pay later.

---

## Sharper value prop candidates

The brief's version — "the wiki feels alive" — is poetic, not commercial.

Better candidates:
- "Your reading turns into a knowledge base, automatically."
- "The second brain that maintains itself."
- "NotebookLM, but it remembers everything you've ever read."
- "Drop a source. Get a wiki page back. Forever."

Pattern that works: name the input, name the output, imply the magic in
between. Avoid "compound" — it's a 30-day promise and people don't buy
30-day promises.

---

## A simpler, sharper v1

If shipping in a quarter:
- **One agent**, not five. A compiler that runs on a schedule.
- **One primitive**: a "compilation event" with a visible diff and one-click
  revert.
- **One integration** at launch: Obsidian (the wedge), with hooks designed for
  Notion/Drive next.
- **One killer artifact**: the **weekly digest email**. People open emails,
  not dashboards. Also a retention engine and viral surface.
- **One activation moment**: dump existing chaotic vault, get compiled version
  back in <60s. *That* is the wow. Maintenance is the hook.

The "fleet of agents" framing is something to tell investors later when the
surface is already loved. Don't lead with it.

---

## On the social layer

Build it second, but **design the data model for it from day one**. Concept
fingerprints and embeddings as a byproduct of compilation.

The social mechanic worth prototyping early (privately) isn't "match users
with overlapping wikis" — it's **"show me how my understanding of X compares
to someone else's."** That's a fundamentally new thing. Worth a private
prototype with ~20 friends well before public launch. Start as a *comparison
tool*, not a *feed*.

---

## Biggest unnamed risk

**Trust collapse from one bad compilation.** If the compiler hallucinates a
claim into someone's wiki, and they cite it later, and it's wrong — they're
gone and they tell everyone.

Default policy must be aggressively conservative: assert only what's literally
in sources, link liberally, summarize sparingly, mark uncertainty visibly.
Most products fail by being too timid. **This one will fail by being too
confident.** Build the timid version first; let users opt into more synthesis.

---

## Highest-leverage open questions

Before any design work, nail these down. Each gates the others.

1. **Wedge audience**: Obsidian power user (offload maintenance) or
   non-technical reader (build me a brain I never had)? Cannot be both at
   launch.
2. **Output artifact**: is the wiki itself the product, or is the weekly
   digest / synthesis the product with the wiki as substrate?
3. **Object model**: pages as primitive, or claims-with-citations as
   primitive?
4. **Trust UX**: how does diff / review / revert actually feel? This is where
   the product lives or dies.
5. **Pricing model**: usage-tiered or all-you-can-eat — and have you actually
   modeled inference costs at the p95 user?

Recommend starting with #1. Every other answer depends on it.
