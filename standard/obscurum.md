# OBSCURUM

## An Open Standard for Measuring Mobile Application Obfuscation Quality

**Version 0.2 — Working Draft**  
**Aligned with OWASP MASVS-RESILIENCE**  
**Supersedes:** v0.1 for active editorial work (v0.1 retained as historical baseline)

---

## Preamble

OBSCURUM exists to make mobile application obfuscation measurable, comparable, and verifiable. Today, when a vendor claims their mobile application is "well protected" or that a competitor's is not, no shared rubric exists to test the claim. Buyers, security teams, and regulators have no neutral way to evaluate competing assertions. The result is a market where confidence in mobile application security is unevenly distributed: where the loudest voice often wins over the most measurably protected product.

This standard proposes a single, vendor-neutral, reproducible way to score the obfuscation quality of any shipping mobile binary. When the standard is widely adopted, vendor claims become checkable. A vendor saying "we are the most obfuscated mobile banking app in the market" can be tested by anyone with access to the shipping binary and a conforming OBSCURUM implementation. Self-assessment loses its monopoly. Independent verification becomes the norm.

The name is Latin for *hidden*. The standard measures how hidden — and therefore how protected — a mobile application's secrets are from reverse engineering and tampering.

**v0.2 note.** This draft folds in research and the 2026-06-15 metric debate adjudication: concrete measurement methods for adopted pillars, a tolerance-based determinism model, scored-core vs advisory layers, and provisional treatment of pillars that are not yet deterministically scoreable. Normative detector tables (appendices) remain draft. **Editorial defaults** (denominator, §6.4 path, freeze order) are locked for this draft so work continues without blocking on open questions.

*Appendices P1-P9 are referenced throughout but are not yet published; they will be released once frozen.*

---

## History of the Standard

This standard began in August 2019 as a skeleton review of existing mobile security guidance. Work paused in 2021 when the focus shifted to cybersecurity outside mobile, then resumed in 2025 as the problem space sharpened and AI's impact on mobile threats grew clearer. It reflects years of iteration, not a first pass.

## Responsible Use of AI

AI-assisted tools supported research and drafting. All technical claims were reviewed by the author against real-world mobile security findings from practicing researchers. This document addresses problems observed in active threat environments, not hypothetical scenarios.

---

## Table of Contents

1. Mission and Rationale
2. Scope and Non-Goals
3. Why Obfuscation at Rest Matters
4. Threat Model
5. The Nine Pillars
6. Scoring Methodology
7. Reporting and Interoperability
8. Conformance Principles
9. Relationship to OWASP MASVS-RESILIENCE
10. Scope of Application
11. Governance and Versioning
12. Glossary
13. Changelog (v0.1 → v0.2)

---

## 1. Mission and Rationale

The mission of OBSCURUM is to establish a single, shared way for the global mobile software industry to think about obfuscation quality.

Three principles drive this mission:

**Independent verifiability.** Any party — the vendor, a customer, a researcher, a regulator, a competitor — should be able to apply the standard to a shipping binary and arrive at a comparable score. No vendor's internal assessment is privileged over an external one. No customer must take a vendor's word on faith.

**Vendor neutrality.** The standard does not favor any obfuscation tool, any platform, any commercial scanner, or any business model. Conformance is defined by the rubric, not by who runs it. Multiple implementers — open-source and commercial — may produce conforming scores, and those scores must agree within the determinism model in §6.4.

**Open access.** The standard, including the full rubric, the scoring methodology, the reporting schema, and the governance process, is published openly and free to implement. Anyone may build a conforming scanner, score their own applications, or use OBSCURUM scores in procurement and assurance processes.

These principles together create accountability. When obfuscation quality can be measured the same way by every party, marketing claims become testable. A score becomes a checkable claim, not an untestable opinion.

---

## 2. Scope and Non-Goals

### 2.1 In scope

- Static analysis of shipping mobile application binaries: `.apk` (Android) and `.ipa` (iOS)
- Measurement of obfuscation quality across nine defined pillars
- Production of a normalized composite score (0–100) and letter grade (A–F) from **scoreable** pillars
- Both major mobile platforms
- A defined reporting schema enabling cross-implementation comparison
- Optional advisory findings that never feed the numerical score

### 2.2 Out of scope

- Dynamic analysis: runtime instrumentation, anti-debug evaluation, anti-tamper testing, behavior observation
- Source code analysis
- Backend or server-side security
- Network traffic analysis
- Detection of bugs, vulnerabilities, or CVEs unrelated to obfuscation
- Privacy, compliance, or regulatory assessment beyond what obfuscation directly affects
- Recommendations on which obfuscation tools to use (the standard is tool-agnostic)
- Acquisition of binaries for analysis
- Implementation details beyond what is required for conformance (reference extraction rules may be published as appendices without mandating a single commercial product)

Implementation guidance, acquisition tooling, and operational considerations belong to companion documents and are intentionally separated from this standard.

---

## 3. Why Obfuscation at Rest Matters

