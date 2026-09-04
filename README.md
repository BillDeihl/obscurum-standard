# OBSCURUM

**An open standard for scoring how well a released mobile app resists static reverse engineering.**

OBSCURUM (from Latin *obscūrum*: dark, obscure, hidden) is a vendor-neutral paper standard. Most checklists ask whether obfuscation is on. This asks how well the shipping binary hides what an attacker can recover at rest.

This repository is the **public paper**. It holds the standard text, contribution rules, and writing guidance. It does **not** hold scoring tools, research notebooks, or private R&D.

| Audience | What you use this for |
|---|---|
| Product security / appsec | Compare builds, vendors, and release gates with a shared rubric |
| Assessors and researchers | Reproduce or challenge a score against the published method |
| Risk / assurance leads | Translate “we obfuscated” into something checkable in procurement and policy |
| Implementers (later) | Build conforming scorers against the published method and conformance rules |

## Why this exists

OWASP [MASVS-RESILIENCE](https://mas.owasp.org/MASVS/11-MASVS-RESILIENCE/) and the [MASTG](https://mas.owasp.org/MASTG/) define resilience *controls* and how to *test for them*. They do not publish a graded, comparable score of obfuscation *quality* on the released artifact.

OBSCURUM fills that gap for **static, at-rest** analysis of shipping mobile packages (Android `.apk` / iOS `.ipa`). Runtime defenses (anti-debug, RASP, root/jailbreak detection, and similar) stay out of the score except insofar as their implementation is visible in the binary at rest.

## What this repo is

- A public draft of an open standard (paper only)
- A place for issues and pull requests that improve the **wording and structure** of that paper
- A path toward wider review, including eventual discussion with the OWASP Mobile community when the draft is ready

## What this repo is not

- Not a scanner, SDK, or product
- Not guidance on acquiring or attacking apps you do not own
- Not a substitute for MASVS/MASTG (it is meant to sit beside them)
- Not the private R&D workspace (research sprints, experimental scorers, and editorial ledgers stay elsewhere)

## Status

**Public starter — draft not yet published here.**

The intended document outline (to be filled from curated R&D, not dumped wholesale):

1. Mission and rationale  
2. Scope and non-goals  
3. Why obfuscation at rest matters  
4. Threat model  
5. Measurement pillars  
6. Scoring methodology  
7. Reporting and interoperability  
8. Conformance principles  
9. Relationship to OWASP MASVS-RESILIENCE  
10. Scope of application  
11. Governance and versioning  
12. Glossary  

See [`standard/`](standard/) for the working home of the draft, and [`STATUS.md`](STATUS.md) for what is public vs private.

## License

The contents of this repository are licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](LICENSE). You may share and adapt the text with attribution. See `LICENSE` for the full terms.

## Contributing

Anyone may open an issue or a pull request on the **paper**. Read [`CONTRIBUTING.md`](CONTRIBUTING.md) first. Prefer issues for scope and wording disputes before large PRs.

Writing conventions for this kind of standard live in [`docs/WRITING-GUIDANCE.md`](docs/WRITING-GUIDANCE.md).

## Maintainer

Editor: Bill Deihl.
