# 🧠 Claude Skills — Community-Powered AI Development Accelerators

> A curated, open-source collection of **Claude Skills** that supercharge your development workflow. Drop them into Claude and instantly unlock specialized capabilities for frontend, backend, fullstack, DevOps, and more.

[![Stars](https://img.shields.io/github/stars/jsrockstaranks/claude-skills?style=for-the-badge&logo=github&color=FFD700)](https://github.com/jsrockstaranks/claude-skills/stargazers)
[![Contributors](https://img.shields.io/github/contributors/jsrockstaranks/claude-skills?style=for-the-badge&color=4CAF50)](https://github.com/jsrockstaranks/claude-skills/graphs/contributors)
[![Skills](https://img.shields.io/badge/skills-growing-blue?style=for-the-badge)](https://github.com/jsrockstaranks/claude-skills/tree/main/skills)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](LICENSE)

---

## What is a Claude Skill?

A **Claude Skill** is a structured `SKILL.md` file you provide to Claude (via system prompt, project instructions, or context) that gives it deep, task-specific expertise. Instead of re-prompting Claude from scratch every time, a skill encodes:

- **What** the task is and when to trigger
- **How** to execute it step-by-step
- **Constraints** and best practices to follow
- **Examples** of ideal inputs and outputs

Think of it as giving Claude a specialist's playbook — reusable, shareable, and version-controlled.

---

## 🚀 Quick Start

**Step 1** — Browse the [`skills/`](./skills/) directory and find a skill for your use case.

**Step 2** — Copy the content of the skill's `SKILL.md`.

**Step 3** — Paste it into:
- Claude's **Project Instructions** (for persistent use across chats)
- The **system prompt** of your Claude API integration
- Or at the start of any conversation as context

**Step 4** — Start your task. Claude will follow the skill automatically.

---

## 📂 Skill Categories

| Category | Description | Example Skills |
|---|---|---|
| [`frontend/`](./skills/frontend/) | UI components, React, Vue, CSS, animations | Component generator, design system enforcer, a11y auditor |
| [`backend/`](./skills/backend/) | APIs, auth, databases, queues, caching | REST scaffold, SQL query builder, JWT auth flow |
| [`fullstack/`](./skills/fullstack/) | End-to-end features, SaaS boilerplate | Feature scaffolder, CRUD generator, multi-tenant setup |
| [`devops/`](./skills/devops/) | CI/CD, Docker, Terraform, cloud deploy | GitHub Actions writer, Dockerfile optimizer, k8s manifest |
| [`database/`](./skills/database/) | Schema design, migrations, query tuning | ERD to schema, migration generator, index advisor |
| [`testing/`](./skills/testing/) | Unit, integration, E2E, mocking | Test case generator, coverage analyzer, mock builder |
| [`security/`](./skills/security/) | Auth, OWASP, secrets, access control | Vulnerability scanner, OWASP checklist, secrets auditor |
| [`performance/`](./skills/performance/) | Profiling, optimization, Core Web Vitals | Bundle analyzer, React render optimizer, DB query profiler |
| [`documentation/`](./skills/documentation/) | READMEs, API docs, changelogs, JSDoc | README writer, OpenAPI generator, changelog formatter |
| [`ai-ml/`](./skills/ai-ml/) | LLM integration, RAG, embeddings, agents | RAG pipeline builder, prompt optimizer, agent scaffolder |
| [`mobile/`](./skills/mobile/) | React Native, PWA, responsive patterns | RN component builder, PWA setup, responsive layout advisor |
| [`tooling/`](./skills/tooling/) | Linting, build config, monorepo, DX | ESLint config generator, Vite setup, monorepo bootstrapper |

---

## 🌟 Featured Skills

### 🎨 `frontend/react-component-generator`
Generates production-ready React components with TypeScript, prop types, Storybook stories, and unit tests in one shot.

### ⚙️ `backend/rest-api-scaffolder`
Takes a data model description and outputs a complete Express/Fastify REST API with validation, error handling, and OpenAPI spec.

### 🗄️ `database/migration-writer`
Converts schema change descriptions into safe, reversible migration files for Prisma, Drizzle, or raw SQL.

### 🔐 `security/owasp-reviewer`
Reviews code against OWASP Top 10 and outputs a prioritized list of vulnerabilities with fix recommendations.

### 📝 `documentation/readme-generator`
Analyzes a codebase or description and produces a polished, SEO-optimized README with badges, usage examples, and contribution guide.

---

## 🛠️ How to Use with Claude API

```javascript
import Anthropic from "@anthropic-ai/sdk";
import fs from "fs";

const skill = fs.readFileSync("./skills/frontend/react-component-generator/SKILL.md", "utf-8");

const client = new Anthropic();
const message = await client.messages.create({
  model: "claude-opus-4-5",
  max_tokens: 4096,
  system: skill,
  messages: [
    {
      role: "user",
      content: "Create a reusable DataTable component with sorting, filtering, and pagination."
    }
  ]
});

console.log(message.content);
```

---

## 🤝 How to Contribute

We want skills from the trenches — things you've actually used to ship faster.

### Option A — Submit a Skill (Recommended)

1. **Fork** this repo
2. **Copy** `_template/SKILL.md` into the right category folder:
   ```
   skills/<category>/<your-skill-name>/SKILL.md
   ```
3. **Add** a `README.md` explaining what it does and when to use it
4. **Include** an `example/` folder with sample input and output (strongly recommended)
5. **Open a Pull Request** using the skill PR template

### Option B — Request a Skill

Open an [Issue](https://github.com/jsrockstaranks/claude-skills/issues/new?template=new-skill.md) with the `skill-request` label describing what you need. The community will build it.

### Contribution Guidelines

- Skills must be **tested** — run it at least 3 times and verify output quality
- Skills should be **focused** — one skill, one job
- Include **at least one example** in the `example/` folder
- Follow the naming convention: `kebab-case` for folder names
- Read [`CONTRIBUTING.md`](./CONTRIBUTING.md) for the full guide

---

## 🏗️ Skill File Structure

Every skill lives in its own folder:

```
skills/<category>/<skill-name>/
├── SKILL.md        ← The actual skill (Claude reads this)
├── README.md       ← Human docs: what it does, when to use it
├── example/
│   ├── input.md    ← Sample prompt or task description
│   └── output.md   ← Expected Claude response
└── preview.png     ← Optional: screenshot of output (for visual skills)
```

### `SKILL.md` Format

```markdown
---
name: your-skill-name
description: One-line description for when Claude should trigger this skill
category: frontend | backend | fullstack | devops | database | testing | security | performance | documentation | ai-ml | mobile | tooling
author: your-github-handle
version: 1.0.0
---

## Overview
What this skill does and why it exists.

## When to Trigger
Exact conditions or user phrases that should activate this skill.

## Step-by-Step Process
1. First, Claude should...
2. Then...
3. Finally...

## Constraints & Best Practices
- Always...
- Never...
- If X, then Y...

## Output Format
Describe the exact format Claude should use for output.
```

---

## 📊 Stats & Roadmap

- 🎯 **Goal:** 1,000 GitHub Stars in 30 days
- 📦 **Current Skills:** Growing daily
- 🌍 **Contributors:** You could be first

### Upcoming
- [ ] Skill search & tagging system
- [ ] Claude.ai direct import button
- [ ] Automated skill quality scoring via CI
- [ ] Skill chaining (compose multiple skills)
- [ ] VS Code extension for quick skill injection

---

## 💬 Community

- **Discussions:** [GitHub Discussions](https://github.com/jsrockstaranks/claude-skills/discussions) — share what you're building
- **Discord:** [Join the server](#) — real-time skill swapping and collaboration
- **X/Twitter:** Tag [#ClaudeSkills](#) when you ship something with a skill

---

## 🔍 SEO & Discoverability

> Looking for: **Claude prompts for developers**, **Claude system prompts**, **Claude project instructions**, **AI coding assistant prompts**, **Claude API examples**, **Claude skills library**, **Anthropic Claude developer tools**, **reusable AI prompts**, **Claude prompt engineering**

This repo indexes community-proven Claude skills for software engineers. Whether you're building with the Anthropic API, using Claude.ai Projects, or running Claude in your own toolchain — these skills make Claude dramatically more capable for real engineering work.

---

## 📄 License

MIT — free to use, fork, and build on. Attribution appreciated but not required.

---

<p align="center">
  Built by developers, for developers. ⭐ Star this repo if a skill saved you time.
  <br/>
  <a href="https://github.com/jsrockstaranks/claude-skills/stargazers">Star it →</a>
</p>