OBSCURUM is a static analysis standard. It scores what an attacker can observe and extract from a binary at rest, without executing it. This scope is intentional and conservative: static analysis is reproducible within a published model, bounded, and foundational to every layer of defense above it.

Dynamic protections — anti-debugging, anti-tampering, root and jailbreak detection, runtime application self-protection (RASP) — are themselves code that lives inside the binary. Their effectiveness depends entirely on an attacker being unable to locate, understand, and disable them before they execute.

A common failure pattern in mobile security illustrates this dependency: applications shipping with substantial runtime protection libraries that are nevertheless bypassed because the binary's at-rest obfuscation is weak. An attacker who can read the binary clearly can statically locate runtime defense routines, identify initialization paths, and patch or short-circuit them before they activate. The dynamic defense, however sophisticated in design, is rendered inert by the static accessibility of its own implementation.

This pattern is not hypothetical. Multiple commercial mobile application protection products have been documented as effectively disabled by attackers who first analyzed weakly protected host binaries to locate and neutralize the protection layer. The runtime protection was present but unprotected at rest.

The consequence: obfuscation at rest is not a separate concern from runtime protection. It is the precondition for runtime protection to function. A binary that scores well under OBSCURUM gives its runtime defenses a chance to do their job; a binary that scores poorly leaves those defenses exposed before they can act.

This is why a static-only standard, properly applied, is meaningful even for applications with extensive runtime protection. The static score is a lower bound on runtime protection effectiveness — runtime defenses cannot be more effective than the at-rest obfuscation that conceals them.

---

## 4. Threat Model

OBSCURUM scores against a defined model of attacker capability and motivation.

### 4.1 Adversary capability tiers

| Tier | Profile | Tools | Effort |
|---|---|---|---|
| **C1 — Casual** | Individual with basic reverse engineering familiarity | Freely available open-source tools (decompilers, manifest readers) | Hours to days |
| **C2 — Skilled** | Experienced reverse engineer | Commercial disassemblers, dynamic instrumentation frameworks | Days to weeks |
| **C3 — Expert** | Professional reverse engineer or small team | Custom tooling, willing to invest substantial time | Weeks to months |
| **C4 — Nation-state** | Effectively unlimited resources, novel techniques | Out of scope for any commercial obfuscation product |

OBSCURUM's defender goal is to raise the cost of an attack from C1 or C2 to at least C3 across all asset categories. Defense against C4 is out of scope for any commercial obfuscation product and is not part of OBSCURUM scoring.

### 4.2 Attacker motivations

OBSCURUM addresses static analysis attacks driven by these motivations:

- **IP extraction**: theft of proprietary algorithms, business logic, or models for competitive purposes
- **Credential extraction**: theft of API keys, tokens, certificates, or cryptographic material
- **Logic recovery**: reconstruction of application behavior from disassembled or decompiled code
- **Asset extraction**: capture of bundled assets such as machine learning models, JavaScript bundles, or scripts for reuse, study, or modification
- **Surface mapping**: identification of attack surface for subsequent exploitation
- **Tampering at rest**: modification of bundled assets or repackaged code to alter application behavior, then redistribution

### 4.3 What OBSCURUM does not measure

- Dynamic attacks: runtime hooking, debugger attachment, memory inspection
- Side-channel attacks
- Supply chain compromise of the build pipeline
- Social engineering against developers
- Attacks requiring physical device access beyond what a normal user has

These threats are real and important. They are not within the scope of a static analysis standard.

---

## 5. The Nine Pillars

OBSCURUM decomposes obfuscation quality into nine measurement pillars. Each pillar addresses a distinct category of static information disclosure or tampering exposure. Each is assigned to one of three tiers — Critical, High, or Moderate — reflecting its relative impact on the threat model.

### 5.0 Scoring status (v0.2)

P stands for pillar and does not indicate priority.

| Pillar | Tier | Composite status | Notes |
|---|---|---|---|
| P1 Identifier Obfuscation | High | **Scoreable (core)** | Syntactic opacity; semantic meaning is advisory |
| P2 String Protection | Critical | **Scoreable (core)** | Protected coverage + severity-weighted sensitive plaintext penalty |
| P3 Embedded Secrets | Critical | **Provisional** | Concept adopted; normative detector + severity tables required (Appendix P3) |
| P4 Resource and Asset Naming | Moderate | **Scoreable (core)** | Reuses P1-style syntactic opacity + capped leak penalties |
| P5 Manifest and Entry-Point | Moderate | **Scoreable (core)** | Penalty-based; best-conditioned on file-format facts |
| P6 Control Flow | Critical | **Advisory only** | Redesign required before composite inclusion |
| P7 Native Code Protection | High | **Scoreable (core)** | Denominator handles fully stripped libraries; packing needs decode pairing |
| P8 Reflection and Indirection | High | **Advisory only** | Sensitive-proximity model rejected; texture indicator only for now |
| P9 Bundled Asset Protection | Critical | **Provisional** | Concept adopted; normative asset taxonomy + severity tables required (Appendix P9) |

