---
name: self-qa
description: >
  Universal quality gate that runs before presenting ANY deliverable to the user.
  Uses independent review lenses instead of a monolithic checklist.
  Covers code, architecture, documents, presentations, and mixed output.
  Should run automatically at delivery time; can also be invoked manually as a check before sharing.
---

# Self-QA Skill
## Universal pre-delivery quality gate

**Core principle:** The user should never be the first pair of eyes on mechanical errors.
Self-QA catches what a systematic check can find. The user's feedback should be about
judgment calls and strategic direction — not typos, broken tests, or over-engineered abstractions.

---

## When to trigger

Self-QA activates automatically when you have completed any of:
- Writing or refactoring code (more than a trivial one-line fix)
- Making an architectural or technical strategy decision
- Building or modifying slides in Figma
- Creating or editing a document (Notion, .md, .docx)
- Generating any artifact the user will review

**Do NOT ask "should I run QA?" — just do it.**

---

## How it works: Lenses, not a checklist

Instead of one monolithic checklist, Self-QA applies **independent review lenses**.
Each lens looks at the output from a different angle. Apply only the lenses relevant
to the output type.

### Lens selection by output type

| Output type | Lenses to apply |
|---|---|
| Code (new feature, refactor) | Correctness → Architecture → Security → Spec Compliance |
| Architecture / tech strategy | Architecture → Spec Compliance |
| Bug fix | Correctness → Security |
| Presentation (Figma) | Visual → Content → Spec Compliance |
| Document (Notion, .md, .docx) | Content → Spec Compliance |
| Client deliverable with data (analysis, strategy, report) | Numbers-Provenance → Content → Spec Compliance |
| Mixed (code + docs) | All relevant lenses for each part |

---

## Lens 1: Correctness

**Purpose:** Does the code actually work? Not "does it look right" — does it *run*?

### Automated (mandatory if project has test/lint infrastructure)
```
[ ] Run the project's own test suite — zero regressions
[ ] Run the project's linter if configured — zero new warnings
[ ] New code has at least one test (happy path + one edge case)
```

### Silent failure check (most important — AI agents skip this)
```
[ ] Trace the primary code path manually: does data flow from input to output?
[ ] Check error paths: do catch blocks actually handle the error or silently swallow it?
[ ] Check async flows: are promises awaited? Are race conditions possible?
[ ] Check state mutations: does the code modify shared state that other code depends on?
[ ] "It works on my machine" test: would this work in production with real data?
```

### Regression check
```
[ ] Read the code you changed — does existing behavior still work?
[ ] If you modified a function signature: grep all call sites, verify they're updated
[ ] If you modified a data structure: grep all consumers, verify they handle the new shape
```

### Claim-verification check

**Purpose:** catch confident claims made without evidence. AI agents tend to report "all done, 100% coverage" while actually verifying only a fraction. This pattern is how silent failures slip into delivery — the claim is un-evidenced, and the user discovers it later.

Scan your forthcoming delivery message for these patterns:

```
[ ] Numeric claims: "100%", "all N built", "N of M done", "coverage ≥ X%"
[ ] Categorical claims: "всё готово", "всё работает", "all ✓", "полностью собрано",
    "успешно завершено", "built successfully", "no issues"
[ ] Gate-passed claims: "ready for production", "passed all checks", "production quality"
```

For each such claim, demand evidence in this session's transcript:

```
[ ] Visual output (Figma component, slide, page, image): at least one screenshot
    per claimed object OR one composite screenshot showing all. If claiming N
    components, there should be ≥N/3 screenshots in transcript. No screenshots
    for a visual claim → REWRITE the claim to what you actually verified.
[ ] Code / logic (function, hook, skill): tests ran + showed pass? Or manual
    trace documented? If neither — rewrite from "works" to "written, untested".
[ ] File / commit claim ("committed", "pushed"): git status / git log output
    in transcript? If no — verify before claiming.
[ ] Decision / structural claim ("registered in config", "added to YAML"): file
    read-back in transcript showing the new state? If no — verify.
```

