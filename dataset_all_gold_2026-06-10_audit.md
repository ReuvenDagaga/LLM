# Gold Dataset Audit

Created: 2026-06-10
Updated: 2026-06-15

## Verdict

Gold dataset status: gold_ready_after_aggressive_line_by_line_pass.

Included 11521 deduplicated, normalized rows. Every included row has quality_label top, quality_score in the 96-100 range, source pointer, quality gate, validation plan, expected behavior, and research escalation policy.

## Quality Checks

Gold id sequence: contiguous gold-1..gold-11521.

- Unique gold ids: 11521/11521
- Unique normalized task keys: 11521/11521
- Internet-required rows: 8000
- Web-search-required rows: 4000
- Official-doc/spec-required research rows: 8000
- Research rows with coverage/no-answer protocol: 8000
- Missing fields: 0
- Short text fields: 0
- Quality score range: 96-100

## What Changed

- RN rows were cleaned of disconnected A11Y helper exports, literal double-escaped newlines, and claim/snippet mismatches in high-risk examples.
- E2E rows now carry scenario-specific error mapping, user copy, and recovery actions instead of repeated boilerplate.
- Perf rows now use category-specific metrics, guardrails, and snippets for DB, React, network, memory, bundle, Node, and web-vitals work.
- Security systemic rows now align category, threat, validation metric, and solution style across all 540 rows.
- Dataset batch 500 and batch 300 rows were repaired for truncated metadata, tenant placeholders, and borrowed metrics.
- Large-code, migration, and animation batches were refreshed so repeated operating goals, rollout telemetry, feature-flag strategy, reference inspiration, and design intent are row-specific.
- Documentation and internet research rows now require target-by-target coverage mapping, current-date freshness (2026-06-15), contradiction handling, and explicit no-answer fallback when a target lacks official coverage.
- Quarantine rows now resolve kept_gold_id to actual gold ids and distinguish current source lines from historical removed source lines.

## Quarantine Summary

- duplicate_lower_priority: 1555
- duplicate_removed_from_source: 984
- duplicate_normalized_task_key: 29
- historical removed source-line references: 1968
- current source-line references: 600

## Residual Risk

This is a much stronger generated gold layer after structural and semantic cleanup. The next quality jump is expert review on a random 200-row sample plus hard cases with messy real logs, conflicting docs, failing code snippets, and framework-specific compile checks.
