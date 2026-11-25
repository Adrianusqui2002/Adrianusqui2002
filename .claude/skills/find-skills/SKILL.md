---
name: find-skills
description: Help the user discover and install agent skills. Use when the user asks "how do I do X", "is there a skill for X", "find a skill that...", or wants to extend Claude's capabilities with a skill for a specific task or domain.
---

# Find skills

Help the user find an existing skill that fits their need, and install it if they want.

## When to use

- The user asks whether a skill exists for some task ("is there a skill for...", "find a skill that...").
- The user asks how to do something that a specialised skill likely covers (testing, deployment, design, docs, a framework or tool...).
- The user wants to extend Claude's capabilities.

## Steps

1. **Understand the need.** Identify the domain (e.g. React, testing, PDFs) and the concrete task. Ask one short question only if it is genuinely unclear.
2. **Check what is already installed.** Look in `.claude/skills/` (project) and `~/.claude/skills/` (user) before searching elsewhere.
3. **Search the ecosystem.** Use the skills CLI with specific keywords:
   ```bash
   npx skills find <query>
   ```
   Try a couple of synonyms if the first search is thin. Skills can also be browsed at https://skills.sh.
4. **Present the options.** For each relevant result give: the name, what it does, its source (`owner/repo@skill`) and the install command. Recommend the best fit.
5. **Install only if the user agrees:**
   ```bash
   npx skills add <owner/repo@skill>
   ```
   Alternatively, copy the skill folder (with its `SKILL.md`) into `.claude/skills/<skill-name>/`.
6. **If nothing fits**, say so, help with the task directly, and offer to create a new skill in `.claude/skills/<name>/SKILL.md`.

## Notes

- Prefer skills from well-known, trusted sources; read a skill's `SKILL.md` before installing it.
- Never install anything without the user's confirmation.
