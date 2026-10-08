---
name: tdd
description: Test-driven development with a red-green-refactor loop. Build features or fix bugs one small vertical slice at a time, always writing the failing test first. Use when implementing anything with a testable seam.
---

# TDD

Always take small, deliberate steps. The rate of feedback is your speed limit.

## Red - Green - Refactor

1. **Red**: write ONE test that fails, covering the smallest next behavior. Run it. See it fail for the right reason.
2. **Green**: write the minimum code that passes. No more. Run the test.
3. **Refactor**: clean both test and code while green. Re-run.
4. Repeat.

Never write implementation before the test fails. Never move to the next slice while red.

## Test quality rules

- Test **external behavior** through the narrowest possible seam, never implementation details.
- Prefer the highest existing seam (API, module interface) over new ones. Fewest seams wins.
- Prior art first: mimic the style and location of similar tests already in the repo.
- One logical assertion per test; a failing test message should name the broken behavior.
- Skip nothing tooling already enforces (types, linters).

## Scope guard

If a slice needs more than ~3 test cycles, it is too big - split it. If you cannot find a seam, stop and report that the design needs a testable interface rather than hacking one in.