**Provisional** pillars may be reported with a computed number when an implementation ships draft tables, but that number **must not** enter the composite until the appendix is frozen and the pillar is marked Scoreable. **Advisory** pillars produce findings only — never a composite-feeding sub-score in v0.2.

Each scoreable pillar has:

1. **Scored core** — deterministic syntactic / structural rules that produce the 0–100 sub-score.
2. **Advisory layer** — optional semantic interpretation that must not feed the sub-score (§6.4).

Normative parameter tables (regex sets, exclusion lists, magic-byte taxonomies, severity weights) live in appendices. Until an appendix is published, implementations MUST document the table version they used and MUST mark affected sub-scores provisional.

### 5.1 P1 — Identifier Obfuscation (High) — Scoreable

**Definition.** The degree to which class, method, field, package, and namespace identifiers in the application have been replaced with semantically opaque tokens.

**Threats addressed.** Static logic recovery, surface mapping.

**Scored core — Identifier Obfuscation Score (IOS).**

```text
eligible = identifiers visible after static extraction
           minus platform-required / ABI-stable symbols
           (OBSCURUM platform-exclusion appendix; not MASTG-KNOW-0033)
opaque   = eligible identifiers matching frozen syntactic rules:
           short generated names, generated-token regexes,
           and/or entropy ≥ frozen cutoff under a frozen tokenization
IOS      = clamp(round(100 * count(opaque) / max(count(eligible), 1)), 0, 100)
```

**Rules.**

- Score **syntactic opacity only**. Dictionary/corpus “meaningful name” detection is **advisory**, never scored.
- Platform-required symbols, exported callbacks, Objective-C selectors required for runtime behavior, Android lifecycle methods, and public API entry points are excluded or normalized per the platform-exclusion appendix published with this standard (or a dated draft table referenced in the report).
- Do not treat MASTG-KNOW-0033 as a machine-readable allow-list; it is background knowledge only.

**Why High tier.** Identifier obfuscation alone does not prevent logic recovery, but its absence dramatically lowers reverse-engineering cost.

**OWASP mapping (informative).** Primary control: MASVS-RESILIENCE-3. Tests: MASTG-TEST-0051 (Android), MASTG-TEST-0093 (iOS). Background: MASTG-KNOW-0033.

### 5.2 P2 — String Protection (Critical) — Scoreable

**Definition.** The degree to which strings embedded in the application — URLs, error messages, log strings, format strings, internal API names, prompts — are protected from direct plaintext extraction.

**Threats addressed.** Static information disclosure, credential extraction, surface mapping.

**Scored core — Protected String Coverage with severity penalty (PSC).**

```text
T = count(eligible string literals across DEX/resources/native and
          Mach-O/ObjC/Swift/native string sections)
P = count(protected eligible strings)
S_w = severity-weighted sum of sensitive plaintext eligible strings
      (weights from frozen sensitive-pattern table in the standard)

PSC = 100 * P / max(T, 1)
SensitivePlaintextPenalty = min(40, S_w)
P2_sub_score = clamp(round(PSC - SensitivePlaintextPenalty), 0, 100)
```

**Protected** means: not statically recoverable as meaningful plaintext **and** represented by an encoded/encrypted blob **plus** a statically identifiable app-owned decode/decrypt reference. High entropy alone does **not** count as protected.

**Sensitive plaintext** patterns (URLs, hostnames, API paths, tokens/keys, auth headers, feature flags, internal service names, SQL/GraphQL operations, diagnostic/production log strings) are defined by a **normative table in the standard**, not by each implementation profile.

Eligible-string exclusions (platform constants, public permission names, localization-only UI strings) use deterministic rules documented in the same table set.

**Why Critical tier.** Plaintext strings are the most direct path to actionable intelligence about an application.

**OWASP mapping (informative).** Primary control: MASVS-RESILIENCE-3. Tests: MASTG-TEST-0051, MASTG-TEST-0093.

### 5.3 P3 — Embedded Secrets and Configuration (Critical) — Provisional

**Definition.** The presence and exposure of cryptographic material, API keys, tokens, certificates, and configuration files embedded directly in the application.

**Threats addressed.** Credential extraction (primary), static information disclosure.

**Concept (adopted).** Measure the proportion of credential candidates protected from direct static recovery, minus a **class-keyed** severity penalty for unprotected high-value secrets.

```text
ESPS = clamp(
  100 * protected_secret_candidates / max(credential_candidate_strings, 1)
  - secret_exposure_penalty,
  0, 100)
```

**Blocked until Appendix P3 freezes:**

1. Normative detector set (regexes, file types, decode rules, benign allow-list).
2. Class-keyed severity table (e.g. production cloud key vs test placeholder).
3. Rules for “protected” (encryption, splitting, transformation) with static recoverability tests.

Until then: implementations MAY emit a draft ESPS as `provisional: true` findings; ESPS **MUST NOT** enter the composite.

**Why Critical tier.** Embedded secrets are the most consequential category of static disclosure.

