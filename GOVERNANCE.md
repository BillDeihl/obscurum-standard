# Governance

## Who decides

OBSCURUM currently has a single maintainer and editor, Bill Deihl, who makes final editorial decisions. Contributors propose changes through issues and pull requests; see [CONTRIBUTING.md](CONTRIBUTING.md) for the rules and review path.

The standard anticipates a transition to a small multi-party editorial board once it has external adopters and a sustained pattern of external contribution. Until then, editorial stewardship stays with the original author.

Participation is covered by the [Code of Conduct](CODE_OF_CONDUCT.md). Security-relevant defects in the standard are handled as described in [SECURITY.md](SECURITY.md).

## Versioning

Versioning follows the paper's own governance and versioning section: [`standard/obscurum.md`, section 11](standard/obscurum.md#11-governance-and-versioning). In summary:

- **Major** (x.0): rubric changes that would yield different composite scores on the same binary under the same denominator.
- **Minor** (0.x): clarifications, expanded metric definitions, appendix freezes that promote provisional pillars, or additions that do not change scoring outputs on existing binaries under the prior denominator.
- **Patch**: editorial corrections.

Promoting a provisional or advisory pillar into the composite denominator is a major, or carefully versioned minor, change. It must bump `obscurum_version` and document the denominator migration.

Target cadence is minor versions as needed and major versions annually or when the mobile threat landscape changes significantly.

## Change management

Proposed changes are tracked publicly. Each change falls into one category: editorial, metric refinement, scope change, pillar change, tier or weighting change, appendix freeze, or governance change. Each category has review requirements proportional to its impact on existing scores.

## Stewardship

In version 0.2, editorial stewardship rests with the standard's original author. Long term, the standard may be transferred to a recognized standards organization for ongoing maintenance.

Release history is in [CHANGELOG.md](CHANGELOG.md).