**Rewrite rule**: when evidence is missing, rewrite the claim in the delivery. Instead of *«Built 35 components, all working»* → *«Built 35 components; verified 5 visually (list them); the remaining 30 are structurally created but visual verification pending — run screenshot pass before relying on them»*. The user gets an honest picture of what's real.

**Hard stop rule**: if you wrote a blanket claim like "100% coverage" and cannot back it with evidence for every item counted, **block your own delivery and go verify first**. Better to be 5 minutes slower than to deliver a confident-but-wrong claim.

**Confidence threshold:** Flag only issues you're ≥80% sure about. Suppress speculative concerns.

---

## Lens 2: Architecture

**Purpose:** Is the solution the right size for the problem? Not too much, not too little.

### Over-engineering detector
```
[ ] Count layers of abstraction introduced. More than the task requires? Remove them.
[ ] Any new helper/utility that's used exactly once? Inline it.
[ ] Any new config/feature flag for a non-configurable thing? Remove it.
[ ] Any "just in case" error handling for scenarios that can't happen? Remove it.
[ ] Any interface/type created for a single implementation? Remove it unless it's a system boundary.
[ ] Did you add extensibility nobody asked for? Remove it.
```

### Under-engineering detector
```
[ ] Is there a known library/pattern that solves this better than your custom code?
[ ] Are you repeating logic that should be shared? (But: 3 similar lines > premature abstraction)
[ ] Did you skip validation at a system boundary (user input, external API, file I/O)?
[ ] Is there a clear failure mode you didn't handle that a user would actually hit?
```

### Decision audit (for architecture/strategy choices)
```
[ ] State the tradeoff explicitly: what did you choose, and what did you give up?
[ ] Is there a simpler approach that achieves 90% of the benefit? If yes — switch to it.
[ ] Does this decision lock in something hard to reverse? If yes — flag it to the user.
[ ] Would you be comfortable explaining this choice to someone in 6 months?
```

**Hard gate:** If any architecture check reveals the solution is structurally wrong for the problem
(wrong pattern, wrong abstraction level, wrong boundaries) — stop and restructure before delivering.
Do not present a deliverable you know is architecturally unsound.

---

## Lens 3: Security

**Purpose:** OWASP top-10 and common AI-generated vulnerabilities.

```
[ ] No hardcoded secrets, tokens, API keys, or credentials (grep for patterns)
[ ] No SQL injection: all queries use parameterized statements
[ ] No XSS: all user-provided content is escaped before rendering in HTML
[ ] No command injection: no user input passed to shell commands
[ ] No path traversal: file paths validated/sanitized
[ ] No insecure deserialization of untrusted data
[ ] Auth/authz checks present at system boundaries
[ ] Sensitive data not logged or exposed in error messages
[ ] Dependencies: no known-vulnerable versions introduced
```

**Applies to:** Any code that touches user input, external APIs, file I/O, or auth.
Skip for pure internal logic with no external surface.

---

## Lens 4: Spec Compliance

**Purpose:** Does the output match what was asked for?

```
[ ] Re-read the user's request (scroll up if needed). Does the output address it?
[ ] Nothing was added that wasn't asked for (no bonus features, no extra refactoring)
[ ] Nothing was missed from the request
[ ] If there was a brief or spec: every requirement has a corresponding piece of output
[ ] Terminology matches the user's language (don't rename their concepts)
```

**This is the simplest lens but catches the most common AI failure mode:**
solving a slightly different problem than what was asked.

---

## Lens 5: Visual (Presentations)

**Purpose:** Slides and visual deliverables look correct and follow brand guidelines.

