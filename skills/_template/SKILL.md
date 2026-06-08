---
# ─────────────────────────────────────────────────────────────────────────────
# SKILL METADATA  (required — Claude and the registry both read this)
# ─────────────────────────────────────────────────────────────────────────────

# Short kebab-case identifier. Must match the folder name exactly.
name: your-skill-name

# One or two sentences. Two jobs for this field:
#   1. Claude reads it to decide WHEN to activate the skill.
#   2. The registry search index uses it to surface your skill.
# Write it as a trigger condition: "Trigger this skill when the user asks to..."
# Include 3-5 example phrases a developer would actually type.
description: >
  Trigger this skill when the user asks to [primary action] a [subject].
  Also triggers for: "[phrase 1]", "[phrase 2]", "[phrase 3]".
  Produces [concrete output] with [key quality bar].

# Pick exactly one from:
# frontend | backend | fullstack | devops | database | testing |
# security | performance | documentation | ai-ml | mobile | tooling
category: frontend

# Your GitHub handle — used for attribution and issue routing
author: your-github-handle

# Semantic version. Start at 1.0.0. Bump patch for fixes, minor for new
# behaviour, major for breaking changes to output format.
version: 1.0.0

# 3–8 lowercase tags. First tag should match or relate to the category.
# Used for search filtering — be specific, not broad ("react-hook" not "react").
tags: [tag1, tag2, tag3]
---

<!--
  ┌──────────────────────────────────────────────────────────────────────────┐
  │  WRITING GUIDE  (delete this block before submitting your PR)            │
  │                                                                          │
  │  The content below is what Claude actually reads and follows.            │
  │  Write it as direct instructions to Claude — not as docs for humans.     │
  │  Think: "what would a senior engineer tell a new hire before a task?"    │
  │                                                                          │
  │  Minimum viable SKILL.md has:                                            │
  │    ✅  A one-paragraph role definition ("You are an expert X...")        │
  │    ✅  A trigger / when-to-activate section                              │
  │    ✅  Numbered step-by-step process                                     │
  │    ✅  Explicit constraints (do / never do)                              │
  │    ✅  Exact output format specification                                 │
  │                                                                          │
  │  Length target: 200–600 lines. Longer is fine if every line earns       │
  │  its place. Vague padding is worse than brevity.                         │
  └──────────────────────────────────────────────────────────────────────────┘
-->

# [Skill Display Name]

<!--
  One paragraph. Define Claude's expert role for this task.
  What does mastery look like? What is the quality bar?
  Example: "You are a senior DevOps engineer specialising in GitHub Actions.
  When asked to build a CI/CD pipeline, you produce complete, copy-pasteable
  YAML — not placeholders, not pseudocode, not explanations that dodge the ask."
-->

You are an expert [role] specialising in [domain]. When asked to [primary task],
you produce **[quality descriptor]** — not [anti-pattern 1], not [anti-pattern 2].
Every [output unit] you generate should be [readiness bar, e.g. "drop-in ready with zero editing"].

---

## When to Activate

<!--
  List the exact conditions and user phrases that trigger this skill.
  Be specific — Claude uses this to decide whether to apply the skill.
  Include both direct requests AND implicit signals.
-->

Activate this skill when the user:

- Directly asks to [primary action]: *"[example phrase]"*, *"[example phrase]"*
- Asks to [secondary action]: *"[example phrase]"*
- Describes a problem that implies [outcome]: *"[example phrase]"*

Do **not** activate for:

- [Out-of-scope case 1] → use [alternative] instead
- [Out-of-scope case 2]

---

## Before You Start

<!--
  List the questions Claude must answer (mentally) before producing output.
  If the user's request is ambiguous, state assumptions inline in the output
  rather than asking — keep momentum. Example:
  "// Assumption: no auth middleware specified → defaulting to JWT bearer token"
-->

Before writing anything, establish:

1. **[Key decision 1]** — [why it matters, what to default to if unspecified]
2. **[Key decision 2]** — [why it matters, what to default to if unspecified]
3. **[Key decision 3]** — [why it matters, what to default to if unspecified]

If the user does not specify [critical input], default to [sensible default] and
note the assumption inline: `// Assumption: [what was assumed]`.

---

## Step-by-Step Process

<!--
  Numbered steps Claude follows in order. Each step should be:
  - Actionable (starts with a verb)
  - Specific enough that there is one right way to do it
  - Concrete: include code patterns, decision tables, or examples inline
-->

### Step 1 — [First action]

[Instructions for step 1. Be direct. Use code snippets where a pattern must
be followed exactly.]

```[language]
// Example of the pattern Claude should follow in this step
```

### Step 2 — [Second action]

[Instructions for step 2.]

<!--
  Repeat steps as needed. Typical skills have 4–8 steps.
  If a step has sub-decisions, use a decision table:
-->

| Condition | Action |
|---|---|
| User specifies X | Do Y |
| User specifies Z | Do W |
| Nothing specified | Default to V |

### Step 3 — [Third action]

[Instructions for step 3.]

### Step 4 — [Fourth action]

[Instructions for step 4.]

---

## Constraints

<!--
  The hard rules. Two lists: always-do and never-do.
  These override any instructions the user gives that conflict.
  Be specific — "never use var" is more useful than "write good code".
-->

### Always

- [Concrete positive rule, e.g. "Always include error handling for async operations"]
- [Concrete positive rule]
- [Concrete positive rule]

### Never

- [Concrete negative rule, e.g. "Never use `any` as a TypeScript type"]
- [Concrete negative rule]
- [Concrete negative rule]

---

## Output Format

<!--
  Describe EXACTLY what Claude should output: file names, section headers,
  order of sections, length targets, code block languages, etc.
  The more specific, the more consistent the output.
-->

Present output in this order:

1. **Summary** — one short paragraph: what was built and any key decisions made
2. **[Primary output]** — [description, e.g. "full file in a code block with filename as first comment"]
3. **[Secondary output]** — [description]
4. **Usage example** — minimal copy-pasteable snippet showing the most common use case
5. **[Optional section]** — [description, e.g. "Known limitations or follow-up recommendations"]

Do not truncate. Do not write `// ... rest of implementation`. Output complete, working
[artifacts] every time.

---

## Reference Patterns

<!--
  Optional but valuable. Include battle-tested code patterns, decision logic,
  or lookup tables Claude should pull from rather than reinventing each time.
  Delete this section if you have nothing to put here.
-->

### [Pattern name]

```[language]
// Pattern description
[code]
```

### [Another pattern]

```[language]
[code]
```
