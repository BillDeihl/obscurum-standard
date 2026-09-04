# Writing guidance for the OBSCURUM standard

This note is for editors and contributors. It borrows patterns from open security standards work (especially OWASP MAS style expectations and IETF-style document discipline) without pretending OBSCURUM is an RFC or an OWASP project yet.

## Document shape

A public revision should usually make these jobs easy to find:

| Section job | What the reader should leave knowing |
|---|---|
| Abstract / short purpose | What is measured, on what artifact, for whom |
| Introduction / mission | Why Boolean “obfuscation present” checks are not enough |
| Scope and non-goals | What is in, what is out, and why |
| Threat / use context | Who benefits from a comparable score |
| Normative method | How to measure (pillars, scoring, reporting) |
| Relationship to other work | Especially OWASP MASVS-RESILIENCE / MASTG |
| Conformance | How two honest implementations should relate |
| Governance | Versioning, how changes are accepted |
| Security considerations | Misuse of the score, over-claiming, false assurance |
| References | Normative vs informative, with stable citations |

Keep the **standard body tight**. Long evidence trails, debate notes, and experiment logs belong in private R&D or in dated research notes—not in the normative text.

## Style

- Factual, brief, scannable. Prefer tables and short lists over long prose.  
- Address implementers and assessors in the second person when giving instructions.  
- American spelling; define terms in a glossary once.  
- No commercial tool advertising in normative sections. Reference extractors may appear as *examples* or *conformance notes*, not as the sole legal path, unless the standard explicitly pins one.  
- Every non-obvious claim needs a citation.

## Normative language

Use RFC 2119 keywords from the start: **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, **OPTIONAL**. Capitalize them when they carry that meaning and use ordinary words otherwise.

Include the standard interpretation paragraph once, near the top of the draft:

> The key words MUST, MUST NOT, REQUIRED, SHALL, SHALL NOT, SHOULD, SHOULD NOT, RECOMMENDED, MAY, and OPTIONAL in this document are to be interpreted as described in RFC 2119.

Adopt this now rather than "when the draft is mature." Retrofitting it means re-reading every sentence to decide what it was supposed to mean; without it, readers argue about whether a given line is a requirement or advice. Costs one paragraph today.

Continue to label the document **Working Draft** and avoid sounding final. Keyword discipline and draft status are separate things—a draft can be precise about which of its statements are requirements.

## Section identity and numbering

Once a section is cited in a contract, an assessment, or a procurement requirement, its number has to keep meaning what it meant.

- Number sections explicitly. Do not rely on rendered position.
- **Never renumber to close a gap.** A retired section stays retired, marked as such, rather than having its number reused.
- Add new material at the end of its parent, or as a sub-level (4.2.1), rather than by shifting the sections after it.
- Anchors follow the same rule. A heading may be reworded; its anchor should not change once published.

Cheap to observe from the first draft, painful to impose after anyone has cited the document. See [PUBLISHING.md](PUBLISHING.md) for how versions are released and cited.

## What not to paste into public markdown

- Internal agent prompts, kanban notes, or model-routing plans  
- Unreviewed detector tables that would imply false precision  
- Employer-specific OSINT or unrelated product briefs  
- Code, binaries, or “how to attack” recipes  

## Alignment with OWASP Mobile

OBSCURUM should map to MASVS-RESILIENCE concepts where useful, and should say clearly where it goes **beyond** presence testing into graded quality. Do not brand the project as OWASP unless and until that community adopts it.