**OWASP mapping (informative).** Control: MASVS-RESILIENCE-3. Weakness context: MASWE-0005 (API keys hardcoded in the app package) — weakness, not a control. Tests: MASTG-TEST-0051, MASTG-TEST-0093.

### 5.4 P4 — Resource and Asset Naming (Moderate) — Scoreable

**Definition.** The semantic transparency of bundled resources and assets beyond code identifiers covered in P1 — layout files, drawable names, asset catalogs, raw resource files.

**Threats addressed.** Static logic recovery, surface mapping, asset extraction.

**Scored core — Resource and Asset Naming Protection Score (RANPS).**

```text
RANPS = clamp(
  100 * protected_names / max(eligible_resource_asset_names, 1)
  - leak_penalty,
  0, 100)
```

- **protected_names** use the same **syntactic opacity** engine as P1 (not dictionary semantics).
- Exclude names that must remain stable for platform contracts, public SDK/API contracts, app-store metadata, accessibility/user-facing strings, or runtime framework lookup (platform-exclusion appendix).
- **leak_penalty** uses exact path-context regexes with **caps** (debug/test/dummy names, source maps, mapping files, staging codenames). Where `test`/`dev`/`staging` can be legitimate user-facing content, those matches are advisory only.

**Why Moderate tier.** Resource naming rarely enables direct exploit; it mainly leaks feature names and developer intent.

**OWASP mapping (informative).** Primary: MASVS-RESILIENCE-3; MASTG-TEST-0051 / 0093. Do not cite Google Issue Tracker 240316983 as resource-name obfuscation evidence (class keep-rules only).

### 5.5 P5 — Manifest and Entry-Point Exposure (Moderate) — Scoreable

**Definition.** The information disclosed by the application's manifest or property list about its components, entry points, and external interfaces.

**Threats addressed.** Surface mapping (primary), tampering at rest.

**Scored core — Entry-Point Exposure Protection Score (EPES).**

```text
EPES = clamp(
  100
  - semantic_name_penalty
  - exported_surface_penalty
  - debug_backup_cleartext_penalty
  - deeplink_penalty,
  0, 100)
```

Each penalty is mechanical and capped:

1. **semantic_name_penalty** — component/service/provider/receiver/URL-scheme/host/path names matching frozen “revealing” patterns (syntactic / allow-list based, not open-ended NLP).
2. **exported_surface_penalty** — exported or externally reachable entry points; reduced weight where signature permissions, verified app links, or explicit allow-lists apply.
3. **debug_backup_cleartext_penalty** — `android:debuggable`, backup-allowed, cleartext-traffic, and iOS equivalents, with platform-version defaults specified in the appendix.
4. **deeplink_penalty** — broad or sensitive deep links / custom URL schemes.

P5 is partly obfuscation measurement and partly security configuration; both are static package facts and are intentionally included.

**Why Moderate tier.** Manifest exposure reveals structure even when code is well obfuscated, but direct exploitability is generally lower than Critical pillars.

**OWASP mapping (informative).** MASVS-RESILIENCE-3 (naming/surface); MASVS-RESILIENCE-2 where integrity/tamper flags apply. Tests: MASTG-TEST-0051; MASTG-TEST-0226 (debuggable); MASTG-TEST-0028 (deep links).

### 5.6 P6 — Control Flow Obfuscation (Critical) — Advisory only (v0.2)

**Definition.** The transformation of program control flow to resist understanding from disassembly or decompilation. Techniques include control flow flattening, opaque predicate insertion, junk code, instruction substitution, and signature-disrupting transformations.

**Threats addressed.** Static logic recovery (primary).

**Status.** **Not scoreable under §6.4 in v0.2.** CFG recovery, opaque-predicate detection, and “introduced by obfuscation” attribution are not reproducible across independent disassemblers without a canonical bytecode/ISA CFG-extraction algorithm, a frozen library-exclusion corpus, and exact syntactic signatures.

**Advisory (non-scoring) signals implementations MAY report:**

- Presence of dispatcher-like state-variable + switch/indirect-branch patterns
- Regions with anomalous basic-block structure relative to app-code norms
- Vendor-documented control-flow protection markers when statically observable

These MUST appear under `advisory` / findings only. They MUST NOT produce a composite-feeding sub-score. P6 weight is **removed from the composite denominator** until redesign.

**Why still Critical as a concept.** Once an attacker has located a function of interest, control-flow hardness dominates reconstruction cost. The tier reflects threat importance, not current scoreability.

**OWASP mapping (informative).** Primary control: MASVS-RESILIENCE-3. Tests: MASTG-TEST-0051 / 0093. MASVS-RESILIENCE-4 is dynamic-analysis resistance and is **not** the primary mapping for this static pillar.

**Redesign gate.** Promote to Scoreable only after: (1) canonical CFG extraction algorithm published, (2) frozen exclusion corpus, (3) exact CFF/opaque/bogus signatures, (4) conformance vectors with expected scores.

### 5.7 P7 — Native Code Protection (High) — Scoreable

