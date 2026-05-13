# environment.* is now an Internet-Draft

**By Michael Msebenzi (Headless Oracle)**
**v1.0.0 · May 13, 2026**

On May 11, 2026, I filed the family-definition specification for the `environment.*` constraint family with the Internet Engineering Task Force. The draft is live at the IETF Datatracker, archived in the canonical Internet-Draft repository, and citable as a stable URL.

The draft: [draft-borthwick-msebenzi-environment-state-00](https://datatracker.ietf.org/doc/draft-borthwick-msebenzi-environment-state/). Co-authored with Douglas Borthwick (InsumerAPI), who maintains the sibling spec for `environment.wallet_state`.

This is a short post explaining what the document does, what it doesn't do, and why a family-definition is the load-bearing artifact rather than any single constraint type.

## What the draft does

The Verifiable Intent (VI) mandate format authorizes autonomous agents to act on behalf of human principals through cryptographically signed constraint instances bound into Layer 2 mandates. VI's existing constraint families describe properties of the transaction itself — what is being purchased, by whom, for how much, against which credential.

They do not describe properties of the *environment* in which the transaction is executed: whether the venue is open, whether the source of funds is funded, whether other relevant external conditions hold at the moment of execution.

`environment.*` is the constraint family that fills that gap.

The Internet-Draft specifies, in normative text:

- The **membership criterion** under which a constraint type qualifies as a member of the family — a single architectural rule: the type's failure mode must be gating. A failure terminates execution rather than degrading, retrying, or partial-fulfilling.
- The **family-wide vocabulary** every member uses (`attestation_url`, `max_attestation_age`, the field-scope taxonomy).
- The **composition discipline** by which family members compose with each other and with constraints from other families. The family is a conjunction; any failure blocks execution.
- The **register discipline** under which family-wide and per-type prose is written, so that a third implementer reading the spec for the first time can tell which fields share semantics across types and which are type-local.
- The **family-wide security considerations** — TOCTOU freshness, fail-closed posture, attestation provider neutrality, algorithm agility.
- The **IANA registry mechanics** under which new family members are registered.

The family integrates with the existing VI vocabulary at a single point: `environment.*` constraints appear only in open mandates (Layer 2 mandates carrying constraints rather than final values), and host validators that do not recognize the namespace reject those mandates fail-closed. No modification to the VI host grammar is required.

What the draft deliberately does *not* do: define any individual constraint type. The two existing types — `environment.market_state` (signed attestation of exchange session state, what Headless Oracle ships) and `environment.wallet_state` (signed attestation of on-chain payment-source state, what InsumerAPI ships) — are referenced informatively in Appendix A. Each has its own canonical reference. Each is independently signed by an independent issuer. Each lives in its own repository, on its own infrastructure, under its own license.

The family-definition is the layer above all of that. It is the document that says: *here is the shape of this category of constraint, here is what membership means, here is how new members are added, here is what every member must share.*

## Why a family-definition is the load-bearing artifact

Consider what the Verifiable Intent vocabulary needs in order to scale beyond its current set of transactional constraint types. New types will be proposed. Some will be proposed by Mastercard. Some will be proposed by Google as AP2 absorbs new mandate semantics. Some will be proposed by Visa as Trusted Agent Protocol extends. Some will be proposed by independent contributors.

The question that every proposal raises: *is this type a constraint on the transaction, or a constraint on the environment in which the transaction executes?*

The answer matters operationally. Constraints on the transaction can fail in ways that are negotiable — a price can be re-quoted, a credential can be re-issued, a counterparty can be substituted. Constraints on the environment cannot. If the exchange is closed, the trade doesn't execute. If the source wallet is empty, the payment doesn't clear. If the regulatory status of the counterparty has changed, the engagement doesn't proceed. The failure modes are not survivable, which means they must gate execution rather than be co-equal with the transactional layer.

Without a family-definition, every proposer reinvents this architectural decision from scratch — and some reinvent it incorrectly. With a family-definition, the question becomes machine-readable: does the proposed type satisfy the membership criterion? If yes, it inherits the family's discipline. If no, it belongs in a different family.

This is the kind of work that does not look strategic until you watch a vocabulary fragment in real time. The `environment.*` family-definition is the artifact that prevents fragmentation. It is the spec-level decision that says: *we have already had this conversation; here is what we decided; here is how to apply that decision to new types.*

## Verifiable intent as a primitive, not a product

A larger thesis sits underneath the family-definition work.

The autonomous agent economy that is now visibly under construction — Mastercard's Verifiable Intent, Google's AP2, Visa's TAP, Stripe's ACP and MPP, Coinbase's x402, every adjacent protocol effort — shares a single architectural commitment. Every agent-initiated transaction must be evidence-bearing. Not "trustworthy" in the social sense. Not "auditable" in the post-hoc sense. *Evidence-bearing*: cryptographically signed, independently verifiable, replay-resistant, attributable to a specific principal acting under specific authorization.

This is what verifiable intent means when stripped of any company's branding: the property that an agent's action carries with it the cryptographic evidence of its authorization, the constraints that were checked, and the environment in which it executed.

Verifiable intent in this sense has three signatures:

- The principal's signature on the mandate (who authorized this action and under what constraints).
- The agent's signature on the execution (which agent acted, with which identity).
- The environment's signature on the conditions (was the venue open, was the wallet funded, was the regulatory status valid — at the moment the agent acted).

The first two signatures are increasingly well-specified across competing standards efforts. The third — an attestation of the conditions in which the agent acted — is what `environment.*` is a family-shaped approach to. There are other possible approaches to the third-signature problem, and none is yet canonical. What distinguishes a family-shaped approach is that the discipline is set once, at the membership-criterion layer, rather than re-litigated per attestation type. That is what makes it a primitive rather than a product.

## What this Internet-Draft is and isn't

Filing an Internet-Draft is the lowest threshold of standards work. It is not a Working Group adoption. It is not a Request For Comments. It is a publication and a citable archive. It says: *here is a specification I am putting on the public record, under a specific authorship attribution, at a specific moment in time.*

What it does provide:
- A permanent, citable URL at `datatracker.ietf.org`.
- An archived plaintext, HTML, and XML rendering in the canonical IETF format.
- An expiration date six months out, at which point the draft must be re-filed or evolved to a new revision.
- A document that is unambiguously authored — the names attached to it cannot be substituted later.

What it does not provide:
- Working Group consensus.
- IETF stream publication.
- Any guarantee that the spec will progress to RFC status.

The drafts that become RFCs typically take three to five years and pass through multiple rounds of working group review. This document may take that path or it may not. Whether it does or doesn't, the version that exists today exists. The family-definition is now in the archive. The shape of the constraint family is on the public record under specific attribution. That is the primary value of filing, and the rest is downstream optionality.

## What comes next

The plan from here is straightforward.

The two existing constraint types continue to mature in their own GitHub repositories — `environment.market_state` in PR #9 against `agent-intent/verifiable-intent`, `environment.wallet_state` in PR #22. Each is currently at v0.5.11 and v0.6.5 draft respectively. Each ships with byte-identical family-wide prose on the shared sections (composition discipline, freshness semantics, algorithm agility, JWKS caching) and with deliberately divergent prose on the per-type sections.

The family will likely accrete additional members over the coming year. Candidates that satisfy the membership criterion include `environment.regulatory_status` (signed attestation of a counterparty's sanctions and jurisdictional status), `environment.custody_state` (signed attestation of a custodian's solvency and operational posture), and `environment.counterparty_credit` (signed attestation of a counterparty's credit and capital state). Each would be authored by parties with the operational standing to issue such attestations credibly. The family-definition's membership criterion is the test each proposal will be checked against.

For Headless Oracle specifically, the focus remains on `environment.market_state` as a primitive that any autonomous agent transacting against any global venue can rely on. Signed receipts. Independent verification. Replay-safe. Fail-closed.

The draft is a milestone, not a destination. The work continues.

—

*Michael Msebenzi*
*Headless Oracle*
*May 13, 2026*
