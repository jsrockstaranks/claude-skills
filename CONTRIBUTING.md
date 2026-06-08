# Contributing to Claude Skills

First off — thank you. Every skill in this repo was built by someone who wanted to ship faster and decided to share how. If a skill saved you an hour, contributing it back saves that same hour for every developer who comes after you.

This guide covers everything: from your first skill idea to a merged PR.

---

## Table of Contents

- [Who Should Contribute](#who-should-contribute)
- [What Makes a Good Skill](#what-makes-a-good-skill)
- [Quick Start](#quick-start)
- [Folder & File Structure](#folder--file-structure)
- [Writing SKILL.md](#writing-skillmd)
- [Writing README.md](#writing-readmemd)
- [Writing Examples](#writing-examples)
- [Category Reference](#category-reference)
- [Testing Your Skill](#testing-your-skill)
- [Submitting a Pull Request](#submitting-a-pull-request)
- [Review Criteria](#review-criteria)
- [Improving an Existing Skill](#improving-an-existing-skill)
- [Requesting a Skill](#requesting-a-skill)
- [Code of Conduct](#code-of-conduct)
- [Need Help?](#need-help)

---

## Who Should Contribute

You don't need to be an AI researcher or a prompt engineer. If you:

- Found yourself writing the same Claude instructions over and over
- Discovered a prompt pattern that consistently produces better output
- Wished Claude knew your team's conventions by default
- Built something with Claude that would have taken you 3x longer without a good system prompt

...then you have a skill worth sharing.

---

## What Makes a Good Skill

A great skill has three properties:

**1. It solves a real, recurring task.**
Not "help me code" (too broad) — but "scaffold a Fastify REST endpoint with Zod validation and a 404/500 error handler" (specific, repeatable, saves real time).

**2. The output quality is noticeably higher with the skill than without.**
Test it. Compare `claude with skill` vs `claude without skill` on the same prompt. If you can't tell the difference, the skill needs more work.

**3. It's scoped to one job.**
A skill that does five things does none of them well. If you're tempted to call it `general-backend-helper`, split it into focused skills.

---

## Quick Start

```bash
# 1. Fork and clone
git clone https://github.com/YOUR_USERNAME/claude-skills.git
cd claude-skills

# 2. Create your skill folder
#    Replace <category> and <skill-name> with your values
mkdir -p skills/<category>/<skill-name>/example

# 3. Copy the template files
cp _template/SKILL.md   skills/<category>/<skill-name>/SKILL.md
cp _template/README.md  skills/<category>/<skill-name>/README.md
cp _template/example/input.md   skills/<category>/<skill-name>/example/input.md
cp _template/example/output.md  skills/<category>/<skill-name>/example/output.md

# 4. Fill in every file (see guides below)

# 5. Test the skill (see Testing section)

# 6. Open a PR
```

---

## Folder & File Structure

Every skill lives in its own folder under `skills/<category>/`:

```
skills/
└── <category>/
    └── <skill-name>/
        ├── SKILL.md          ← Required. Claude reads this.
        ├── README.md         ← Required. Humans read this.
        ├── example/
        │   ├── input.md      ← Required. Sample prompt.
        │   └── output.md     ← Required. Real Claude output.
        └── preview.png       ← Optional. Screenshot for visual skills.
```

### Naming rules

| What | Rule | Good | Bad |
|---|---|---|---|
| Folder name | `kebab-case` | `rest-api-scaffolder` | `RestAPIScaffolder`, `rest_api` |
| Matches metadata | Folder = `name:` in frontmatter | `rest-api-scaffolder` / `rest-api-scaffolder` | `rest-api-scaffolder` / `api-scaffold` |
| No version in name | Version goes in frontmatter | `sql-query-builder` | `sql-query-builder-v2` |
| Descriptive | Name = what it produces | `migration-writer` | `db-helper` |

---

## Writing SKILL.md

This is the file Claude reads. It's the most important file in the folder.

### Frontmatter (required fields)

```yaml
---
name: your-skill-name          # Must match folder name exactly
description: >                 # Trigger condition + example phrases + output description
  Trigger this skill when the user asks to...
category: frontend             # See Category Reference below
author: your-github-handle
version: 1.0.0
tags: [tag1, tag2, tag3]       # 3–8 lowercase tags
---
```

**Writing the `description` field** is the most important part of the frontmatter. Claude reads it to decide when to activate the skill. A weak description leads to missed triggers or over-triggering.

Template:
```
Trigger this skill when the user asks to [primary action] [subject].
Also triggers for: "[phrase 1]", "[phrase 2]", "[phrase 3]".
Produces [concrete output] with [quality bar].
```

Real example:
```yaml
description: >
  Trigger this skill when the user asks to write, create, or generate SQL queries,
  schema definitions, or database migrations. Also triggers for: "query to find X",
  "add a column to Y table", "write a migration that Z". Produces valid, optimized SQL
  with index hints, transaction safety, and rollback scripts.
```

### Body content

Claude reads your SKILL.md body as direct instructions. Write it like you're briefing a senior engineer on a task, not writing API documentation.

**Required sections:**

| Section | Purpose |
|---|---|
| Role definition | One paragraph telling Claude who it is for this task |
| Trigger / when to activate | Exact conditions, explicit out-of-scope cases |
| Step-by-step process | Numbered, actionable, specific |
| Constraints | Hard do/never-do rules |
| Output format | Exact structure Claude must follow |

**Optional but valuable:**

| Section | When to include |
|---|---|
| Decision tables | When there are multiple valid approaches based on user input |
| Reference patterns | Battle-tested code snippets Claude should use as templates |
| Anti-patterns | When the wrong approach is common enough to warn against |
| Accessibility / security checklist | For frontend and security skills |

### SKILL.md quality checklist

Before submitting, verify every item:

- [ ] `name` in frontmatter exactly matches the folder name
- [ ] `description` is written as a trigger condition, includes 3+ example phrases
- [ ] `category` is one of the valid values (see Category Reference)
- [ ] `author` is your GitHub handle
- [ ] `tags` has 3–8 values, all lowercase
- [ ] Role definition paragraph exists and is specific to this task
- [ ] Step-by-step process is numbered and actionable
- [ ] Constraints section has both "Always" and "Never" rules
- [ ] Output format specifies files, section order, and code block language
- [ ] No placeholder text remaining (`[your text here]`, `TODO`, etc.)
- [ ] No truncated code examples (`// ... rest of implementation`)

---

## Writing README.md

The README is for humans browsing the repo. It answers three questions in 60 seconds:

1. What does this skill produce?
2. Should I use it for my task?
3. How do I get started?

### Required sections

**Header** — Emoji, skill name, one-line value prop.

```markdown
# ⚙️ rest-api-scaffolder

> Scaffolds a complete Fastify REST API from a data model — routes, Zod validation,
> error handling, and OpenAPI spec — in one shot.
```

**What It Does** — Concrete list of output files and what each contains.

**When to Use** — Table: trigger phrase → example prompt.

**Output Quality** — Checklist with ✅ for each guarantee. Be specific.

**Usage** — How to load the skill + one example prompt.

**Requirements** — Dependencies and versions the output assumes.

**Customization Tips** — 3–5 prompt additions that meaningfully change output.

### README.md quality checklist

- [ ] Value prop is one sentence and specific (not "helps with React")
- [ ] "What It Does" lists concrete output files, not abstract capabilities
- [ ] "When to Use" has at least 3 rows with real example prompts
- [ ] "Output Quality" checklist has 4+ items, all specific
- [ ] Example prompt in "Usage" would actually produce good output with the skill
- [ ] Requirements table is accurate — lists real version constraints
- [ ] No marketing language ("revolutionary", "powerful", "amazing")

---

## Writing Examples

The `example/` folder is proof the skill works. Maintainers and users will look here first.

### input.md

Write a realistic prompt a developer would type. Rules:

- Specific enough to produce non-trivial output
- Includes at least 3 concrete requirements
- Natural — as if you're actually typing to Claude
- Not so complex that output would exceed ~400 lines of code

Good example:
```markdown
Build a `Pagination` component. Requirements:
- Shows page numbers with prev/next buttons
- Accepts: totalItems, itemsPerPage, currentPage, onPageChange
- Truncates with ellipsis when there are more than 7 pages
- Fully keyboard accessible
- Use Tailwind CSS
```

Bad example:
```markdown
Make a pagination component
```
(Too vague — Claude's output will be inconsistent and not showcase the skill.)

### output.md

Copy-paste **real Claude output**. Don't write it by hand. Rules:

- Run the skill at least **3 times** and use the best result
- Include the **complete output** — no truncating
- If output exceeds ~300 lines of actual code, include a trimmed version in `output.md` AND a `output-full.md` with everything
- The output must match exactly what Claude produced — no editing to make it look better

### preview.png (optional)

For skills that produce visual output (UI components, diagrams, charts), include a screenshot. Requirements:

- PNG format, max 1MB
- Minimum 800px wide
- Shows the rendered output, not the code
- No personal information visible

---

## Category Reference

Place your skill in the most specific matching category:

| Category | What belongs here |
|---|---|
| `frontend` | React, Vue, Svelte components; CSS; animations; design tokens; a11y auditing; UI patterns |
| `backend` | REST/GraphQL APIs; auth flows; middleware; background jobs; caching; rate limiting |
| `fullstack` | End-to-end features spanning client + server; SaaS boilerplate; monorepo setup |
| `devops` | CI/CD pipelines; Docker; Kubernetes; Terraform; cloud deploy; monitoring setup |
| `database` | Schema design; migrations; query writing; indexing; ORMs; seed scripts |
| `testing` | Unit tests; integration tests; E2E tests; mocking; test data factories; coverage config |
| `security` | OWASP hardening; auth security; secrets management; dependency auditing; pentest checklists |
| `performance` | Bundle optimization; Core Web Vitals; query profiling; caching strategies; load testing |
| `documentation` | README generation; API docs; changelogs; inline comments; architecture decision records |
| `ai-ml` | LLM integration; RAG pipelines; embedding search; agent scaffolding; prompt chaining |
| `mobile` | React Native; PWA; responsive patterns; native bridge code; app store prep |
| `tooling` | Linters; formatters; build config; git hooks; monorepo tooling; DX improvements |

**If you're unsure**, open an issue with the `category?` label and the maintainers will help.

---

## Testing Your Skill

A skill is ready to submit when it passes all three tests:

### Test 1 — Trigger accuracy

Verify Claude activates the skill when it should, and doesn't activate when it shouldn't.

```
# Should trigger:
"[One of your example trigger phrases]"
"[A paraphrase of your trigger that isn't in the description]"

# Should NOT trigger:
"[Out-of-scope request that a confused user might ask]"
```

### Test 2 — Output consistency

Run the same prompt from `example/input.md` three separate times. The outputs should:

- Follow the same structure every time
- Hit the same quality bar every time
- Produce working code (if the skill generates code)

If the outputs are wildly different each run, your SKILL.md needs more specific instructions.

### Test 3 — Output quality gap

Run your `example/input.md` prompt **without** loading the skill. Compare the output to **with** the skill.

The with-skill output should be clearly better. If it's the same, your skill isn't doing enough work. If Claude ignores parts of the skill, those sections need to be rewritten as more direct instructions.

### How to load a skill for testing

**Claude.ai:**
1. Create or open a Project
2. Go to Project Instructions
3. Paste the contents of your SKILL.md
4. Test in that project

**API:**
```javascript
import Anthropic from "@anthropic-ai/sdk";
import fs from "fs";

const skill = fs.readFileSync("skills/<category>/<skill-name>/SKILL.md", "utf-8");

const client = new Anthropic();
const response = await client.messages.create({
  model: "claude-opus-4-5",
  max_tokens: 4096,
  system: skill,
  messages: [{ role: "user", content: "YOUR TEST PROMPT HERE" }],
});

console.log(response.content[0].text);
```

---

## Submitting a Pull Request

### Before opening the PR

- [ ] All four required files exist (`SKILL.md`, `README.md`, `example/input.md`, `example/output.md`)
- [ ] Folder name matches `name:` in SKILL.md frontmatter
- [ ] SKILL.md quality checklist passed (see above)
- [ ] README.md quality checklist passed (see above)
- [ ] `example/output.md` contains real Claude output, not handwritten
- [ ] Skill tested at least 3 times (consistent output confirmed)
- [ ] Output quality gap confirmed (better with skill than without)
- [ ] No placeholder text remaining in any file

### PR title format

```
feat(category): add skill-name

Examples:
feat(frontend): add accessible-modal-builder
feat(backend): add jwt-auth-scaffolder
feat(testing): add vitest-factory-generator
```

### PR description

Use the PR template (`.github/PULL_REQUEST_TEMPLATE.md`). It asks you to:

1. Link to an issue (if one exists)
2. Describe what the skill does in 2 sentences
3. Show your test results (3-run consistency check)
4. Confirm the quality gap exists

### What happens after you submit

1. A maintainer reviews within 3 business days
2. Feedback (if any) is left as PR comments — specific and actionable
3. Once approved, your skill is merged and appears in the registry within 24 hours
4. Your GitHub handle is added to the Contributors section of the README

---

## Review Criteria

Maintainers evaluate skills on four dimensions:

**Correctness** — Does the skill do what `SKILL.md` and `README.md` claim? Does the example output actually come from running the skill?

**Specificity** — Are the instructions specific enough that two different Claude sessions produce consistently similar output? Vague skills get sent back for revision.

**Scope** — Is the skill focused on one job? Skills that try to do everything get asked to split.

**Value** — Is the output quality meaningfully better with the skill than without? If a bare Claude prompt gives the same result, the skill isn't pulling its weight.

### Common rejection reasons (and fixes)

| Reason | Fix |
|---|---|
| `description` too vague — Claude activates on unrelated requests | Add 3–5 specific example phrases; add explicit out-of-scope cases |
| Output not consistent across runs | Add a more prescriptive output format section; add a reference patterns section |
| Example output looks hand-written | Re-run the skill and paste real output; include the "// Assumption:" comments Claude naturally generates |
| Skill does too many things | Split into focused skills; each can link to the others in "See Also" |
| No quality gap vs bare Claude | Skill needs more opinionated constraints, patterns, or checklists |
| Placeholder text remaining | Finish filling in the template |

---

## Improving an Existing Skill

Found a bug, a missing case, or a better pattern? Improvements are very welcome.

**For small fixes** (typos, broken examples, missing edge case):
Open a PR directly with a clear title: `fix(category): skill-name — description of fix`

**For behaviour changes** (adding a new step, changing output format, tightening constraints):
Open an issue first with the `skill-improvement` label. Describe the current behaviour, the problem with it, and your proposed change. This prevents duplicate work and lets the original author weigh in.

**Version bump rules:**
- Fix typo / clarify wording → no bump required
- Add a step or constraint that doesn't change existing output → bump patch (`1.0.0` → `1.0.1`)
- Change output format or add new output files → bump minor (`1.0.0` → `1.1.0`)
- Remove files, rename props, or break existing usage → bump major (`1.0.0` → `2.0.0`)

---

## Requesting a Skill

Don't have time to build it but know what you need? Open an issue with the `skill-request` label.

A good skill request includes:

1. **What task** — one specific, recurring developer task
2. **What output** — what files or artifacts it should produce
3. **Quality bar** — what "done well" looks like
4. **How often you'd use it** — helps maintainers prioritise

The community can claim requests with a comment: *"I'll take this"* — and then open a PR once built.

---

## Code of Conduct

This project follows the [Contributor Covenant](https://www.contributor-covenant.org/version/2/1/code_of_conduct/). In short:

- Be respectful in reviews and discussions
- Criticism is about the skill, not the person
- Maintainers may edit or remove content that violates community standards

---

## Need Help?

Stuck on something? Three ways to get help:

- **GitHub Discussions** — [Start a discussion](https://github.com/YOUR_USERNAME/claude-skills/discussions) — best for questions about skill design
- **GitHub Issues** — [Open an issue](https://github.com/YOUR_USERNAME/claude-skills/issues) — for specific bugs or requests
- **Discord** — [Join the server](#) — real-time help from contributors

When asking for help on a skill-in-progress, paste your current `SKILL.md` and describe what behaviour you're seeing vs. what you expected. That gives reviewers enough context to help quickly.

---

*Thanks again for contributing. Every skill in this repo represents someone deciding to share rather than hoard. That's what makes this useful.*
