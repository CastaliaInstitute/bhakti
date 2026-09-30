# Critical Review — a-guru-edge.md v0.1

**Scope.** Review of the edge-service specification against the goal (live access-controlled chat: limited free access → membership/pay for AI tokens), the persona contract, and the project's honesty norms. Conducted 2026-09-29. Findings first, fixes applied inline in `a-guru-edge.md` where flagged "applied."

---

## W1. Route decision — affirmed, with one correction of my own stated rationale

The thin-dedicated-edge decision survives scrutiny, but the §1 rationale cited a defect ask-faculty does not have: `ask-faculty` *already exposes* `enable_fidelity_check`, `output_contract` (with `required_anchors`), and `rag_exclude` (held-out evaluation targets). The genuine residual reasons for a thin edge remain: per-claim provenance return (ask-faculty returns none), metering/gating (no concept of it), dial policy (not expressible as contextual modes), and the consented person store. The edge should *reuse* `output_contract.required_anchors` and fidelity checks in its ask-faculty sub-calls rather than reimplement them. *(Correction noted in spec §1.)*

## W2. Monetization defects

- **W2.1 Unit of metering is pedagogically hostile (severity: high).** "N = 5 questions / visitor / 24 h" charges by *turn*, but Dialogue and Counsel modes are precisely where a.guru asks multiple questions back. Counting turns penalizes the user for the project's own pedagogy — and creates an incentive to make a.guru *less* dialogical on free tiers, i.e., to monetize away the best part of the instrument. **Fix (applied):** the free unit is an *exchange* — everything between one user-initiated question and the user's next turn counts once, however many a.guru turns it takes. Tokens, not turns, remain the internal cost unit everywhere.
- **W2.2 The dial must never be tiered (severity: high).** If `challenge` or `teach` were member-only, the *honesty of the instrument itself* becomes paywalled — a spiritual-quality paywall, and a category violation given the persona contract. **Fix (applied, new §5.1 principle):** policy (all dials, refusals, provenance completeness, honesty) is identical in every tier; depth (retrieval breadth, lens fan-out count, model size, monthly token ceiling) is what membership buys.
- **W2.3 Member downgrade on outage harms paying users (severity: medium).** "Open-open on outage" currently reads as *fall back to anonymous free tier* — which would clamp a paying member to the free cap mid-session. **Fix (applied):** a valid auth JWT alone (verifiable locally, no auth-service round-trip) grants a safe default member ceiling flagged `degraded: true, meter_debt: true`; tier lookup is re-restated when the service returns. Fail-open with metering debt, never fail-closed.
- **W2.4 Abuse economics of the free tier are unaddressed (severity: medium).** `verify_jwt: false` + client-side `person_ref` means a scripted caller can steamroll the anonymous tier; daily-hash rotation adds no protection (fresh hash each day ⇒ fresh budget). Honest position: **the free tier is paced, not abuse-proof.** Institutional token budget caps the blast radius; abuse is accepted as research cost and *disclosed* in the spec rather than pretended away. **Fix (applied):** sticky 24h key (no rotation theater), documented "paced-not-enforced" stance, and an institutional daily free-token ceiling.
- **W2.5 Lens fan-out is a cost amplifier parked on the cheapest tier (severity: medium).** `compare` mode = multiple ask-faculty sub-calls, each with its own RAG + LLM generation — the most expensive mode offered to anonymous visitors. **Fix (applied):** per-tier retrieval budget: anonymous ≤ 1 lens call and no `compare`; members ≤ 3 lenses, `compare` allowed. E-run sessions must log tier so evaluations don't confound quality with depth (feeds REVIEW.md E1/E7 validity).
- **W2.6 Scope discipline on "pay" (severity: note).** The goal says "require membership / pay"; the στ spec consumes *existing* membership status (Supabase auth + custodian/patron tiers — already the institute's rails). Building new payment capture is out of the MVP checkpoint set by design; paywall copy directs to the join page. State said explicitly in §5 so nobody silently grows the goal.

## W3. Privacy and disclosure defects

- **W3.1 "Anonymized IP hash (rotated daily)" is protective-sounding noise.** Rotation changes nothing about enforcement and implies more protection than exists. Sticky 24h window, disclosed in the widget footer as what it is: "the instrument counts your use for the day by a per-device key; nothing human-readable is stored." *(Applied.)*
- **W3.2 No retention policy for quota or usage rows.** **Fix (applied):** free-quota rows pruned at 48 h; member usage rows keyed by member ID pseudonym only, aggregated for reporting; PII never attached.
- **W3.3 Persona-bundle drift.** The edge loading `persona/a-guru.json` "at cold start" is underspecified — from where? A duplicated copy in the function directory can drift from the bhakti repo's canonical file. **Fix (applied):** bundled copy pinned by `AGURU_PERSONA_SHA`; the edge refuses to serve (honest-degradation payload) if the sha of the bundled persona does not match the deployed pin, and every session logs the sha in `eval_note`.

## W4. Enforcement-fidelity honesty deficit

§3's "hard strings, rejected in output even if model-generated" overpromises: string matching is evadable by paraphrase; "attainment" claims are semantically definable and not listable. **Fix (applied):** enforcement is *belt and braces, best-effort* — prompt-level constraints first, string gate last; and the residual leakage rate (guard-bypass per 1,000 sampled responses) becomes an E2-measured quantity, with the spec saying so plainly rather than implying determinism.
**W4.1 (new defect found by this review's own fix):** the validator path (E-run/curl loops) must not burn the very anonymous quota it is testing — a **`AGURU_VALIDATOR_KEY`** allowlist with its own pre-paid budget is added (§5.3); the key exists only in env + evaluation configs, never in client code.

## W5. Widget contract gap (interface, not edge)

The widget's catch-all error path will render a deliberate 429 as *"The a.guru engine did not respond"* — wrong genre of message (limit status ≠ malfunction) and, per its current copy, mildly dishonest about why nothing came back. **Required (blocked on implementation):** widget handles `429 needs_membership` as a distinct, styled, honest status — instrument copy, zero upsell pressure, with the non-sales alternatives ("the sources remain free on-site; sit with the question"). Filed to, and patched in, the current widget contract (a-guru-edge.md §2 notes the rendering rule).

## Verdict

**Route: sound. Metering: needs the fixes now applied.** The freemium idea is compatible with the project only under the §5.1 principle — *policy is unbought, depth is bought* — and metering in exchanges, with member-fail-open on outage, honest pricing copy, and storage retention rules. The genuinely-open items are implementation questions the checkpoints already carry: keeping the anonymous tier cheap *and* within institutional budget, and nothing else creative at this stage beyond review item W1's reuse-corrective.

*Fix statuses: applied — W1 (exchange unit), W2.2 (policy unbilled principle), W2.3 (fail-open with meter debt), W2.4 (paced-honest + institutional cap), W2.5 (lens budget by tier), W3.1–W3.3 (sticky key, retention, persona pin), W4 (best-effort enforcement + E2 leak measure), W4.1 (validator key), **W5 (widget 429 patch: index.html now renders `needs_membership` as a distinct `.msg.status` instrument-status turn — never the engine-error genre; validated in-browser via a declared fetch-stub, see evaluations/README.md)**. Open — validator-key issuance at deploy, precedent scan for metered spiritual instruments.*


## Revision pass 2 (2026-09-29, after the first revision commits)

Reviewing the reviewed — the widget/contract diffs produced by the previous fixes, plus their interactions. Findings and applied fixes:

**S1 (high) — `person_ref` absent from the shipped requests.** The quota design keys on the client-sent `person_ref`, but the widget never sent it: the metering scheme had no key source on the client and would have silently fallen back to IP/device every time, contradicting the disclosed footer rule. **Applied:** widget now generates a persisted opaque UUID (`localStorage.aguru.person`, `crypto.randomUUID` with fallback) and sends it as `person_ref` on every request.

**S2 (medium) — the hardcoded welcome self-describes engine status.** The client welcome carried a provenance chip reading "Not yet connected to a model endpoint." — a client-side claim that goes *false* the moment the edge is live, i.e., precisely the staleness-dishonesty genre the project's maxim forbids. **Applied:** the status chip now appears only when the endpoint is the known-empty default; with a configured endpoint the welcome makes no engine-status claim (the server greeting, once it exists, carries any such state).

**S3 (medium) — privacy disclosure missing from the widget.** §5/W3.1's rule ("widget footer discloses the device-key counting") existed only in the spec. **Applied:** footer now reads "instrument · counts your use per day by a device key · never initiates · never scores · provenance always shown."

**S4 (medium) — double-submit burns double budget.** Enter-key spam fired concurrent submits; the meter (and the honest-degradation logic) would charge/flip twice for one question. **Applied:** `submitting` in-flight guard with `finally` reset.

**S5 (low) — spec §2's 429 body carried a TODO note addressed to the widget.** Since the widget now implements that rendering, the payload example should be pure contract. **Applied:** TODO prose removed from the JSON; rendering requirement lives in AGENTS.md and the review.

**S6 (note) — mode confusion.** The landing page describes Ask/Study/Counsel/Compare/Practice/Reflect; the widget sends fixed `mode:"dialogue"` with dial as the pedagogy control. Documented in AGENTS.md as a v0.1 simplification (the edge maps dial; richer mode UI arrives with the edge) rather than pretending modes are selectable.

**Verdict:** the revision closed W5 but left four smaller contract-party defects (S1–S4) unreviewed; these are the ones that bite at integration time, not in prose review. All applied. Re-validated via the declared fetch-stub after patch; the 429 path still renders as instrument status (`msg status`), now also with provenance chips.


## Revision pass 3 (2026-09-30 — deployed-behavior review; live curl)

Reviewing the *deployed* edge against its spec, from its actual outputs rather than the code text:

**T1 (high) — the dial was decorative.** `systemPrompt` (persona constraints, dial→acts table) was computed and never sent; the lens call carried only the question. At `listen` the edge still fabricated a full lens lecture with prescriptions — a direct violation of the permitted-acts table the provenance chips *claimed* to be obeying. Lesson recorded: the spec→code defect class this project keeps hitting is *asserting policy in the chips while not enforcing it in the path*. Applied: dial is now behavioral — `listen` returns an a.guru-authored invitation (no lens call; eval_note lens = 'none (listen)'), `question` passes a socratic hint, `teach`/`challenge` stronger hints into the lens prompt; dead block removed. Live-verified on both branches.

**T2 (medium) — gate scope vs attributed voice.** The forbidden-claims string gate runs over the *entire* relay, including lens-quoted material: an authentic historical quote containing "my disciples" would be withheld. The gate that protects a.guru's speech shouldn't mute attributed historical speech. v0.1 stance: accept the false-positive (honest withholding, filter_hit logged); the principled fix — scoping the gate to the a.guru-authored span — applies when responses are authored rather than relayed. Documented, deferred with reason.

**T3 (medium) — persona honesty state in production.** The deployed runtime cannot readTextFile the bundled persona; the pin honest-degrades to persona_sha "unbundled" and continues serving under inline constraints. Correct per W3.3's refusal logic — but "unbundled" must be an exceptional state, not the norm. Action: set AGURU_PERSONA_SHA from the bundle at deploy (or bundle the persona as a module import) so the pin is live rather than aspirational.

**T4 (low) — dial-hint leakage.** The pedagogy hint ships inside the lens user message; a reconstruction may quote it back or react to it. Acceptable for v0.1; a dedicated a.guru synthesis prompt inside ask-faculty remains the correct long-term shape.

Verdict: the edge now does what its chips say on the two exercised dials. Open: T3 pin, T2 gate scoping, checkpoint 4 live quota exercise, widget endpoint switch, site browser validation.
