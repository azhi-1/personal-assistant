# OpenCode / DeepSeek Instructions

Read `AGENTS.md` first. This file adds OpenCode/DeepSeek-specific operating rules for this repo.

## Role

DeepSeek is the rough builder and high-volume scout.

It is allowed to try broad implementation when the route is obvious, but failed rough builds must become scout reports.

## Rough Build Protocol

Before building:

- state the intended path in one sentence
- inspect nearby files
- run the smallest relevant command first

During building:

- prefer coarse vertical slices over polishing
- keep changes easy for Codex to review
- avoid hidden global rewrites
- do not delete source data or generated databases

Switch to scout mode if:

- the same build/test failure repeats twice
- dependency setup becomes unclear
- runtime behavior depends on hidden host state
- the data model is uncertain
- the implementation would require guessing an API contract

## Failure Report

If rough build fails, write `docs/failures/YYYY-MM-DD-topic.md` with:

- objective
- commands run
- exit codes
- important stderr
- files touched
- what is confirmed
- what is still unknown
- next smallest step

Do not leave the next agent to rediscover the failure.

