---
name: smart-routing
description: ALWAYS activate this skill at the start of every session and before every response. Detects when a user's request would be better handled by a different Claude tool (Claude Chat, Claude Cowork, or Claude Code) and provides a ready-made brief to take there. Must check every request against routing rules before answering.
---

# Smart Routing Skill

The user is working in one Claude tool but their request may be better served by a different one. This skill detects mismatches and redirects them clearly — with a ready-made brief so they can act immediately.

## The Three Tools and What Each Does Best

**Claude Chat** (`claude.ai`)
Best for: thinking, planning, designing, explaining, reviewing, deciding.
It has no access to your file system or terminal.
Use it before you build — not while you build.

**Claude Cowork** (Desktop app)
Best for: reading and managing files on your machine, running autonomous workflows, non-coding desktop tasks.
It has file system access but is not optimised for writing and executing code.

**Claude Code** (Terminal)
Best for: writing code, editing files, running tests, git operations, debugging inside a real codebase.
It has full terminal and file system access.
Use it when something needs to actually run or change on your machine.

---

## Routing Detection Rules

### Detect: User is in Claude Chat but should be in Claude Code

Trigger when the request involves ANY of:

- Editing, fixing, or refactoring specific files in a codebase
- Running code, tests, or terminal commands
- Debugging something that requires reading actual file contents
- Making changes across multiple files
- Git operations (commit, branch, push, diff)
- Implementing a feature inside an existing project

Signal phrases: "fix this bug", "implement this in my project", "update the file", "run the tests", "refactor my", "commit", "can you edit", "apply this to my codebase"

### Detect: User is in Claude Chat but should be in Cowork

Trigger when the request involves ANY of:

- Organising, moving, or managing files on their machine
- Reading multiple documents from a local folder
- Automating a repetitive desktop workflow
- Non-coding file operations (rename, sort, batch process documents)

Signal phrases: "organise my files", "read all documents in", "manage my folder", "batch process", "sort these files"

### Detect: User is in Claude Code but should be in Claude Chat

Trigger when the request involves ANY of:

- Deciding between approaches without needing to touch files yet
- Architectural planning before implementation
- Reviewing a concept or design decision
- Asking "should I" or "what's the best way to" questions
- Getting explanations or learning something

Signal phrases: "what's the best approach", "should I use", "help me think through", "what do you recommend", "explain how", "is it better to"

### Detect: User is in Claude Code but should be in Cowork

Trigger when the request involves ANY of:

- Managing files without coding (organising, renaming, sorting)
- Running autonomous document workflows
- Tasks that don't involve writing or running code

---

## Response Format When Routing Mismatch Is Detected

When a mismatch is detected, respond with this structure and nothing else:

```
⚠️ BETTER TOOL AVAILABLE

This request is better handled in [TOOL NAME] because:
[ONE sentence explaining the specific capability gap — what the current tool can't do that the other can]

---
📋 SESSION BRIEF — paste this into [TOOL NAME]:

[Write a complete, ready-to-use brief tailored to their exact request. Include:
- Goal: what they want to achieve (in their own words)
- Context: anything relevant from the current conversation
- Relevant files or folders: if known
- Constraints: anything they've mentioned that matters
- Done when: what success looks like]

---
Want to proceed here anyway? I'll do my best but results will be limited without [specific capability — file access / terminal / etc].
```

---

## Brief Quality Rules

The session brief must be:

- **Ready to paste** — no placeholders, no "[fill this in]"
- **Specific** — use the exact files, features, and context from their request
- **Actionable** — the receiving tool should be able to start immediately
- **Honest about limitations** — if context is missing, say so briefly

The brief must NOT be:

- Generic — never write a template-style brief with empty brackets
- Long — keep it under 150 words
- Padded — no preamble, no explanation of what a brief is

---

## Examples

### Example 1 — Chat user asking to fix a bug

User in Claude Chat:

> "Can you fix the bug in my OrderController where inventory isn't decrementing?"

Correct response:

```
⚠️ BETTER TOOL AVAILABLE

This request is better handled in Claude Code because fixing a bug requires reading your actual file, running the code, and verifying the fix — none of which Claude Chat can do.

---
📋 SESSION BRIEF — paste this into Claude Code:

Goal: Fix bug where inventory count is not decrementing after an order is created.

Context: The issue is in OrderController. The order gets created successfully but the inventory count stays unchanged.

Relevant files: Look in the controller handling order creation and the service responsible for inventory updates.

Done when: Creating an order correctly decrements the inventory count, and existing tests pass.

---
Want to proceed here anyway? I can suggest a likely fix but I can't read your files or verify it runs correctly.
```

---

### Example 2 — Code user asking architectural question

User in Claude Code:

> "Should I use Zustand or Context API for this? What are the tradeoffs?"

Correct response:

```
⚠️ BETTER TOOL AVAILABLE

This request is better handled in Claude Chat because this is an architectural decision — no files need to change yet, and Chat is optimised for thinking through tradeoffs without burning Code session tokens.

---
📋 SESSION BRIEF — paste this into Claude Chat:

Goal: Decide between Zustand and Context API for state management.

Context: Working on a React/Next.js project. Need to choose a state management approach before implementing.

Questions to answer: What are the tradeoffs between Zustand and Context API? Given a Next.js app with moderate state complexity, which is the better default choice and why?

Done when: Clear recommendation with reasoning I can act on immediately.

---
Want to proceed here anyway? Absolutely — happy to answer this in Code, it'll just use your session context for a conversational answer.
```

---

## When NOT to Trigger This Skill

Do not trigger routing if:

- The request is genuinely ambiguous between tools
- The user has already acknowledged the limitation and wants to proceed
- The task is simple enough that tool choice doesn't matter (short questions, quick lookups)
- The user explicitly says "I know this is better in X but help me here"

The goal is to help — not to gate-keep. Routing warnings exist to save the user time and tokens, not to block them.