**Definition.** Protections applied to native code components — shared libraries on Android, embedded native code on iOS — including symbol stripping, debug symbol removal, section encryption, and packing.

**Threats addressed.** Static logic recovery, static information disclosure.

**Scored core — Native Protection Score (NPS).**

```text
NPS = clamp(round(100 * (
  0.35 * stripped_or_obfuscated_native_symbols_ratio
+ 0.20 * debug_info_absence_ratio
+ 0.20 * hardening_features_ratio
+ 0.15 * high_entropy_or_packed_section_signal
+ 0.10 * native_integrity_or_anti_tamper_reference_signal
)), 0, 100)
```

**Denominator / stripped libraries.** A fully stripped library with **no** usable symbol table is the **protected** case for the symbol component (ratio = 1.0 for that library), not “undefined.”

**Component definitions (summary):**

- **stripped_or_obfuscated_native_symbols_ratio** — stripped, local-only, or non-semantic symbols over all native symbols where tables exist; full strip ⇒ protected.
- **debug_info_absence_ratio** — native libraries without DWARF/debug sections over total native libraries.
- **hardening_features_ratio** — normalized static presence of PIE/PIC, stack canaries / SSP, RELRO/NX, and platform-applicable PAC bits per published platform check tables (e.g. readelf/otool-class checks).
- **high_entropy_or_packed_section_signal** — entropy threshold **paired with** a packer signature or decode reference; high entropy alone is advisory, not full credit.
- **native_integrity_or_anti_tamper_reference_signal** — capped static references to integrity/tamper logic. Anti-debug behavioral claims that require execution are out of scope and MUST NOT dominate this component.

**Why High tier.** Native code increasingly holds performance-critical logic and protection libraries themselves.

**OWASP mapping (informative).** MASVS-RESILIENCE-2 (tamper/integrity), MASVS-RESILIENCE-3 (anti-static). Strongest direct test: MASTG-TEST-0044 (PIE / stack-smashing). Also MASTG-TEST-0051 / 0093; MASTG-TEST-0047 for integrity cross-ref.

### 5.8 P8 — Reflection and Indirection (High) — Advisory only (v0.2)

**Definition.** The use of reflective and indirect method invocation to hide sensitive behavior from static analysis.

**Threats addressed.** Static logic recovery.

**Status.** **Not scoreable in v0.2.** A “sensitive-keyword over class/method names” filter is self-defeating once P1 renaming succeeds and perversely rewards keeping names like `license` / `crypto`. No deterministic sensitive-proximity model is available yet.

**Advisory (non-scoring) signals implementations MAY report:**

- Global indirection *texture* (rates of reflective APIs, dynamic class load, `objc_msgSend` density, JNI bridge density) without sensitive-site weighting
- Obvious dynamic class-loading of encrypted secondary DEX/JAR when paired with static blob references (may also inform P2/P9 advisory)

These MUST NOT produce a composite-feeding sub-score. P8 weight is **removed from the composite denominator** until a redesign lands.

**OWASP mapping (informative).** Primary control: MASVS-RESILIENCE-3. Tests: MASTG-TEST-0051 / 0093. MASWE-0090 is **not** a valid P8 mapping (resource/asset related).

### 5.9 P9 — Bundled Asset Protection (Critical) — Provisional

**Definition.** The protection of supplemental files bundled inside the application package, including machine learning models, interpreted code bundles, embedded databases, cryptographic material, and other tamper-relevant resources.

**Threats addressed.** Asset extraction and reuse, tampering at rest, IP extraction.

**Concept (adopted).** Severity-weighted coverage of sensitive bundled assets protected from direct static reading or replacement:

```text
BAPS = clamp(
  100 * protected_sensitive_assets / max(total_sensitive_bundled_assets, 1)
  - asset_exposure_penalty,
  0, 100)
```

Severity weighting is required so one readable ML model or JS bundle cannot be diluted by many protected trivia files.

**Blocked until Appendix P9 freezes:**

1. Asset taxonomy by magic-byte / path patterns.
2. Read-protection and integrity-reference rules.
3. Class-keyed severity table.

Until then: draft BAPS may be reported as provisional findings only; **MUST NOT** enter the composite.

**Why Critical tier.** Bundled assets often contain the application's most valuable IP and are extractable with effort far below attacking compiled code.

**OWASP mapping (informative).** Controls: MASVS-RESILIENCE-2 (anti-tamper for code/resources), MASVS-RESILIENCE-3. Tests: MASTG-TEST-0047 (Android file integrity), MASTG-TEST-0090 (iOS file integrity), plus obfuscation tests 0051/0093. Threat context: OWASP Mobile Top 10 2024 M7; 2016 M9 — context, not controls.

---

## 6. Scoring Methodology

### 6.1 Sub-scores

Each **scoreable** pillar produces a sub-score on a scale of 0 to 100, where 0 indicates no observable protection under that pillar's core rules and 100 indicates maximum observable protection under those rules.

Provisional pillars may emit draft sub-scores only with `provisional: true` and MUST be excluded from the composite.

