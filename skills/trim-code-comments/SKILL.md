---
name: trim-code-comments
description: Review and trim the code comments on a branch or diff, deleting ones that are unneeded, stale, likely to go stale, or meaningless outside the current project, and shortening the rest. Use when asked to clean up, trim, review or refactor comments, remove stale or unneeded comments, or "de-AI" a diff. Also run it on your own diff before calling a branch ready, and whenever a refactor moves or rewrites a comment.
---

# Trim code comments

Comments should be rare, short, and only explain what a reader of that line cannot see from the code. Design reasoning goes in the PR description, not in the source. AI-written branches tend to put a design doc into the code; this skill removes it.

## Scope

- Default target: comments **added or changed by the branch** (`git diff <base>...HEAD`), in source and tests alike. Test files usually have the most to cut.
- Leave pre-existing comments on lines the branch did not touch alone. If one is clearly wrong, report it; don't fix it.
- If the user names files or a PR, use that scope instead.

## The bar

A comment stays only if **all** of these hold:

1. **It helps someone reading this line.** It says something the code, names and types don't already say.
2. **It can't go stale.** Nothing in it becomes false when this PR merges, the config changes, or another file is edited.
3. **A future reader can decode it.** No project-management vocabulary. Domain terms that appear in real identifiers in the codebase are fine.
4. **It is the only copy.** The same reasoning isn't already in the implementation's docblock or another file.

The bar is higher than "it explains a why". A true, non-obvious, important **design decision** still moves to the PR description. The urge to keep it because it's valuable is the usual mistake: when unsure, delete.

## Cut

- **Design rationale and history**: why the module exists, how a constant came to mean two things, "extracted from X where it was open-coded at four call sites". Also cut it when it names a real constraint elsewhere in the system.
- **Module-level `/** */` essays** at the top of a file.
- **Restating the code**: JSDoc prose that repeats the parameter names, docs on self-describing constants or enum members, "// loop over the users".
- **Test comments that paraphrase the `it(...)` / `describe(...)` string.** If the test name doesn't say it, fix the name: describe behaviour ("states both bounds when the rule has an upper bound"), not mechanics ("renders a two-sided window from the rule").
- **Dated or environment claims**: "this flag is on in every environment (confirmed 2026-08-28)", "agreed with product, 2026-09-10". A comment can't keep production state true. If it matters, a test should prove it.
- **Claims about the codebase over time**: "this file had no coverage", "there is no existing test for this". These are false as soon as the PR merges.
- **Epic, ticket, acceptance-criteria, sprint or stakeholder framing**: "ahead of epic 2", "AC3", "open question with the PO".
- **Line numbers or grep results from other files or repos**: "see `handler.js:99,156`".
- **Inlined copies of the code under test**: pseudo-code of the buggy branch drifts silently.
- **Self-answering asides**: "does 'ABCD' contain 'BC'? Yes, but …".
- **Comments that restate a table**: an exhaustive `it.each` table asserts a product decision better than a sentence about it.
- **Commented-out code, and TODOs with no ticket.**

## Keep (shortened to one line where possible)

- **A subtle invariant**: "tested on length, not truthiness: an empty array is truthy".
- **What a repaired line used to do, when the bug wasn't obvious**: "previously parsed as `(type === isAdmin) ? … : …`".
- **In tests**:
  - a one-line marker that a suite pins *current* rather than desired behaviour, e.g. `// DEFECT, pinned deliberately` (without any environment claim);
  - a fixture fact you can't work out by reading it: "2026-07-20 is a Monday" when working-day arithmetic depends on it.
- **Lint directives that guard against a real hazard**, e.g. an `/* eslint-disable import/order */` that keeps `jest.mock` hoisting working. Leave these exactly as they are.

## Moved comments

When a refactor moves a comment (a test deleted, a block extracted), judge it again as if writing it fresh; don't just carry it over. First grep the implementation for the same reasoning. A test-file comment explaining *why the code behaves this way* almost always duplicates the docblock, so delete it.

## Procedure

1. List every added or changed comment in scope (`git diff <base>...HEAD -U0` and look for comment lines; include JSDoc blocks).
2. For each one: delete it, shorten it, or keep it as-is, using the bar above. Rewrite survivors to the fewest words. One line is the norm.
3. If a deleted comment carried a real design decision, collect it as a bullet for the PR description. Don't delete knowledge, move it.
4. If a comment existed only to explain a bad name or a confusing structure, fix the name instead (e.g. drop a jargon suffix). Don't restructure beyond that; extracting helpers is out of scope.
5. Run the formatter and the affected tests. Removing comments must not change behaviour; a red test means something other than a comment was touched.
6. Commit separately (e.g. `chore: trim comments`) so the diff reviews on its own. If the PR is already approved, ask before pushing, since a push may dismiss the approval.
7. Report: lines of comment before and after, any comments kept on purpose and why, and the PR-description bullets for the user to paste in. Don't edit the PR description yourself.
