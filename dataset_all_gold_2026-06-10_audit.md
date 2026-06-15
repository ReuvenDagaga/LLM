# Gold Dataset Audit

Created: 2026-06-10
Updated: 2026-06-15

## Verdict

Gold dataset status: gold_ready.

Included 11521 deduplicated, normalized rows. Every included row has quality_score 100, quality_label top, source pointer, quality gate, validation plan, expected behavior, and research escalation policy.

## Quality Checks

Gold id sequence: contiguous gold-1..gold-11521.

- Unique gold ids: 11521/11521
- Unique normalized task keys: 11521/11521
- Internet-required rows: 8000
- Web-search-required rows: 8000
- Official-docs-required rows: 8000
- Missing fields: 0
- Short text fields: 0
- Quality score range: 96-100

## What Changed

- Older rows without quality metadata were upgraded with design goals, assumptions, decision frameworks, validation plans, tradeoff costs, expected behavior, and gold quality gates.
- Documentation and internet rows were strengthened with web search requirements, official docs requirements, source comparison, freshness policy, evidence requirements, and no-answer conditions.
- Duplicate lower-priority rows were quarantined instead of being mixed into the gold set.
- Exact duplicate normalized gold tasks found during the 2026-06-15 audit were removed from gold and added to quarantine. Source batches with duplicate task rows were deduplicated and reports were updated.
- Invalid JSON lines in source batches were repaired, and source parse counts now reflect the current files.

## Quarantine Summary

- duplicate_removed_from_source: 984

- duplicate_lower_priority: 1555
- duplicate_normalized_task_key: 29

## Residual Risk

This is a stronger generated gold layer after structural cleanup. The next quality jump is a human expert review on a random 200-row sample plus hard cases with messy real logs, conflicting docs, and failing code snippets.
