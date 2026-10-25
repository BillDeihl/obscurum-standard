# Changelog

All notable changes to the OBSCURUM standard text are recorded here. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and versioning follows [section 11 of the standard](standard/obscurum.md#11-governance-and-versioning) (see [GOVERNANCE.md](GOVERNANCE.md)).

## [0.2] — 2026-10-25 — Working Draft

First public release of the standard text ([`standard/obscurum.md`](standard/obscurum.md)). Status: **Working Draft**.

Appendices P1-P9 are referenced by the text but not yet published. Each is released once frozen.

Changes from the earlier internal baseline, as listed in section 13 of the standard:

- Added a per-pillar status table (scoreable, provisional, or advisory) in section 5.0.
- Merged revised measurement methods from earlier research and proposed edits.
- P1, P2, P4, P5, P7: added concrete scored-core formulas, syntactic opacity, and entropy/decode pairing rules.
- P3, P9: concept adopted. They stay out of the composite until their appendices are frozen.
- P6, P8: removed from the composite denominator and made advisory-only pending redesign.
- Section 6.2: the v0.2 denominator is P1 + P2 + P4 + P5 + P7 (weights sum to 3.0).
- Section 6.4: replaced the absolute cross-tool byte-for-byte claim with intra-implementation determinism, cross-implementation agreement on a conformance suite, and a scored-core versus advisory split.
- Section 7: the report schema gains `status`, `provisional`, `denominator_pillars`, and `appendix_versions`.
- Section 9: added citation-class hygiene (controls, tests, weaknesses, and Top 10 context kept distinct).

v0.1 was an unpublished internal baseline and is not published in this repository.
