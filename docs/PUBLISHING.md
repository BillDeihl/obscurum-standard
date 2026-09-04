# Publication model

How OBSCURUM revisions are versioned, released, and cited. This note is a
decision record for editors. It describes the intended pipeline; the
mechanics are not built yet, because there is no normative text to release.

## Canonical form

**The web page is the standard. The PDF is a snapshot of it.**

A standard is consumed by people who need to cite one rule — a procurement
requirement, an assessor's report, a vendor's rebuttal. That needs a stable
deep link to a section. A PDF gives you "page 14 of the attached," which
stops being true the moment layout shifts.

| Artifact | Role | Where |
|---|---|---|
| HTML | Canonical. Citable, deep-linkable, always current. | GitHub Pages |
| PDF | Frozen snapshot of one version. For records and attachments. | GitHub release asset |
| Markdown | Source of both. | this repo, `standard/` |

Both outputs build from the same markdown, so they cannot drift.

## Integrity

Do not rely on PDF permission flags or password protection. They are
stripped by ordinary tools and establish nothing. A document about how well
protections resist attack should not ship behind one that does not.

What establishes integrity instead:

- The version and date on page one, so a loose copy is self-identifying
- A published SHA-256 for each release PDF, on the site and in the release
- The git tag the PDF was built from, so anyone can rebuild and compare

## Versioning

A score is only meaningful against a stated version. If a scoring rule
changes, scores computed under the previous version are no longer
comparable to scores computed under the new one. The changelog must
therefore record not just *what* changed but *whether it moves scores*.

| Class | Meaning | Bump |
|---|---|---|
| **Normative** | Changes a rule, threshold, or scope boundary. Old scores are no longer comparable. | Minor — 0.3 → 0.4 |
| **Non-normative** | Wording, structure, examples. Same input, same score. | Patch — 0.3.1 |
| **Editorial** | Typos, links, formatting. | No release |

Every release entry in `CHANGELOG.md` states its class. A normative entry
also states, in one sentence, what a re-score would do differently.

Assessors state the version they scored under. "Scored under OBSCURUM
v0.3" is a complete claim; "scored under OBSCURUM" is not.

Pre-1.0 means the standard is still moving. Normative changes are expected
and are not breakage.

## Release steps

1. Land changes on `main` through pull requests, as normal
2. Update `CHANGELOG.md` with the class of each change
3. Tag `vX.Y.Z`
4. On the tag: build HTML and deploy Pages; build the PDF, hash it, attach
   both to the GitHub release

Nothing runs on ordinary commits. Day-to-day editing is unaffected.

## Where releases go

| Destination | Purpose | When |
|---|---|---|
| GitHub Pages | The citable home of the current version | First release |
| GitHub Releases | Immutable per-version PDF plus its hash | First release |
| Zenodo | A DOI per release, for academic and permanent citation | Worth connecting before the first release; awkward to backfill |
| OWASP Mobile | If the community adopts it. Their process, their timeline. | Much later |

## Tooling

Typst is preferred for the PDF build: fast, readable templating, a
professional result without a TeX toolchain. Pandoc is the fallback if one
pass should produce both HTML and PDF.

Neither choice depends on visual identity. A plain, well-typeset document
can ship now and be restyled later without touching the pipeline.
