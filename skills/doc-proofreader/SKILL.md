---
name: doc-proofreader
description: "Automatically proofread and QA any document before sharing or publishing. Triggers on: 'proofread', 'proof-read', 'check for errors', 'QA this document', 'review before publishing', 'check for consistency', 'найди ошибки', 'проверь документ', or any request to verify a document for mistakes. Also use this skill proactively when Claude has just finished creating or substantially editing a long document (50+ lines) and is about to present it to the user — run the proofread checklist silently before sharing. Covers: internal consistency (numbers, names, counts), factual accuracy against known context, structural integrity, language quality, and formatting. Works on .md, .docx, .html, .txt files and in-conversation text blocks."
---

# Document Proofreader & QA Skill

## Purpose

Catch errors that slip through during iterative document editing — the kind where you update one section but forget to update three other places that reference the same fact. This skill runs a structured multi-pass review before any document is shared.

## When to run

1. **Explicitly requested:** User asks to proofread, QA, or check a document
2. **Proactively before sharing:** When Claude has just created or heavily edited a document (50+ lines) across multiple tool calls, run the checklist before calling `present_files`. Do this silently — don't announce the proofreading unless errors are found
3. **Before publishing:** Any document going to a client, partner, or public audience

## The Proofread Checklist

Run all six passes in order. Each pass has a specific focus. Do NOT try to do everything in one read — sequential passes catch more errors.

### Pass 1 — Internal Consistency (numbers and counts)

Scan for quantified claims and verify they match throughout the document:

- **Counts of items:** If the doc says "three clients" in a header, count the actual items listed. If there are four, that's a bug.
- **Numeric references:** If a number appears in multiple places (team size, budget, percentage), verify all instances match.
- **List lengths:** If the doc says "five patterns" or "six key features", count them.
- **Table row/column counts:** Verify tables have the number of rows/columns implied by surrounding text.
- **Cross-references:** "As described in Section 2" — does Section 2 actually describe that?

**Action:** For each quantified claim, find ALL other places in the document that reference the same quantity. Flag mismatches.

### Pass 2 — Entity and Name Consistency

- **Names:** Are people, companies, products, and places spelled consistently? (e.g., "AcmeCorp" vs "ACME" vs "Acme Corporation")
- **Terminology:** Is the same concept always called the same thing? (e.g., don't alternate between "context layer" and "knowledge layer" unless intentional)
- **Acronyms:** Defined on first use? Used consistently after?
- **Roles and titles:** If someone is called "CEO" in one place and "founder" in another, is that intentional?

### Pass 3 — Factual Accuracy Against Known Context

Cross-check claims against information available in:

- **The current conversation history** — has the user corrected something that's still wrong in the doc?
- **User memories** — does the doc contradict known facts about the user or their business?
- **Earlier sections of the same document** — does the conclusion contradict the introduction?
- **Source documents** — if the doc was built from specific sources (Notion pages, web search results), do citations and references hold up?

**Common traps:**
- Using placeholder data that was meant to be replaced
- Referencing a decision that was later reversed in conversation
- Carrying forward an assumption the user explicitly corrected

### Pass 4 — Structural Integrity

- **Headers match content:** Does each section deliver what its header promises?
- **Logical flow:** Do sections follow a logical sequence? Are there abrupt jumps?
- **Orphaned references:** Does the doc reference sections, figures, or appendices that don't exist?
- **Duplicate content:** Are there paragraphs or sections that say essentially the same thing?
- **Incomplete sections:** Any section that feels cut off or unfinished?

### Pass 5 — Language Quality

- **Grammar and spelling** in all languages present in the document
- **Tonal consistency:** Does the doc maintain the same register throughout? (e.g., don't mix casual chat with formal business language)
- **Sentence clarity:** Flag sentences that are ambiguous or require re-reading
- **Repetitive phrasing:** Same word or phrase used too many times in close proximity
- **Language-specific rules:** Apply user preferences (e.g., " — " with spaces, sentence case for headlines, no emojis)

### Pass 6 — Formatting and Presentation

- **Markdown/HTML validity:** Broken links, unclosed tags, malformed tables
- **Table alignment:** Do column headers match their data? Are columns consistent?
- **Code blocks:** Properly fenced? Language tag specified?
- **Whitespace issues:** Double blank lines, trailing spaces, inconsistent indentation
- **File-specific:** For .docx — will it render correctly? For .html — are all tags closed?

## Output Format

After running all six passes, report findings in this structure:

**If errors found:**

```
## Proofread Results

### Critical (must fix before sharing)
- [Pass 1] Line 29: says "three clients" but four are listed (A, B, C, D)
- [Pass 3] Line 85: states "$10M revenue" but user corrected this to unspecified

### Minor (recommended fixes)
- [Pass 2] Line 45: "AcmeCorp" appears as "ACME" once on line 112
- [Pass 5] Line 67: "очень" repeated three times in one paragraph

### Observations (no action needed)
- Document uses both "context layer" and "knowledge layer" — appears intentional based on context
```

Then fix all Critical items automatically, flag Minor items for user decision, and note Observations.

**If no errors found:**

Do not output a proofreading report. Simply present the document as normal.

## Proactive Mode (silent pre-publish check)

When Claude has built or edited a document across 3+ tool calls and is about to `present_files`:

1. Run Passes 1-4 silently (consistency, entities, facts, structure)
2. If Critical errors found → fix them, then present with a brief note: "Caught and fixed: [description]"
3. If only Minor or no errors → present normally, no mention of proofreading
4. Skip Passes 5-6 in proactive mode unless the document is client-facing

## Multi-language Support

This skill works in any language present in the document. For mixed-language documents (e.g., Russian and English), apply language-specific grammar rules per section. Pay special attention to:

- Transliteration consistency (e.g., "Сирадж" vs "Siraj" — both should be present or one chosen)
- Code-switching points — where the document transitions between languages, ensure the transition is clean
- Quotation mark styles matching the language section they're in
