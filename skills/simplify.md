---
name: simplify
description: Review changed code for reuse, quality, and efficiency, then fix any issues found. Use when refactoring for clarity without changing behavior, or when code has accumulated unnecessary complexity.
user_invocable: true
---

# Code Simplification

Simplify code by reducing complexity while preserving exact behavior. The goal is not fewer lines -- it's code that is easier to read, understand, modify, and debug.

Test: "Would a new team member understand this faster than the original?"

## When to Use

- After a feature is working and tests pass, but the implementation feels heavy
- During code review when readability or complexity issues surface
- When you encounter deeply nested logic, long functions, or unclear names
- When consolidating related logic scattered across files

## When NOT to Use

- Code is already clean and readable
- You don't understand what the code does yet
- The code is performance-critical and the simpler version would be measurably slower
- You're about to rewrite the module entirely

## Process

### 1. Understand Before Touching (Chesterton's Fence)

Before changing or removing anything, understand WHY it exists. If you see a fence across a road and don't understand why it's there, don't tear it down.

Before simplifying, answer:
- What is this code's responsibility?
- What calls it? What does it call?
- What are the edge cases and error paths?
- Are there tests that define expected behavior?
- Why might it have been written this way? (Performance? Platform constraint? Historical reason?)
- Check `git blame`: what was the original context?

If you can't answer these, read more context first.

### 2. Identify Opportunities

**Structural complexity:**

| Pattern | Signal | Action |
|---------|--------|--------|
| Deep nesting (3+ levels) | Hard to follow control flow | Guard clauses or helper functions |
| Long functions (50+ lines) | Multiple responsibilities | Split into focused functions |
| Nested ternaries | Requires mental stack to parse | if/else chains or lookup objects |
| Boolean parameter flags | `doThing(true, false, true)` | Options objects or separate functions |
| Repeated conditionals | Same check in multiple places | Well-named predicate function |

**Naming and readability:**

| Pattern | Signal | Action |
|---------|--------|--------|
| Generic names (`data`, `result`, `temp`) | Ambiguous | Rename to describe content |
| Abbreviated names (`usr`, `cfg`, `btn`) | Unclear | Full words (except universal: `id`, `url`, `api`) |
| Misleading names (e.g. `get` that mutates) | Dangerous | Rename to reflect actual behavior |
| Comments explaining "what" | Redundant | Delete -- the code is clear enough |
| Comments explaining "why" | Valuable | Keep -- they carry intent code can't express |

**Redundancy:**

| Pattern | Signal | Action |
|---------|--------|--------|
| Duplicated logic (5+ lines) | DRY violation | Extract to shared function |
| Dead code, commented-out blocks | Clutter | Remove (after confirming truly dead) |
| Wrapper that adds no value | Indirection | Inline it |
| Factory-for-a-factory, strategy-with-one-strategy | Over-engineering | Replace with direct approach |

### 3. Apply Changes Incrementally

One simplification at a time. Run tests after each change.

**Separate refactoring from feature work.** A PR that refactors and adds a feature is two PRs -- split them.

```
FOR EACH SIMPLIFICATION:
1. Make the change
2. Run the test suite
3. Tests pass -> continue to next simplification
4. Tests fail -> revert and reconsider
```

**Rule of 500:** If a refactoring would touch 500+ lines, use codemods or AST transforms rather than manual edits. Manual edits at that scale are error-prone and exhausting to review.

### 4. Verify the Result

```
COMPARE BEFORE AND AFTER:
- Is the simplified version genuinely easier to understand?
- Did you introduce any new patterns inconsistent with the codebase?
- Is the diff clean and reviewable?
- Would a teammate approve this change?
```

If the "simplified" version is harder to understand or review, revert.

## Red Flags

- Simplification that requires modifying tests to pass (you likely changed behavior)
- "Simplified" code that is longer and harder to follow than the original
- Renaming things to match your preferences rather than project conventions
- Removing error handling because "it makes the code cleaner"
- Simplifying code you don't fully understand
- Batching many simplifications into one large, hard-to-review commit
- Refactoring code outside the scope of the current task without being asked

## Checklist

- [ ] All existing tests pass without modification
- [ ] Build succeeds with no new warnings
- [ ] Linter/formatter passes
- [ ] Each simplification is an incremental, reviewable change
- [ ] Diff is clean -- no unrelated changes
- [ ] Simplified code follows project conventions
- [ ] No error handling was removed or weakened
- [ ] No dead code left behind (unused imports, unreachable branches)
