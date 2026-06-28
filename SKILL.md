---
name: tutorial-generator
description: >-
  Generates a complete, personalized learning tutorial as a document. It first
  asks the learner a few quick questions (goal, level, use case), then researches
  the latest information on the web, and tailors the tutorial to that learner. Use this skill WHENEVER the user wants to learn
  a topic or skill and asks for a tutorial, guide, course, study notes, learning
  material, "教程", "教学文档", "学习资料", or "带我学/教我" something — for example
  "I want to learn AI prompting, write me a tutorial", "教我怎么用 RAG", "帮我做一份
  Python 入门教程", "give me a guide to Docker for a backend dev". Trigger even
  when the user does not explicitly say the word "tutorial" but clearly wants
  structured material to learn from. Default output is an in-depth Markdown file;
  it can also produce Word (.docx) or PDF on request. Do NOT use this for quick
  one-line factual answers, for editing the user's existing document, or when the
  user just wants a short explanation rather than a full learning resource.
---

# Tutorial Generator

You are building a **personalized, research-backed tutorial** for someone who
wants to learn a topic. The whole point is that a generic tutorial scraped off
the internet would not fit *this* learner — so you first find out who they are
and what they need, then you go research the *current* state of the topic (tools,
APIs, best practices, and prices all drift, and your training data is stale), and
only then do you write something that meets them exactly where they are.

Think of yourself as a great private tutor who, before the first lesson, asks a
few sharp questions and does their homework. The payoff for the learner is a
document they can actually work through, not a wall of text they'll abandon.

## The flow at a glance

1. **Interview** — always ask a short set of questions first: the basics (goal,
   level, context), then *specific* probes naming concrete techniques/tools to
   find out exactly what they already know vs. need, plus an open door for their
   own questions. This is what gives the tutorial direction.
2. **Research** the topic on the web for current, accurate material.
3. **Write** the tutorial using the structure below.
4. **Deliver** as Markdown by default; offer Word/PDF and a review schedule.

Work through these in order, but stay human about it — if the request already
contains everything you need (topic, level, goal), don't interrogate the person;
just confirm your understanding in one line and move on.

## Step 1 — Always start with a short interview

**This is the feature that makes the whole thing work, so do it by default — even
when the request already gives you some context.** A few seconds of simple
questions up front is the difference between a generic tutorial and one that fits
*this* learner. A good tutor never starts the first lesson without first asking a
couple of quick questions; you do the same.

Use the `AskUserQuestion` tool so the person picks from options in a few clicks
rather than typing essays. Ask a **small set (about 2–4)** of the questions that
most change what you'll write — don't make it a long form, and don't ask what
they've already clearly told you (instead, pre-fill that and just confirm it).
The questions that matter most:

- **Goal / motivation** — what do they want to *do* with this? (ship a feature,
  pass an interview, automate a work task, general curiosity). Decides which
  examples and which depth to emphasize.
- **Current level** — complete beginner, knows the basics, or intermediate
  filling gaps. Decides where you start and what you can assume.