Advisory pillars MUST NOT emit composite-feeding sub-scores.

### 6.2 Tier weighting and composite (v0.2)

Pillars are weighted by tier when they are **scoreable** (or later promoted from provisional/advisory):

| Tier | Weight | Pillars (conceptual) |
|---|---|---|
| **Critical** | 1.0 | P2, P3, P6, P9 |
| **High** | 0.6 | P1, P7, P8 |
| **Moderate** | 0.4 | P4, P5 |

**v0.2 composite denominator** includes only pillars with status **Scoreable**:

| Included now | Weight |
|---|---|
| P2 Critical | 1.0 |
| P1 High | 0.6 |
| P7 High | 0.6 |
| P4 Moderate | 0.4 |
| P5 Moderate | 0.4 |
| **Sum W** | **3.0** |

Excluded from denominator until promoted: P3 (provisional), P6 (advisory), P8 (advisory), P9 (provisional).

```text
Composite = (Σ_{p in scoreable} pillar_sub_score(p) × tier_weight(p))
            / (Σ_{p in scoreable} tier_weight(p))
```

When a provisional pillar's appendix freezes, add its weight to the denominator in the same minor/major version that marks it Scoreable. Do not silently drop missing pillars without documenting the active denominator set in the report (`score.denominator_pillars`).

### 6.3 Letter grades

The composite score maps to a letter grade:

| Composite | Grade | Interpretation |
|---|---|---|
| 90–100 | A | High resilience |
| 80–89 | B | Strong protection with minor gaps |
| 70–79 | C | Moderate protection; meaningful gaps |
| 60–69 | D | Weak protection; critical gaps |
| 0–59 | F | Critical failure; insufficient protection |

Grade boundaries are calibrated against the threat model in Section 4 and may be adjusted in subsequent versions based on industry data and the conformance corpus.

Verbal band labels under individual pillars (e.g. “weak / moderate / strong”) are **non-normative** aids; the numeric sub-score is authoritative.

### 6.4 Determinism and comparability guarantee

OBSCURUM scores must be trustworthy across runs and comparable across implementations. v0.2 replaces a pure “byte-for-byte across any toolchains” promise with a two-part model:

#### 6.4.1 Intra-implementation determinism (mandatory)

Given the same binary, the same standard version (including appendix versions), and the same implementation version, a conforming implementation MUST produce identical composite scores, identical scoreable sub-scores, and identical structured finding IDs on every run.

#### 6.4.2 Cross-implementation agreement (mandatory via conformance suite)

Independent conforming implementations MUST agree on the published **conformance test-vector suite**: for each reference binary, sub-scores and composite MUST match the canonical expected values within published **tolerance bands** (default: exact match for file-format-derived pillars; stated ±N for pillars that depend on disassembly-class extraction once those are scoreable).

Until the suite is published, implementations MUST:

1. Document extraction rules and appendix table versions in the report.
2. Mark any sub-score that depends on non-frozen tables as `provisional: true`.
3. Not claim cross-implementation interchange without suite evidence.

#### 6.4.3 Scored core vs advisory layer

Only the **scored core** of each scoreable pillar feeds numerical scores. Advisory output (semantic labels, remediation prose, optional ML assists, dictionary “meaningful name” hints, entropy-only packing suspicions, P6/P8 texture notes) MUST be clearly separated in the report schema and MUST NOT feed back into any sub-score or the composite.

#### 6.4.4 What this is not

- It is not a license to ship non-repeatable scores inside one product.
- It is not permission to change tier weights or grade boundaries in a “conforming” fork.
- It does not require a single commercial disassembler; it does require published extraction rules and suite agreement where cross-impl claims are made.

---

## 7. Reporting and Interoperability

### 7.1 Conformance schema

A conforming implementation must produce reports in a defined JSON structure. This schema enables scores from different implementations to be compared, archived, and ingested by other tools without translation.

```json
{
  "obscurum_version": "0.2",
  "appendix_versions": {
    "P1_exclusions": "string|null",
    "P2_patterns": "string|null",
    "P3_detectors": "string|null",
    "P7_platform_checks": "string|null",
    "P9_taxonomy": "string|null"
  },
  "implementation": {
    "name": "string",
    "version": "string"
  },
  "target": {
    "platform": "android | ios",
    "package_identifier": "string",
    "sha256": "string"
  },
  "score": {
    "composite": "0-100|null",
    "grade": "A|B|C|D|F|null",
    "denominator_pillars": ["P1", "P2", "P4", "P5", "P7"]
  },
  "pillars": [
    {
      "id": "P1",
      "name": "string",
      "tier": "critical|high|moderate",
      "status": "scoreable|provisional|advisory",
      "sub_score": "0-100|null",
      "provisional": false,
      "findings": [
        {
          "severity": "string",
          "description": "string",
          "location": "string"
        }
      ]
    }
  ],
  "advisory": {
    "summary": "string",
    "remediation": [],
    "p6_control_flow_notes": [],
    "p8_indirection_notes": []
  }
}
```

