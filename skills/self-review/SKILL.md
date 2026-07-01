---
name: self-review
description: Carefully self-review recent code or repo changes against the user's request and repository standards. Use when the user asks the agent to double check its work, double check your work carefully, review this again, review your change, verify what you just did, make sure this is right, audit the last edit, or otherwise inspect the agent's own recent implementation before finishing.
---

# Self Review

## Overview

Use this skill to verify the just-completed work against the actual request, the current diff, and the repo's standards. The goal is to find real defects, fix them when clearly in scope, and report the result plainly.

Do not use this skill to expand scope, redesign the solution, or invent hypothetical problems.

## Workflow

1. Re-read the user's request and any relevant follow-up instructions. Do not rely on memory of the task.
2. Inspect the current change set with `git status` and `git diff` when available. Include staged changes if relevant.
3. Identify which changes are yours. Do not revert or rewrite unrelated user changes.
4. Check the implementation against the request, existing project style, and nearby code patterns.
5. Run the relevant tests, lints, type checks, build checks, or validators for the touched area.
6. Fix clear defects that are within scope, then re-run the failed or most relevant checks.
7. Report what you checked, what you fixed, and any remaining risk.

## Review Checklist

- Confirm the change solves the user's actual request, not a nearby problem.
- Look for missed files, stale generated artifacts, broken imports, bad names, dead code, and accidental debug output.
- Check behavior at boundaries touched by the change: empty input, missing config, error paths, async/concurrency, persistence, and compatibility when relevant.
- Verify tests cover the intended behavior when tests were added or should have been added, and run the targeted test command when possible.
- Check that formatting, lint, types, build steps, migrations, snapshots, or generated files are handled according to the repo's conventions.
- For UI work, inspect the result with the repo's normal browser or screenshot workflow when possible.
- For documentation or skills, validate required frontmatter, metadata, links, examples, and command accuracy.

## Reporting

Compare the final report against the actual diff and command results. Lead with the outcome:

- If issues were found: say what was wrong, what was changed, and what verification passed after the fix.
- If no issues were found: say that directly, list the checks that actually ran, and note any meaningful residual risk.
- If verification could not be run: say why and identify the best manual or follow-up check.

Keep the report short. Do not claim tests, lint, or build passed unless those commands ran and passed. Do not bury a real defect behind a general success summary.
