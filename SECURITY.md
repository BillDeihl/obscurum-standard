# Security Policy

OBSCURUM is a **paper standard**. This repository contains prose, not
software. There is no package to install, no binary to run, and no
dependency tree to patch. That shapes what "security issue" means here.

## What belongs in this repo

**Defects in the standard itself.** A scoring rule can be wrong in ways that
have real security consequences — for example, a rule that:

- awards a high score to a build an attacker can trivially recover strings from,
- is gameable by a cosmetic change that adds no real resistance,
- misstates an OWASP MASVS or MASTG mapping in a way a reader would act on, or
- would push an implementer toward a weaker configuration than they had.

These are the highest-value reports we receive. Open a
[public issue](https://github.com/BillDeihl/obscurum-standard/issues) with
the section, the claim, and your reasoning. Most of them belong in the open,
where they can be argued and cited.

## What does NOT belong in this repo

**A vulnerability in someone else's app.** If you used OBSCURUM to assess a
released application and found a real weakness in it, that finding belongs
with that application's vendor under their disclosure policy — not here. Do
not post teardowns, extracted keys, or recovered source of third-party apps
in issues or pull requests. Reports of that kind will be deleted.

**Bypass playbooks.** Per [CONTRIBUTING.md](CONTRIBUTING.md), this project
does not publish acquisition or exploitation walkthroughs. Describe *that* a
protection fails and *what class* of analysis defeats it. Do not supply a
working recipe.

## Reporting privately

Use a private channel when disclosing publicly would cause harm before it
causes improvement — for example, a defect in the standard that names a
specific shipping product's weakness, or anything you believe is
embargoed elsewhere.

Report privately via GitHub's
[private vulnerability reporting](https://github.com/BillDeihl/obscurum-standard/security/advisories/new).
It goes only to the maintainer.

Expect an acknowledgement within **5 business days** and a substantive reply
within **30 days**. This is a small, volunteer-maintained project; those are
targets, not contractual guarantees.

## Supported versions

The standard is pre-1.0 and unversioned. Only the current `main` is
maintained. Earlier drafts are historical and receive no corrections.

## Credit

Corrections that materially change a scoring rule are credited in the commit
and in the changelog once one exists, unless you ask to stay anonymous.