Implementations may extend the schema with additional fields for their own use, but conforming reports must include all required fields at the structure shown.

### 7.2 Human-readable output

Implementations are expected to produce at least one human-readable rendering of the report — typically HTML or PDF — in addition to the canonical JSON form. The human-readable output is not standardized; the JSON form is.

### 7.3 Score comparability

Two OBSCURUM scores are comparable when they were produced against the same standard version **and** the same appendix versions, with the same `denominator_pillars` set. Scores against different versions or denominators are not directly comparable and must be re-scored before comparison.

---

## 8. Conformance Principles

### 8.1 What a conforming implementation must do

- Accept a shipping `.apk` or `.ipa` as input without requiring source code
- Produce a sub-score for each **scoreable** pillar, using only scored-core rules
- Omit composite contribution for provisional and advisory pillars
- Compute the composite score and letter grade using §6 with an explicit denominator set
- Produce a report in the schema defined in §7
- Satisfy §6.4 intra-implementation determinism
- Clearly separate advisory output from numerical scores

### 8.2 What a conforming implementation may do

- Emit provisional draft scores for P3/P9 when draft tables are declared, marked provisional
- Report P6/P8 advisory findings
- Layer optional advisory or AI-assisted interpretation on top of deterministic scoring
- Provide additional analysis beyond the nine pillars, clearly distinguished from OBSCURUM scoring
- Implement performance optimizations, caching, or parallel processing, provided determinism is preserved
- Charge for the service, distribute as open source, or operate as an internal-only tool

### 8.3 What a conforming implementation must not do

- Produce non-deterministic scores for the same implementation inputs
- Include advisory outputs in the numerical score
- Feed provisional pillar scores into the composite before appendix freeze
- Modify the scoring rubric, tier assignments, or grade boundaries while claiming conformance
- Use the OBSCURUM name to describe outputs that do not conform to this standard

### 8.4 Multiple implementers expected

OBSCURUM expects multiple conforming implementations to exist — open source, commercial, internal to organizations, and academic. Conformance is defined by the rubric and §6.4, not by any single implementer's authority. Material disagreement on suite vectors indicates an implementation or standard bug, not a difference of marketing opinion.

---

## 9. Relationship to OWASP MASVS-RESILIENCE

OBSCURUM is designed as a measurement implementation of the obfuscation requirements expressed in the OWASP Mobile Application Security Verification Standard, MASVS-RESILIENCE category.

### 9.1 Citation classes (do not mix in conformance claims)

| Class | Role in OBSCURUM |
|---|---|
| **MASVS-*** controls | Primary control mappings |
| **MASTG-TEST-*** | Test procedure mappings |
| **MASWE-*** | Weakness context (not controls) |
| **Mobile Top 10** (e.g. M7, M9) | Threat landscape context only |

### 9.2 Pillar → OWASP map (informative summary)

| Pillar | Primary MASVS | Primary MASTG tests | Notes |
|---|---|---|---|
| P1 | RESILIENCE-3 | 0051, 0093 | KNOW-0033 background only |
| P2 | RESILIENCE-3 | 0051, 0093 | |
| P3 | RESILIENCE-3 | 0051, 0093 | MASWE-0005 weakness context |
| P4 | RESILIENCE-3 | 0051, 0093 | |
| P5 | RESILIENCE-3 (+ RES-2 as relevant) | 0051, 0226, 0028 | Config + exposure |
| P6 | RESILIENCE-3 | 0051, 0093 | RES-4 not primary (dynamic) |
| P7 | RESILIENCE-2 + RESILIENCE-3 | 0044, 0051, 0093 | 0047 integrity xref |
| P8 | RESILIENCE-3 | 0051, 0093 | Not MASWE-0090 |
| P9 | RESILIENCE-2 + RESILIENCE-3 | 0047, 0090, 0051, 0093 | M7/M9 threat context |

MASVS and MASTG define *what* should be tested and *how a tester might evaluate it*; they do not define a reproducible scoring rubric. OBSCURUM fills that measurement gap. It is complementary infrastructure, not a competing control framework. Improvements may be proposed upstream to MASVS through that project's process.

---

## 10. Scope of Application

OBSCURUM is intended to be applied to mobile applications that the user has legal authority to analyze. This includes:

- Applications the user has developed or commissioned
- Applications under bug bounty programs whose scope permits this analysis
- Applications for which contractual permission has been granted
- Open-source applications under licenses permitting reverse engineering
- Research samples used for the development of the standard itself, acquired through legitimate channels

The standard does not specify acquisition methods. Users of OBSCURUM and operators of conforming implementations are responsible for ensuring they have legal authority to analyze any binary they submit for scoring. The standard itself takes no position on jurisdiction-specific reverse engineering law, contract law, or terms-of-service compliance; those are the user's responsibility.

---

## 11. Governance and Versioning

### 11.1 Openness

OBSCURUM is published openly. The standard, its rubric, its scoring methodology, and its reporting schema may be implemented by any party without license or fee. The OBSCURUM name and any associated marks are protected to prevent misuse — claims of OBSCURUM conformance by non-conforming implementations — but the standard itself is unencumbered.

