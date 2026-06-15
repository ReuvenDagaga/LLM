# Dataset Quality Audit - Fullstack Logic + Documentation Research

Created: 2026-06-10

## Verdict

The latest datasets are structurally valid and useful for training behavior. The strongest part is that the documentation dataset forces official documentation checks. The main weakness is that it does not yet fully model how senior engineers search the internet for solutions: comparing official docs, release notes, issue trackers, examples, dates, versions, and contradictory advice.

## Scores

- Fullstack logic overall audit score: 75/100
- Documentation research overall audit score: 90/100
- Fullstack unique tasks: 1000/1000
- Documentation unique tasks: 4000/4000
- Documentation internet_required rows: 4000/4000

## Key Finding

The current documentation tasks are good at forcing official docs, but real fullstack problem solving often requires a wider evidence loop: official docs first, then official changelogs or migration guides, then current issue/discussion evidence, then a version-aware decision.

## Recommended Upgrade

Create a separate internet-solution-search dataset with these required gates:

- Search the web before answering.
- Prefer official docs for APIs and current behavior.
- Check changelogs, migration guides, release notes, and official issue trackers for version-sensitive behavior.
- Use community sources only as supporting evidence, never as the sole authority for framework or provider behavior.
- Record source URLs, version/date assumptions, contradictions, and the final decision.
- Refuse to invent missing API details when sources are unavailable.

## Residual Risk

The generated examples are still template-driven. They are strong scaffolding for training desired behavior, but a later gold pass should add messy real incidents, partial information, conflicting sources, and source freshness traps.
