---
name: backspaceoddity-jtbd
description: Competitive analysis through the Jobs-to-be-Done lens. Instead of "who are our direct competitors", asks "what is the customer hiring this product to do, and what else could they hire for the same job". Use this skill when the user requests a JTBD analysis, a competitor map through the customer's job, a breakdown of substitutes/alternatives, competitive mapping, or phrases the question as "who really competes for this customer". Triggers (RU/EN) — "JTBD", "jobs to be done", "конкурентный анализ", "substitutes", "competitive mapping", "карта конкурентов", "альтернативы клиента", "who really competes for this customer", "core functional job".
---

# JTBD competitive analysis

Clayton Christensen / Tony Ulwick lens. The question is not "who are the
direct competitors?" but "what is the customer hiring this product to do,
and what else could they hire for the same job?"

Core functional job formula — per
<https://jobs-to-be-done.com/defining-the-customers-core-functional-job-to-be-done-7914eb5b6011>.

---

## Input — intake protocol

All fields are optional. The skill works with whatever it has. Decide
first whether to **skip intake** (go straight to analysis) or **run
intake** (collect missing pieces interactively).

**Skip intake if** the user's prompt already contains substantive
context: a product/customer paragraph, a pasted brief, a deck excerpt,
or an explicit "use what you have / run on minimal info" instruction.

**Run intake if** the prompt is bare (e.g. just "run JTBD",
"competitive analysis please", "/bso-jtbd-competitive") or contains
only one or two pieces of info and would clearly benefit from more.

### Intake calls

**CALL THE `AskUserQuestion` TOOL THREE TIMES, IN ORDER. DO NOT print
the questions as plain text — use the tool.**

**Language:** detect the user's language from their initial prompt
(or default to English). All question and option text below is a
template — localize to RU if the user is writing in Russian.

**Default chip shape** — most questions use only two technical chips
(plus the auto-provided **Other** for the substantive answer):

- Chip 1: **"Skip — infer from context"**
  description: `Don't ask further; I'll infer this field from the rest.`
- Chip 2: **"Don't know — mark as unknown"**
  description: `Leave a gap; the analysis should flag this field as unknown rather than guess.`

Three questions break this default and carry real categorical chips —
flagged explicitly below (Q0 gate, Q2 buyers, Q9 competitive advantage).

`multiSelect: false` unless the question is marked otherwise.

---

#### Batch 1 (4 questions) — gate, product, buyers, alternatives

**Q0. How well do you know the customer side?** (`header: Depth`)
Real categorical chips — pick one. Other = paste a written brief.

- "Know the product; customer side is hypothesis-level"
  description: `Strong product knowledge, customer hypotheses only.`
- "Have signals — sales data, conversations — but no interviews"
  description: `Customer signals from indirect sources, no direct research.`
- "Strong evidence — interviews, retention, sales patterns"
  description: `Direct customer research and behavioral data.`
- Other → if Other contains a substantial brief (a paragraph or more
  pasted into Other), treat that as the answer to Q1–Q9 collectively
  and **skip Batches 2 and 3**. Run the analysis on the brief.

**Q1. What does the product/service do?** (`header: Product`)
Default chip shape. Substantive answer via Other (2–4 sentences).

**Q2. Who buys it — roles and positions of ideal customers?**
(`header: Buyers`, `multiSelect: true`)
Real categorical chips — pick all that apply. Other = specific titles.

- "Founders / CEOs"
- "Heads of function (sales / marketing / ops)"
- "Individual contributors / practitioners"
- "Procurement / finance"

**Q3. What do they use instead today — competitors, manual processes, adjacent tools?**
(`header: Alternatives`)
Default chip shape. Substantive answer via Other.

---

#### Batch 2 (4 questions) — pain, friction, triggers, decision

**Q4. Which customer problems are most painful — the ones they pay or want to pay to solve?**
(`header: Pain`)
Default chip shape. Other = free text.

**Q5. What don't your prospective customers like about their current solution?**
(`header: Friction`)
Default chip shape. Other = free text. (Reframed to make the subject
explicit — it's the customers' dissatisfaction, not yours.)

**Q6. Best customers — those who bought fast, without lots of questions or heavy discounts. What was the trigger? What context were they in at the time?**
(`header: Triggers`)
Default chip shape. Other = trigger + context, free text.

**Q7. Customer decision-making process — does the customer decide alone or align with colleagues, are there cycles (budgeting → search → evaluation → negotiation → decision)?**
(`header: Decision`)
Default chip shape. Other = free text.

---

#### Batch 3 (2 questions) — stack + edge

**Q8. Customer tech stack — what are they deeply embedded in, what does the product need to integrate with (e.g. Microsoft suite)?**
(`header: Stack`)
Default chip shape. Other = free text.