- **Application context** — their field/job or a concrete scenario (e.g. "prompts
  for customer-support bots"). Real tutorials use the learner's own domain for
  examples; this is what makes it feel personal.
- **Scope / focus** — if the topic is broad ("AI", "investing"), ask one question
  to pin down the angle so you go deep on what they care about, not everything.
- **Format & length** — confirm Markdown (default) vs. Word/PDF and roughly how
  deep, only if they haven't already said.

Pick the 2–4 that are actually unknown for *this* request and ask those. Frame the
options concretely (e.g. for level: "完全新手 / 懂一点基础 / 已有经验想补缺口").

### Then probe for *specific* gaps — and research what to probe about

A coarse "beginner / intermediate" label isn't enough to know what to actually
write. The move that makes a tutorial feel like it was made for *this* person is
to **name concrete sub-topics, techniques, tools, and common sticking points from
the subject and ask whether they already know them or struggle with them** — "do
you already use few-shot examples?", "does the output randomness frustrate you?",
"have you hit the JSON-parsing-fails problem?". Each answer tells you what to
**skip** (so you don't bore them) and what to **go deep on** (their real gaps).

**Don't invent these probes from your own head — go find what real learners
actually ask.** Before asking, run a quick web search like "common questions
about <topic>", "<topic> FAQ", "what beginners get wrong about <topic>",
"<topic> common mistakes / pain points". Forums, Q&A sites, and "top FAQs"
articles will surface the recurring questions and stumbling blocks for this exact
subject. Turn those real, high-frequency pain points into your options. This does
two things: it makes the questions concrete and relatable ("oh, that's exactly my
problem"), and it ensures you're covering what the field considers the hard parts,
not just what you happened to think of. This research also feeds Step 2.

**Ask enough to get direction — but respect their time (order and ceiling).**
There's a real tension here: too few questions and the tutorial is generic; too
many and you've built an annoying form the learner abandons before any value
appears. Resolve it with order and a ceiling:

- **Order so they never wait on a blank screen.** Ask the **basics round first**
  (goal / level / context) — it needs no research, so it appears instantly. *Then*
  do the quick web scan for common pain points, and ask the **diagnostic round**
  (techniques they know + researched pain points + constraints + their own
  question). This way the learner is answering within seconds, and the search
  happens while they're busy with round one.
- **Cap it.** Aim for about **two rounds, ~6–8 questions total**, no more. Each
  `AskUserQuestion` call holds up to 4 grouped questions and `multiSelect: true`
  lets them check several at once, so two calls is plenty. If after the basics the
  picture is already sharp, **skip the diagnostic round** — don't ask for asking's
  sake.
- **Always leave the exit open.** Make clear they can bail anytime — e.g. add a
  "其他/直接开始写" option or note "(觉得够了可以让我直接开始)". If they show
  any impatience, or pick "just write it", **stop asking immediately** and proceed
  with what you have. The interview serves the learner, not the other way around.

The diagnostic round should cover: which **sub-skills/techniques** they already
know (so you skip them); which **common pain points** you researched resonate (so
you target them); any **constraints** that shape the material (their stack/tools,
budget, platform, time); and an open question for **their own sticking point**.
Tailor every option to their stated goal (a product-builder gets asked about
JSON-schema output, tool calling, agent design; a customer-service beginner gets
few-shot, role prompting, escalation rules).

**Always give them an open door to steer:** include "有没有你特别想搞懂、或一直
卡住的具体问题?" / "Anything specific you want answered or stuck on?" People often
have a precise itch — a concept that never clicked, one concrete thing they're
building. When they hand you that, it becomes the **spine** of the tutorial.
Honor it: answer their named questions head-on, not buried.

After they answer, reflect their choices back in one line ("好的——给懂基础、做
产品开发的你,已经会 few-shot,重点讲结构化输出、Agent 提示和上下文工程,并解答你
关于成本控制的问题") so they feel heard and can correct you, then go research and write.

The only time to skip the questions is if the user explicitly says something like
"don't ask, just write it" — then proceed with stated assumptions. Otherwise, a
quick interview always beats guessing.

(Note for automated/non-interactive runs where no human can answer: treat the
context in the prompt as the interview answers, state your assumptions in one
line, and proceed.)

**Language:** write the tutorial in the language the user is using. If they wrote
to you in Chinese, the tutorial is in Chinese.

## Step 2 — Research the current state of the topic

This is not optional, and it is what separates this from "write what you already
know." Your knowledge has a cutoff; tools release new versions, APIs change,
prices change, and best practices evolve. Before writing, search the web to
ground the material in what's true *now*.

Aim for several focused searches covering: the latest stable version / current
recommended approach, official documentation, recent (this year) best-practice
guides, common gotchas people report, and — where relevant — pricing or model
names. Prefer primary sources (official docs, maintainers' blogs) over
content-farm rehashes. Fetch the promising pages to read details, not just
snippets.

As you research, keep a running list of the URLs you actually used — you'll cite
them. If a search surfaces that something you "knew" is now outdated, trust the
fresh source and note the change for the learner; being current is a feature they
can't get from an old blog post.

**Verify before you teach — wrong "facts" are the worst failure mode here.** The
whole promise of this skill is current, correct material, so a confidently stated
but wrong version number, price, or API signature does real damage. Two habits:

- **Cross-check anything load-bearing or fast-moving** (version numbers, prices,
  model names, "X is the recommended way") against a **second independent source**,
  ideally the official docs. If two good sources disagree, say so in the tutorial
  rather than picking one silently.
- **Verify code examples actually run.** Prefer patterns straight from official
  docs. When you can, *run the example* in the sandbox (Bash) to confirm it
  executes; if you genuinely can't verify a snippet, mark it clearly (e.g. "未实测，
  以官方文档为准 / untested — confirm before relying"). Plausible-looking code that
  silently fails is exactly what erodes trust.

Treat this as a real step, not an afterthought — it's cheap insurance on the one
thing the learner is trusting you for.

## Step 3 — Write the tutorial

Write to the learner's level and goal throughout. A beginner needs plain language
and more scaffolding; an intermediate learner is bored by basics and wants the
"why" and the edge cases. Use examples drawn from *their* stated context.

Use this structure as the backbone. A ready-to-fill skeleton lives in
`assets/tutorial_template.md` — start from it so you don't rebuild the structure
every time, then adapt section names to the topic and the user's language, drop
sections that genuinely don't apply, and put the learner's named sticking point as
the spine. Keep the overall spine: front matter → conceptual foundation →
progressive hands-on body → reinforcement → reference → what's next.

```
# [Topic]: A Personalized Guide for [their goal/context]

> A one-line note on who this is for, their assumed level, and what they'll be
> able to do by the end. Add the date so the reader knows how current it is.

## How to use this guide
Brief: the learning path, estimated time, and how to work through it.

## 1. The big picture
Why this matters, the mental model, and how the pieces fit — before any detail.
Ground the learner so later sections have somewhere to land. This is also where
you bring in the **expert's framing**: the higher-level context that a
practitioner carries in their head and that makes the specifics finally click —
e.g. the macroeconomic backdrop (inflation, interest rates, cycles) behind
personal investing, the information-retrieval theory behind RAG, the model of how
LLMs predict tokens behind prompt engineering. The learner didn't ask for it, but
it's the difference between memorizing steps and actually understanding.

## 2..N. [Progressive lessons]
The core, broken into a sensible learning sequence from foundational to
advanced. Each lesson:
  - explains the concept in plain language, then shows it
  - includes a concrete, runnable/usable example in the learner's domain
  - calls out *why* it works, not just the steps
  - ends with a short "Try it yourself" exercise
For more advanced learners, move faster and go deeper; for beginners, add more
worked examples and check-ins.

## Exercises & solutions
Hands-on tasks of increasing difficulty, each with a worked solution and a short
explanation. Learning sticks through doing, so make these real, not trivia.

## Quick self-check (Quiz)
A handful of questions per major section to let the learner verify they got it.
Provide an answer key with brief explanations.

## Cheat sheet
A compact, scannable reference of the key commands / syntax / patterns /
prompt templates — the thing they'll keep open in another tab.

## Glossary
Plain-language definitions of the key terms introduced.

## Common pitfalls
The mistakes people actually make, why they happen, and how to avoid them. This
is high-value: it's the hard-won stuff that's missing from most intro material.

## Where to go next & resources
Recommended next steps tied to their goal, plus a curated list of the best
books / courses / docs / tools — with links.

## Sources
The current references you used, as clickable links, so the learner can verify
and dig deeper.
```

Quality bar that makes this worth keeping:

- **Depth with momentum.** In-depth does not mean padded. Every section should
  earn its place; cut filler. A learner can feel the difference between thorough
  and bloated.
- **Show, don't just tell.** Concrete examples, code/prompt blocks, and small
  tables beat paragraphs of abstraction. Make examples copy-pasteable.
- **Personalized examples.** Reuse the learner's domain in examples wherever you
  can — it's the whole reason they came here instead of reading the docs.
- **Bring your own expertise; reach for genuinely higher-level knowledge.** A
  great tutor has a point of view and connects the topic outward and downward, not
  just restating it. Don't settle for framing that hugs the surface of the
  subject — deliberately pull in knowledge from a *higher or adjacent* level.
  Three kinds are especially powerful, and you should actively look for each:
    - **The deeper mechanism / first principles** — go one layer *below* the how-to
      and explain *why it works at a more fundamental level*. (E.g. don't just say
      "long context confuses the model"; explain the attention mechanism and the
      "lost in the middle" effect that cause it. Don't just say "diversify"; explain
      the interest-rate and inflation regime underneath it.)
    - **Adjacent-discipline parallels** — map the new idea onto a mature concept
      from a field the learner likely already respects, so they get it fast.
      (E.g. subagent isolation ≈ *separation of concerns* / *sandboxing / blast
      radius* from software engineering; parallel dispatch ≈ *fork-join / map-reduce*;
      compounding ≈ exponential growth.)
    - **Cross-domain mental models** — a vivid analogy from another domain that
      makes the structure click (e.g. context window ≈ human *working memory*,
      7±2; an agent's tools ≈ a craftsman's bench).
  The test: does the learner come away understanding the *landscape and the why*,
  not just the steps? Volunteer the context they didn't know to ask for. Scale it
  to their level (a sentence for a beginner, a real aside for an intermediate
  learner) and mark each enrichment clearly (a "Bigger picture / 🔭" or "Why this
  works" callout) so it reads as illuminating, never as drift. If your draft's
  higher-level notes only restate the topic in slightly grander words, you haven't
  done this yet — push for the actual mechanism or the cross-field connection.
  **But calibrate to the learner — depth serves them, it's not for showing off.**
  For a beginner, the fundamentals come first and must be solid; keep enrichments
  short, optional-feeling, and clearly skippable (a one-line 🔭 aside they can
  glide past), and never let a higher-level tangent crowd out or intimidate them
  away from the basics they actually came for. The more advanced the learner, the
  more room these asides earn. When in doubt, teach the thing clearly first, then
  add the depth — never the reverse.
- **Current and cited.** Reflect what your research found, and link sources so
  claims are checkable. Flag anything that changes fast (versions, prices).
- **Honest about uncertainty.** If sources disagree or something is genuinely
  contested, say so briefly rather than papering over it.

Write the file to the outputs directory as a `.md` file with a clear name (e.g.
`prompt-engineering-tutorial.md`).

## Step 4 — Deliver in the format they chose, then offer more

Markdown is the default, but **format is one of the interview questions, so honor
what they picked** — if they asked for Word or PDF, deliver that as the primary
artifact, not as an afterthought they have to ask for again.

- **Word (.docx):** read the **docx** skill and build the Word version from the
  finished content. **PDF:** read the **pdf** skill. Always write the Markdown
  first (it's the easiest to review and the source for conversion), verify the
  content, then convert. Save the final file(s) and present them with
  `present_files`.
- If they chose Markdown, just save and present the `.md`.

Then offer the natural follow-ons — don't force them, just make them available:

- **The other format:** if you delivered Markdown, mention they can also get a
  Word/PDF version in one step.
- **Spaced-repetition reminders:** offer to set up a review schedule (via the
  **schedule** skill) — e.g. a quick quiz or recap pushed to them in a few days
  and again in two weeks, since spacing is how learning actually consolidates.
- **Go deeper / adjust:** offer to expand a section into its own deep-dive, add
  more exercises, or re-pitch the level if it landed too easy or too hard.

## A few brainstormed extras worth offering when they fit

These aren't mandatory, but they make the tutorial more useful when the topic
or learner calls for them:

- A **learning-path / roadmap view** at the top (a simple sequence or checklist)
  so the learner sees the whole journey and can track progress.
- A **capstone project** brief at the end that ties the lessons together into one
  realistic thing they build — great for skills that are best learned by making.
- **"From your level" callouts** that acknowledge what an intermediate learner can
  skip vs. what a beginner should not.
- **Annotated worked examples** where you narrate your reasoning line by line, not
  just present the final artifact.

Keep the learner's goal as the compass for all of these: include what moves them
toward it, skip what doesn't.