### Automated checks (via Figma Plugin API, when applicable)
```
[ ] Font size audit: walk all TEXT nodes, extract fontSize
    - No text below 14px (absolute floor)
    - No body text below 20px on light backgrounds
    - No body text below 24px on dark backgrounds
    - Accent text on dark bg ≥ 24px bold
[ ] Font family check: all text uses brand-specified fonts (no system fallbacks)
[ ] Distinct sizes: ≤ 5–7 distinct font sizes across deck
[ ] Frame dimensions: match the target spec (e.g., 1920×1080 for 16:9 decks)
```

### Visual checks (via screenshots of 3–5 representative slides)
```
[ ] Text doesn't overflow frames or cards
[ ] Card padding is visually consistent (≥ 24px)
[ ] Margins from slide edges are consistent (≥ 80px)
[ ] Color usage matches brand tokens
[ ] Visual hierarchy reads correctly (title > subtitle > body > caption)
[ ] Whitespace ≥ 40% of slide area
```

### AI-slop scan — 8-pattern checklist

Apply silently on any visual deliverable (HTML/CSS landings, slides, web designs). Pattern hit → fix before delivery, do not show user.

```
[ ] Pattern 1: Aggressive gradients
    Avoid: rainbow blends / 3+ color gradients / neon-on-neon on hero/buttons/large surfaces
    Use: flat color OR subtle on-tone gradient (single hue, low contrast)

[ ] Pattern 2: Emoji-as-decoration
    Avoid: 🚀 prepending headlines, repeated decorative emoji («✨ Welcome», «🎉 Launch»)
    Exception: only if the brand explicitly uses emoji as part of its identity

[ ] Pattern 3: Trendy card pattern «border-radius:12px + border-left:4px solid»
    Avoid: this combination as default card style — overused signal of AI-template
    Use: subtle shadow OR thin all-around border (no left-accent unless purposeful)

[ ] Pattern 4: Hand-drawn SVG illustrations of people/scenes
    Avoid: cartoon-y custom SVG of people/scenes without brand-specific context
    Use: photography / professional illustration / honest placeholder OR no illustration at all

[ ] Pattern 5: Overused default fonts without brand intent
    Avoid: bare Inter / Roboto / Arial / Fraunces without justification
    Use: the brand's canonical font from project context. If no brand font defined,
    flag it to the user before shipping — don't silently default to Inter.

[ ] Pattern 6: Pure #FFFFFF background + #000000 text
    Avoid: exact pure values — looks lazy / off-the-shelf
    Use: subtle warm/cool/neutral tone (e.g. #FAFAFA bg, #1A1A1A text, or brand tokens)

[ ] Pattern 7: Random invented hex colors (#0066CC, #0077DD, similar variants)
    Avoid: multiple slightly-different hues without token-basis (signal of ad-hoc design)
    Use: consolidate to brand tokens; if extending palette — use oklch() for harmony

[ ] Pattern 8: Random spacing values (7px, 15px, 18px, 13px irregular)
    Avoid: arbitrary pixel values
    Use: 4px or 8px scale (4/8/12/16/24/32/48/64) via spacing tokens
```

**Fail action:** silent fix before delivery. List each caught pattern in QA report only if confidence < 80%.

---

## Lens 6: Content (Documents)

**Purpose:** Document is complete, clean, and follows tone guidelines.

```
[ ] All sections from the brief/request are present
[ ] No placeholder text left ("TODO", "TBD", "[fill in]", "...")
[ ] Headings follow a logical hierarchy (H1 > H2 > H3)
[ ] No broken links or references
[ ] No orphan sections (heading with no content below)
[ ] Tone matches the project's voice guidelines (if a brand ToV file exists, follow it)
[ ] For Russian docs: no English words where Russian equivalents exist
[ ] No strawman arguments ("most companies do X wrong...")
[ ] No passive voice when the actor can be named
```

---

## Lens 7: Numbers-Provenance (Data-derived claims in client deliverables)

**Purpose:** Every currency / percentage / count / time-cell number in a client-facing deliverable must trace to a **same-turn** data-pipeline run — not to a stale JSON snapshot or to memory. This catches the failure mode where an agent reads stale source data, weaves a confident narrative around it, and the numbers turn out to be off by ±5–25% from reality.

