> If this saves you time, consider starring ⭐ — it helps others find it.

# Smart Routing Skill for Claude Code

A global Claude Code skill that detects when your request would be better handled by a different Claude tool — and gives you a ready-made brief to take there immediately.

---

## The Problem This Solves

Claude has three tools that do very different things:

| Tool              | Best For                                        |
| ----------------- | ----------------------------------------------- |
| **Claude Chat**   | Thinking, planning, architecture, decisions     |
| **Claude Cowork** | File management, desktop automation             |
| **Claude Code**   | Writing code, running tests, editing files, git |

Without guidance, it's easy to end up asking Claude Code architectural questions (wasting expensive session tokens on conversation) or asking Claude Chat to fix bugs (getting suggestions you have to apply yourself instead of Claude doing it directly).

This skill detects those mismatches and redirects you — with a copy-paste brief ready to go.

---

## What It Does

When you make a request that belongs in a different tool, the skill:

1. **Warns you** with a clear one-line explanation of why
2. **Hands you a ready-made brief** tailored to your exact request — no fill-in-the-blanks
3. **Offers to proceed anyway** so you're never blocked

---

## Example

You're in Claude Chat and type:

> "Fix the bug in my OrderController where inventory isn't decrementing"

The skill responds:

```
⚠️ BETTER TOOL AVAILABLE

This request is better handled in Claude Code because fixing a bug
requires reading your actual file, running the code, and verifying
the fix — none of which Claude Chat can do.

---
📋 SESSION BRIEF — paste this into Claude Code:

Goal: Fix bug where inventory count is not decrementing after an order is created.
Context: Issue is in OrderController. Order creates successfully but inventory stays unchanged.
Relevant files: Look in the controller handling order creation and the inventory service.
Done when: Creating an order correctly decrements inventory count and existing tests pass.

---
Want to proceed here anyway? I can suggest a likely fix but can't read
your files or verify it runs correctly.
```

---

## Installation

### Global install (recommended)

Applies to all your projects automatically.

```bash
# Copy skill to your global Claude config
cp -r .claude/skills/smart-routing ~/.claude/skills/smart-routing
```

### Per-project install

```bash
# Copy into your project
cp -r .claude/skills/smart-routing your-project/.claude/skills/smart-routing
```

### Via Claude Code plugin marketplace

```
/plugin marketplace add [your-github-username]/smart-routing-skill
```

---

## How It Works

The skill lives in `.claude/skills/smart-routing/SKILL.md`. Claude Code reads it automatically and activates when your request matches routing trigger patterns.

It detects:

- **Chat → Code mismatches**: file editing, bug fixing, running code, git ops
- **Chat → Cowork mismatches**: file management, desktop automation
- **Code → Chat mismatches**: architectural decisions, tradeoff questions, planning
- **Code → Cowork mismatches**: non-coding file operations

It does NOT trigger for:

- Ambiguous requests where tool choice doesn't matter
- Simple questions and quick lookups
- When you've explicitly acknowledged the limitation

---

## Philosophy

**One job, done well.**
This skill does routing and nothing else. No capability cheatsheets, no extra guidance — just a clean warning and a ready brief when it matters.

**Never blocking.**
Every warning ends with an offer to proceed anyway. The skill helps — it doesn't gatekeep.

**Token-aware.**
The skill uses progressive disclosure — it only activates when a mismatch is detected, so it adds zero overhead to normal sessions.

---

## Contributing

This is a v1. Routing patterns, trigger phrases, and brief templates can all be improved with real usage. PRs welcome — especially:

- New trigger patterns from real mismatches you've encountered
- Better brief templates for specific request types
- Edge cases where the skill triggers incorrectly

---

## License

MIT