**Q9. Competitive advantage — what do you have that competitors can't copy, what value do you create that they can't?**
(`header: Edge`, `multiSelect: true`)
Real categorical chips — pick all that apply. Other = your specific edge.

- "Unique data / access"
- "Network effects"
- "Deep integration into the stack"
- "Speed / team execution"

---

### Mapping answers to the analysis

After the intake completes (or after Q0 short-circuits with a brief),
normalize each field:

- **Other text** → treat as the substantive answer for that field.
- **Real categorical chips** (Q0, Q2, Q9) → record the selection(s)
  alongside any Other text. Both can be present.
- **"Skip — infer from context"** → no gap. Infer from other inputs and
  the JTBD lens; do not mention this field is missing.
- **"Don't know — mark as unknown"** → in the analysis, explicitly state
  `unknown` for this field and do not invent details. Base any inferences
  on the remaining inputs.

**Q0 depth selection** modulates confidence in the analysis: a
hypothesis-level answer means substitutes and triggers are
plausibility-ranked, not evidence-ranked — say so explicitly in the
output if relevant.

If the user abandons the intake mid-way (closes the question, switches
topic) — proceed with whatever was collected, treat unanswered fields as
`Skip — infer from context`, and run the analysis. **Do not block on
collecting all answers.**

---

## Method

### 1. Main job (core functional JTBD — strict formula)

Write the main job using a three-part formula, in this exact order:

    [Verb] + [Object of the Action] + [Contextual Clarifier]

- **Verb** — a single action verb in the infinitive (minimize, restore,
  organize, prepare, validate). Not "use", not "have", not "be able to".
- **Object** — what the action is performed on. A specific noun phrase.
  Not "things" or "stuff".
- **Contextual clarifier** — when / where / under what constraint. This
  is what turns a capability into a job. Often starts with "when",
  "before", "while", "without", "for", "during".

**Correct shape:**

- "Minimize handoff loss when transferring a lead from marketing to sales"
- "Restore confidence in a forecast before sharing it with the board"
- "Hire a person in a new jurisdiction without setting up a local entity"
- "Cut competitive-analysis prep time without spending a week on research"

**Wrong shape — do not produce these:**

- "Use a CRM"                 — no real verb, no clarifier
- "Manage leads"              — no clarifier
- "Be more productive"        — no object, no clarifier
- "Sales productivity"        — not a job statement at all

If you can't infer a credible clarifier, state the most plausible one
("when …"). **Do not drop the third part.**

### 2. Substitutes (4–6 items, spread across categories)

List 4–6 substitutes the customer could hire for the same job. Spread
them across categories — aim for variety, do not stack everything into
`direct_competitor`:

- `direct_competitor` — another vendor solving the same job the same way
- `indirect_alternative` — a different product category that does the same job
- `status_quo` — what the customer uses today and isn't actively trying
  to replace (often the strongest substitute). **Pattern worth watching:**
  the sitting incumbent that the buyer is mildly dissatisfied with but
  hasn't bothered to leave is its own substitute row even when the same
  vendor appears as a `direct_competitor`. Switching inertia is the
  barrier, not feature differentiation.
- `manual_workaround` — spreadsheet, doc, human process, no tool
- `do_nothing` — accepting the pain, postponing, working around

For each substitute, write four short phrases:

- **why_hired** — which part of the job this substitute serves
- **where_it_wins** — where it beats the asking company
- **where_it_loses** — where the asking company can take the job back
- **switch_trigger** — the concrete event that would push the customer
  off this substitute toward the asking company's product. Not "they grow"
  or "they need more features" — a specific event ("their auditor flags
  contractor misclassification", "their current vendor drops the country
  from its routing list", "a peer recommends switching after a failed
  audit").

If no specific brand is implied in the input, use category labels
("in-house Google Sheet", "manual outreach via LinkedIn").
**Do not invent vendors.**

### 3. Discovery questions (three)

End with three falsifiable questions for the next round of customer
calls. Each validates a different load-bearing assumption:

- **DQ1 — main job.** Does the customer recognize the framing? Phrased
  so a real customer would either nod or push back — not give an essay.
- **DQ2 — strongest substitute.** Names a specific substitute or
  category and asks about recent decisions. Format: "Of the last N
  decisions / deals / hires, how many ...".
- **DQ3 — trigger.** Asks about the timing of recent purchases or
  near-misses to validate the "why now?" hypothesis.

Not "go talk to customers" — questions that get binary or counting
answers. If you can imagine the same answer from any customer in any
category, rewrite.

---

## Output format — produce exactly this, no preamble

Match the language of the input (EN input → EN output, RU input → RU output).

A section may be **omitted** when its feeding inputs are all `Don't know — mark as unknown` AND nothing in the rest of the intake can fill it. **Never invent content to keep a section alive.** When a section runs on `Skip — infer from context` answers, no special marker; when it runs on `Don't know`, flag explicitly with `unknown` on the relevant line.

~~~
**Company:** <one-sentence normalized summary — from Q1>

**Buyers + decision dynamics:** <roles from Q2; who decides / who can block / where deals stall — from Q7. One to three sentences. If the buyer profile is bimodal (e.g. solo founder at small companies + committee at larger companies), structure as two short sentences — one per profile — so the dynamics for each are visible.>

> **Confidence:** <one line grounded in Q0. Example phrasings: "Hypothesis-level — neither product nor customer side has been validated; the analysis is a starting frame, not a playbook." / "Signal-level — based on sales conversations and operational data but no structured interviews; substitutes and triggers are plausibility-ranked, not evidence-ranked." / "Evidence-backed — based on customer interviews and retention patterns; treat the analysis as testable claims, not facts.">

---

**Main job:** <Verb + Object + Contextual Clarifier>

**Related jobs surfacing around it:**
- <adjacent job that shows up when the main job is in motion>
- <another related job>
- <optional third>

---

**Substitutes — what else they could hire**

| Substitute | Category | Why hired | Where it wins | Where it loses | Switch trigger |
|---|---|---|---|---|---|
| <name>     | <category> | <one phrase> | <one phrase> | <one phrase> | <one phrase> |
| <name>     | <category> | <one phrase> | <one phrase> | <one phrase> | <one phrase> |
| ...        | ...        | ...          | ...          | ...          | ...          |

(4–6 rows, spread across at least 3 of the 5 categories: `direct_competitor`, `indirect_alternative`, `status_quo`, `manual_workaround`, `do_nothing`.)

---

**Why now? — triggers that open the purchase window**

- <a specific operational or market event from Q6 + Q5 + Q3>
- <another event>
- <another event>

3–5 bullets. Concrete events, not "the company grows" or "they expand internationally". Examples of the right shape: "their EOR suspends payouts in a key country", "an auditor flags contractor misclassification", "their bank drops a destination from its routing list".

If Q6 was `Don't know — mark as unknown`, add an italic note immediately after the bullets: *"Q6 was unknown — these triggers are inferred from Q3 (alternatives) and Q5 (friction) and need interview validation."* This keeps the section useful without overclaiming.

---

**Belief shift / A-ha moment**

2–3 sentences. The one mental flip — grounded in Q4 (pain) and Q9 (your edge) — that moves the customer from "current solution is fine" to "we need this."

---

**Stack integration angle** *(omit entirely if Q8 is unknown)*

1–3 sentences. Where the product slots into the customer's existing stack (Q8) and why that either accelerates the purchase (drops friction) or stalls it (replacement cost). If integration expectations differ across the buyer profiles in *Buyers + decision dynamics*, split by segment — e.g. heavy HRIS/accounting demands at the larger profile, lightweight tolerance at the smaller one.

---

**Discovery questions for the next round of customer calls**

1. **DQ1 (main job):** <validates the framing — would a real customer nod or push back?>
2. **DQ2 (strongest substitute):** <names a specific substitute or category; format "Of the last N ..., how many ...">
3. **DQ3 (trigger):** <asks about timing of recent purchases or near-misses>
~~~

If the analysis surfaced something non-obvious — a blind spot, an unexpected substitute, asymmetric category coverage, a substitute hiding in plain sight — add a short **Observation on top of the frame** block after the discovery questions, 2–4 sentences. Only when there's something worth saying.

---

## Self-check before responding

Before sending, verify:

1. `main_job` contains a verb, an object, and a clarifier. Missing
   clarifier → **rewrite the line**.
2. Substitutes span at least 3 of the 5 categories. All
   `direct_competitor` → **rewrite**.
3. Every substitute row has a populated `switch_trigger` cell that
   names a concrete event, not a generic growth/feature reason. If a
   row reads "they need more features" or "they grow" — **rewrite**.
4. Each table cell is a short phrase, not a paragraph.
5. **Confidence callout is present** and matches Q0: a `hypothesis-level`
   Q0 → callout says so; `evidence-backed` Q0 → callout reflects that.
6. **Three discovery questions**, each addressing a different
   load-bearing assumption (main job, strongest substitute, trigger).
   Each is binary or counting-answerable, not essay-prompting.
7. If input fields are marked `unknown`, that's visible in the analysis
   (no invented details — state the assumption plainly).
8. **Stack integration angle** section is present only when Q8 has
   substance; otherwise omitted entirely (not "Stack: unknown").
