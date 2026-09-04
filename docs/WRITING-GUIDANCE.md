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

When the draft is mature enough, mark requirements clearly (for example RFC 2119-style MUST / SHOULD / MAY, or an equivalent table). Until then, label the whole document **Working Draft** and avoid sounding final.

## What not to paste into public markdown

- Internal agent prompts, kanban notes, or model-routing plans  
- Unreviewed detector tables that would imply false precision  
- Employer-specific OSINT or unrelated product briefs  
- Code, binaries, or “how to attack” recipes  

## Alignment with OWASP Mobile

OBSCURUM should map to MASVS-RESILIENCE concepts where useful, and should say clearly where it goes **beyond** presence testing into graded quality. Do not brand the project as OWASP unless and until that community adopts it.