### 11.2 Versioning

OBSCURUM follows semantic versioning principles adapted for a measurement standard:

- A **major** version increment indicates rubric changes that would yield different composite scores on the same binary under the same denominator
- A **minor** version increment indicates clarifications, expanded metric definitions, appendix freezes that promote provisional pillars, or additions that do not change scoring outputs on existing binaries under the prior denominator
- A **patch** version increment indicates editorial corrections

Promoting P3/P6/P8/P9 into the composite denominator is a **major** or carefully versioned **minor** change and MUST bump `obscurum_version` and document denominator migration.

Target cadence: minor versions as needed, major versions annually or when warranted by significant changes in the mobile threat landscape.

### 11.3 Change management

Proposed changes to the standard are tracked publicly. Changes are categorized as editorial, metric refinement, scope change, pillar change, tier or weighting change, appendix freeze, or governance change. Each category has documented review requirements proportional to its impact on existing scores.

### 11.4 Editorial stewardship

In version 0.2, editorial stewardship rests with the standard's original author. Transition to a small multi-party editorial board is anticipated once the standard has external adopters and a sustained pattern of external contribution. Long-term, the standard may be transferred to a recognized standards organization for ongoing maintenance.

### 11.5 Conformance program

This standard does not establish a paid certification program. Conformance is a property of an implementation, defined by Section 8. Recognition or certification programs, if established, are governed separately, are not required for use of the standard, and are not part of this document.

---

## 12. Glossary

**APK** — Android Package, the distribution format for Android applications.

**Advisory layer** — Non-scoring output that must not feed sub-scores or the composite.

**Bytecode** — Compiled intermediate representation, typically for a virtual machine.

**Composite score** — The single 0–100 value produced by tier-weighted aggregation of **scoreable** pillar sub-scores.

**Conformance suite** — Published reference binaries and expected scores used for cross-implementation agreement.

**Conforming implementation** — An OBSCURUM scorer that satisfies Section 8 requirements.

**Denominator pillars** — The set of pillars included in the composite for a given standard version.

**Grade** — The letter mapping (A–F) of the composite score per Section 6.3.

**IPA** — iOS App Store Package, the distribution format for iOS applications.

**MASTG** — OWASP Mobile Application Security Testing Guide.

**MASVS** — OWASP Mobile Application Security Verification Standard.

**MASVS-RESILIENCE** — The category of MASVS controls addressing resistance to reverse engineering and tampering.

**Obfuscation** — Any transformation applied to a mobile application or its bundled assets that increases the time, skill, or cost required to extract, understand, or modify the original content, while preserving observable application behavior.

**Pillar** — One of the nine measurement categories defined in Section 5.

**Provisional pillar** — Concept adopted; numeric contribution blocked until normative appendix freeze.

**Scored core** — The deterministic rules that produce a pillar sub-score.

**Static analysis** — Examination of a binary without executing it.

**Sub-score** — The 0–100 value produced for a single scoreable pillar.

**Tier** — Critical, High, or Moderate, as assigned to each pillar per Section 6.2.

---

## 13. Changelog (v0.1 → v0.2)

- Added per-pillar **scoreable / provisional / advisory** status table (§5.0).
- Merged measurement methods from 2026-06 research and proposed edits, **as revised by** the 2026-06-15 metric debate adjudication.
- **P1, P2, P4, P5, P7:** concrete scored-core formulas; syntactic opacity; entropy/decode pairing rules.
- **P3, P9:** concept adopted; composite blocked pending Appendix P3/P9.
- **P6, P8:** removed from composite denominator; advisory-only pending redesign.
- **§6.2:** v0.2 denominator = P1+P2+P4+P5+P7 (weights sum 3.0).
- **§6.4:** replaced absolute cross-tool byte-for-byte claim with intra-impl determinism + conformance-suite cross-impl agreement + scored-core/advisory split.
- **§7:** report schema gains `status`, `provisional`, `denominator_pillars`, `appendix_versions`.
- **§9:** citation-class hygiene (controls vs tests vs weaknesses vs Top 10 context).
- Editorial: scope separated from related internal work; this document remains the standard only.

Open work toward v0.3 / v1.0 readiness:

1. Freeze Appendices P1 exclusions, P2 patterns, P5 penalty tables, P7 platform checks (proof pattern).
2. Freeze Appendices P3 and P9 → promote to scoreable.
3. Redesign P6 (canonical CFG) and reconsider P8 texture metric.
4. Publish initial conformance test-vector corpus.
5. Calibrate grade boundaries on a labeled corpus.

---

*This is a working draft. The standard's mission — getting the global mobile software industry to think about obfuscation scoring in one consistent way — depends on contribution, critique, and adoption. Comments, corrections, and proposed extensions are welcome.*

*End of OBSCURUM v0.2*

---

*Licensed under [CC BY 4.0](../LICENSE) per the repository LICENSE.*
