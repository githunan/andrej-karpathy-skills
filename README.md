# Karpathy-Inspired Claude Code Guidelines

> Check out my new project [Multica](https://github.com/multica-ai/multica) — an open-source platform for running and managing coding agents with reusable skills.
>
> Follow me on X: [https://x.com/jiayuan_jy](https://x.com/jiayuan_jy)

A single `CLAUDE.md` file to improve Claude Code behavior, derived from [Andrej Karpathy's observations](https://x.com/karpathy/status/2015883857489522876) on LLM coding pitfalls.

English | [简体中文](./README.zh.md)

## The Problems

From Andrej's post:

> "The models make wrong assumptions on your behalf and just run along with them without checking. They don't manage their confusion, don't seek clarifications, don't surface inconsistencies, don't [...]

> "They really like to overcomplicate code and APIs, bloat abstractions, don't clean up dead code... implement a bloated construction over 1000 lines when 100 would do."

> "They still sometimes change/remove comments and code they don't sufficiently understand as side effects, even if orthogonal to the task."

## The Solution

Four principles in one file that directly address these issues:

| Principle | Addresses |
|-----------|-----------|
| **Think Before Coding** | Wrong assumptions, hidden confusion, missing tradeoffs |
| **Simplicity First** | Overcomplication, bloated abstractions |
| **Surgical Changes** | Orthogonal edits, touching code you shouldn't |
| **Goal-Driven Execution** | Leverage through tests-first, verifiable success criteria |

## The Four Principles in Detail

### 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

LLMs often pick an interpretation silently and run with it. This principle forces explicit reasoning:

- **State assumptions explicitly** — If uncertain, ask rather than guess
- **Present multiple interpretations** — Don't pick silently when ambiguity exists
- **Push back when warranted** — If a simpler approach exists, say so
- **Stop when confused** — Name what's unclear and ask for clarification

### 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

Combat the tendency toward overengineering:

- No features beyond what was asked
- No abstractions for single-use code
- No "flexibility" or "configurability" that wasn't requested
- No error handling for impossible scenarios
- If 200 lines could be 50, rewrite it

**The test:** Would a senior engineer say this is overcomplicated? If yes, simplify.

### 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:

- Don't "improve" adjacent code, comments, or formatting
- Don't refactor things that aren't broken
- Match existing style, even if you'd do it differently
- If you notice unrelated dead code, mention it — don't delete it

When your changes create orphans:

- Remove imports/variables/functions that YOUR changes made unused
- Don't remove pre-existing dead code unless asked

**The test:** Every changed line should trace directly to the user's request.

### 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform imperative tasks into verifiable goals:

| Instead of... | Transform to... |
|--------------|-----------------|
| "Add validation" | "Write tests for invalid inputs, then make them pass" |
| "Fix the bug" | "Write a test that reproduces it, then make it pass" |
| "Refactor X" | "Ensure tests pass before and after" |

For multi-step tasks, state a brief plan:

```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let the LLM loop independently. Weak criteria ("make it work") require constant clarification.

## Install

### For Claude Code

**Option A: Claude Code Plugin (recommended)**

From within Claude Code, first add the marketplace:
```
/plugin marketplace add forrestchang/andrej-karpathy-skills
```

Then install the plugin:
```
/plugin install andrej-karpathy-skills@karpathy-skills
```

This installs the guidelines as a Claude Code plugin, making the skill available across all your projects.

**Option B: CLAUDE.md (per-project)**

New project:
```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md
```

Existing project (append):
```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md >> CLAUDE.md
```

### For Cursor

This repository includes a committed Cursor project rule ([`.cursor/rules/karpathy-guidelines.mdc`](.cursor/rules/karpathy-guidelines.mdc)) so the same guidelines apply when you open the project.

To use in another project:
```bash
mkdir -p .cursor/rules
curl -o .cursor/rules/karpathy-guidelines.mdc https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/.cursor/rules/karpathy-guidelines.mdc
```

### For Codex / GitHub Copilot

**New:** Use [`CODEX.md`](./CODEX.md) for OpenAI Codex and GitHub Copilot optimized guidelines!

#### Option A: Download CODEX.md

```bash
# For your project root
curl -o CODEX.md https://raw.githubusercontent.com/githunan/andrej-karpathy-skills/add-codex-guidelines/CODEX.md
```

#### Option B: GitHub Copilot Instructions (VS Code)

```bash
# Create .github directory if it doesn't exist
mkdir -p .github

# Download Codex guidelines as Copilot instructions
curl https://raw.githubusercontent.com/githunan/andrej-karpathy-skills/add-codex-guidelines/CODEX.md \
  -o .github/copilot-instructions.md
```

GitHub Copilot will automatically read from `.github/copilot-instructions.md`.

#### Option C: Cursor Rules for Codex Style

If you want Codex guidelines in Cursor:
```bash
mkdir -p .cursor/rules
# Copy CODEX.md content and convert to .mdc format
```

#### Option D: Use as Reference

Simply reference the principles when using Codex CLI or API:
```bash
# When using Codex API or CLI tools
cat CODEX.md | # Review the principles while coding
```

### Use Cases for CODEX.md

| Use Case | Setup |
|----------|-------|
| **GitHub Copilot in VS Code** | Copy to `.github/copilot-instructions.md` |
| **OpenAI Codex CLI** | Copy to project root as `CODEX.md` |
| **Reference during coding** | Keep open while using any Codex-based tool |
| **Team guidelines** | Commit to git, share with team |

### Key Differences: CLAUDE.md vs CODEX.md

| Aspect | CLAUDE.md | CODEX.md |
|--------|-----------|---------|
| **Tool** | Claude Code IDE | Codex API / GitHub Copilot |
| **Interaction** | Multi-turn dialogue | Single-turn completion |
| **Context** | Large window, can reason | Limited, needs tight context |
| **Focus** | Reasoning first | Completion speed |
| **Main Risk** | Over-discussing | Over-abstracting, hallucinating |
| **Use** | IDE plugin or `.claude` | `.github/copilot-instructions.md` or project root |

### Installation Summary

```bash
# Clone or download this repo
git clone https://github.com/githunan/andrej-karpathy-skills.git
cd andrej-karpathy-skills

# For Claude Code in your project
cp CLAUDE.md /your/project/

# For Cursor IDE
mkdir -p /your/project/.cursor/rules
cp .cursor/rules/karpathy-guidelines.mdc /your/project/.cursor/rules/

# For GitHub Copilot
mkdir -p /your/project/.github
cp CODEX.md /your/project/.github/copilot-instructions.md

# For standalone Codex reference
cp CODEX.md /your/project/
```

## Key Insight

From Andrej:

> "LLMs are exceptionally good at looping until they meet specific goals... Don't tell it what to do, give it success criteria and watch it go."

The "Goal-Driven Execution" principle captures this: transform imperative instructions into declarative goals with verification loops.

## How to Know It's Working

These guidelines are working if you see:

- **Fewer unnecessary changes in diffs** — Only requested changes appear
- **Fewer rewrites due to overcomplication** — Code is simple the first time
- **Clarifying questions come before implementation** — Not after mistakes
- **Clean, minimal PRs** — No drive-by refactoring or "improvements"

For **Codex/Copilot** specifically:
- **Fewer over-abstracted completions** — Codex respects your simplicity intent
- **Fewer hallucinations** — Better testing practices catch non-existent functions
- **Better code integration** — Completions match your file's existing patterns

## Customization

These guidelines are designed to be merged with project-specific instructions. Add them to your existing instruction file or create a new one.

For project-specific rules, add sections like:

```markdown
## Project-Specific Guidelines

- Use TypeScript strict mode
- All API endpoints must have tests
- Follow the existing error handling patterns in `src/utils/errors.ts`
```

## Tradeoff Note

These guidelines bias toward **caution over speed**. For trivial tasks (simple typo fixes, obvious one-liners), use judgment — not every change needs the full rigor.

The goal is reducing costly mistakes on non-trivial work, not slowing down simple tasks.

## Files in This Repository

- **[CLAUDE.md](./CLAUDE.md)** - Guidelines for Claude Code
- **[CODEX.md](./CODEX.md)** - Guidelines for OpenAI Codex and GitHub Copilot (NEW)
- **[CURSOR.md](./CURSOR.md)** - Setup instructions for Cursor IDE
- **[EXAMPLES.md](./EXAMPLES.md)** - Detailed before/after examples for all four principles
- **[.cursor/rules/karpathy-guidelines.mdc](.cursor/rules/karpathy-guidelines.mdc)** - Cursor IDE rule file

## License

MIT
