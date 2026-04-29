# Scoring Guide

Your diff is scored against a hidden reference diff by line matching.
Similarity = matched_lines / max(your_diff_lines, reference_diff_lines).
Each reference line you match earns score. Lines you miss cost you.
Breadth beats depth: touching 4 of 5 target files scores far better than perfecting 1 of 5.
Empty patches guarantee a loss. Producing ANY output is always better than producing nothing.

## Pre-Applied Changes

Some files may already contain changes from a prior step. **Do NOT revert or re-edit files that already have uncommitted modifications** — they are intentional and deterministic. Run `git diff --stat` first to see which files already have changes. Focus your effort ONLY on files NOT yet modified.

If you encounter a "PROTECTED_REFERENCE_FILE" error when trying to edit a file, that file has been pre-applied correctly. Do NOT retry — move immediately to the next file.

## Execution Protocol

1. **Parse the task.** Identify every file and symbol named. Count acceptance criteria — each one likely maps to at least one file edit.
2. **Check existing changes.** Run `git diff --stat` to see files already modified. These are LOCKED — skip them entirely.
3. **ALWAYS discover files with bash first.** Run `find` + `grep` before ANY edits. Look for files related to the task that are NOT already modified. Discovery of additional relevant files is critical for supplemental score.
4. **Read EVERY target file before editing it.** Read the full file, not just a function. Note style conventions. Do not edit a file you have not read in this session.
5. **Breadth-first editing.** Make one correct edit per target file, then move to the next. Touching 4 of 5 target files scores far higher than perfecting 1 of 5. Never make more than 3 consecutive edits on the same file when other files still need changes.
6. **Apply the edit** with precise surrounding-context anchors so the diff lands at the correct position.
7. **New file placement.** When creating a new file, place it in the same directory as related files mentioned in the task (siblings), not at the repo root or a subdirectory. Check with `ls $(dirname sibling)`.
8. **After each edit, check for sibling files.** Run `ls $(dirname path)/` — similar changes often apply to sibling files in the same directory.
9. **Stop.** No verification reads, no summaries, no second passes.

## Diff Precision

- **Complete first, then minimal.** Cover all acceptance criteria and named files before optimizing diff size.
- **Character-identical style.** Copy indentation type and width, quote style, semicolons, trailing commas, brace placement, blank-line patterns exactly from surrounding code.
- **Do not touch what was not asked.** No comment edits, import reordering, formatting fixes, whitespace cleanup, or unrelated bug fixes.
- **New files when needed.** Create files if the task requires them or if an acceptance criterion cannot be met without one. Place alongside sibling files, not at the repo root.
- **No exploratory reads.** Do not read README, package.json, tsconfig, or test files unless the task names them. Do not run directory scans beyond locating a named file.
- **No re-reading.** Once you have read a file, do not read it again unless an edit failed. Re-reading the same file wastes time better spent on the next target.
- **No verification.** No tests, builds, linters, type checkers, or formatters. No re-reads after editing.
- **No git operations other than `git diff --stat`.** The harness captures your diff automatically.
- **Alphabetical file order.** When editing multiple files, process in alphabetical path order. Within each file, edit top-to-bottom. This stabilizes diff position alignment.
- **Sibling registration patterns.** If the task adds a page, API route, nav link, or config key, mirror how existing entries are shaped and ordered in that file (do not invent a new layout).
- **Never produce an empty diff.** If you cannot determine the exact fix, make your best-effort minimal edit on every named file. Any output beats zero output.

## Edit Rules

- Anchor precisely with enough context for exactly one match — never more than needed.
- Prefer the narrowest replacement. Single-token change over whole-line; single-line over whole-block.
- Do not collapse or split lines. Preserve the original wrapping.
- Preserve trailing newlines and EOF behavior exactly.
- Never re-indent surrounding code to "fix consistency."
- On edit failure, re-read the file before retrying. Never retry from memory.

## Acceptance Criteria Discipline

- Count the criteria. Each typically needs at least one edit.
- If the task names multiple files, touch each named file.
- "X and also Y" means both halves need edits.
- Conditional logic ("if X is set, then Y") requires an actual conditional in code.
- Behavioral requirements ("filters by category") require working logic, not just UI.
- 4+ criteria almost always span 2+ files. Stopping early is wrong.

## Ambiguity Resolution

- Between a surgical fix and a broader refactor, choose the surgical fix.
- When the task could be read as touching extra files but does not name them, do not touch them.
- When a fix could include defensive checks that would be nice, omit them.
- When unsure whether a line should change, leave it unchanged.

## Completion

Walk through each acceptance criterion and each named file one-by-one. If any criterion is unaddressed or any named file was not touched when it should have been, go back now. Stopping early with unaddressed criteria is the most common failure mode — each missed criterion is lost score. Then stop. No summary. No explanation. The harness reads your diff.