**Trigger surfaces (auto-fire):**
- File paths matching `*-strategy*`, `*-analysis*`, `*-report*`, `*-deck*`, `*-proposal*`, `/content/*`, `/deliverables/*`
- Notion-write tool calls (`notion-create-pages`, `notion-update-page`, `API-patch-page`, `API-patch-block-children`)

**Patterns to scan for:**
- Currency figures: `[£$€¥]\d{2,}` (e.g., £218k, $1,200, €40,723)
- Percent with decimals: `\d+\.\d+%` (e.g., 48.5%, 34.2%)
- Count-with-noun: `\d{2,5}\s+(guests|customers|visits|covers|bookings|reservations|cohort|checks)` (e.g., 563 guests, 642 customers)
- Named-time-cells: `(Mon|Tues|Wednes|Thurs|Fri|Satur|Sun)day\s+\d{1,2}:\d{2}` (e.g., Friday 18:00)

**Required for each match — at least one of:**
1. **Same-turn pipeline run:** Bash execution of `cross_tabs.py` / aggregation script / SQL query / `pandas` / `polars` / `duckdb` invocation IN THIS TURN that produced the cited number
2. **Fresh data Read:** `Read` tool call against `.json` / `.csv` / `.parquet` / `.xlsx` / `.sqlite` file with **mtime < 24 hours**
3. **Explicit escape for historical/benchmark numbers:** inline HTML comment `<!-- obs: cited-from-<source-id>-<YYYY-MM-DD> -->` in same paragraph as the number. Use this for legitimate citations (public reports, prior-year benchmarks, etc.) — but NOT for current-period analytics

**Hard-gate behavior:**
- ANY ungrounded number in a client-deliverable surface → block delivery. Re-run the pipeline this turn OR add an explicit `<!-- obs: -->` escape.
- Cached >24h treats as ungrounded — even if a Read happened, the file mtime check fails

**Rationale:** the failure class is «derived claim presented as live observation» — agent reads stale source, weaves narrative around it with confident tone, the user's reputation is on the line when the numbers ship and someone double-checks them.

---

## Execution rules

### Speed
QA should take 1–3 tool calls, not 10. If it's taking longer, you're over-engineering the QA itself.

### Confidence scoring
- **High (≥80%):** Fix silently before delivery.
- **Medium (50–80%):** Fix if the fix is safe; otherwise flag to user.
- **Low (<50%):** Do not mention. Suppress speculative noise.

### Hard gates (block delivery until resolved)
These are not flags — they are blockers:
- Tests fail after your changes
- Architecture is structurally wrong for the problem
- Security vulnerability at a system boundary
- Output doesn't match what was asked for (spec compliance failure)
- Ungrounded numeric claim in a client deliverable

### Soft flags (deliver but mention)
- Design/UX judgment calls that could go either way
- Performance concerns that aren't blocking
- Suggestions for improvement beyond the scope of the request

---

## QA Report Format

After completing QA, include a brief summary in your response:

```
**Self-QA** — [lenses applied, e.g. "Correctness + Architecture + Spec"]
Checked: [scope — e.g. "4 files, 127 lines changed"]
Fixed: [what was fixed — or "0 issues"]
Flags: [anything for user judgment — or "none"]
```

Keep it to 2–4 lines. If zero issues: `**Self-QA:** 0 issues across [scope].`

---

## What Self-QA is NOT

- **Not a blocker for judgment calls.** If QA finds something that requires user preference — deliver and flag it.
- **Not a reason to delay.** The QA loop is fast by design.
- **Not a substitute for user review.** You catch mechanical errors. The user catches strategic misalignment.
- **Not recursive.** Don't QA your QA. One pass is enough.

---

## Integration with other skills

- **doc-proofreader**: If the output is a document, run doc-proofreader as part of the Content lens.
- **project-journal**: Log QA findings in LEARNINGS.md if they reveal a pattern worth remembering.
