# Critical Review — bhakti / a.guru v0.1

**Scope.** A candid internal review of WHITEPAPER.md v0.1, DESIGN.md v0.1, ONTOLOGY.md v0.1, persona/a-guru.json, and the public web client, conducted 2026-09-29. Purpose: find the defects *before* reviewers or devotees do. Nothing here retracts the project; everything here demands a change before E-runs begin.

---

## 1. Defects already fixed by this review

- **6.3 contained a placeholder artifact** ("~77%/200 arcs… *(placeholder removed)*") inside a *Results* section — a fabrication-shaped sentence in the one place honesty is non-negotiable for this project. Fixed by replacing the sentence with its true, sourced basis (8.8 citations/transcript; 1,960 normalized references) plus the edition-normalization caveat. **Lesson: Feynman rule — never write a number you cannot immediately source.**

## 2. Doctrinal and framing defects

### 2.1 The thesis is presented as adopted, not argued or tested (§2.1)

"Devotion, like inquiry, is a function of the student" is a *literalist-protestant* reading of guru-disciple transmission. The Gauḍīya traditions the paper leans on would *contest* it: dikshā is a formal transmission (śakti conveyed through paramparā), and the ācārya's grace is ontologically load-bearing in their own literature, not merely functional. The paper's reversal ("the crown makes the king") is internally consistent, but §2.1 states it as a settled anthropological premise. It should be reframed as: **a working hypothesis, theologically contested by the very traditions in the corpus, which the corpus itself can be queried about.** (Bhakti Sandarbha on the necessity of the guru favorable to the devotee's progress would be the natural place to let the corpus answer.)

### 2.2 `G_functional ≟ G_guru` is a category error as stated

No tradition claims guru-hood is behaviorally supervenient; therefore *no* behavioral result can either confirm or deny the ontological remainder. The comparison as written implies a convergent measure that cannot exist. The honest restatement: behavioral convergence is **necessary-justifying-evidence for function replacement claims**, and the remainder — if real — is underdetermined by behavior *by definition*. The paper should say so rather than implying that driving act-sequence distance to zero leaves "a measurable remainder." Measurement of the remainder requires instruments the paper does not yet have (at minimum: devotee-reported practice outcomes E7, which measure the student, not the guru).

### 2.3 E7 can be misread as testing consecration

E7's framing experiment (ascribed vs instrumental) can *never* adjudicate whether an AI "can be a guru." It measures framing effects on student-side outcomes — a social-psychology result. Where it does show a framing effect, one cannot tell consecration from habituation-from-drama. Where it shows none, one cannot tell "devotion is student-side" from "the sample was too weak." The text mostly says this; the name ("consecration reversal") overpromises. Rename to "framing-invariance test" or explicitly bound the inference.

### 2.4 Ethics of E7 is under-designed for its risk profile

Randomizing participants toward *ascribed guru framing of an AI* is exactly the harm vector (false spiritual authority) the project's own §14 forbids inducing. Guard conditions exist (consent, withdrawal, review gate) but no named reviewing body, no stopping rule, no post-session debrief requirement, and no assessment of vulnerable sub-populations (spiritual seekers in distress are precisely the demographic drawn to "guru" framing). The abheda rule must be strengthened: **the ascribed arm requires enhanced consent with debriefing about the framing manipulation** — deceiving people into devotional ascription is not study design, it is the failure mode §29 exists to prevent.

## 3. Methodological defects

### 3.1 E1 has a base-rate control problem (the biggest technical flaw)

Predicting Guru Acts on a corpus where one teacher modal-answers with `CITE_SCRIPTURE + REQUEST_PRACTICE` can score high agreement by always emitting the modal progression. **No null model is specified.** Required baselines, pre-registered before any model runs: (a) corpus-frequency act prior; (b) genre-conditioned prior; (c) an n-gram/statistical baseline. Model deltas above these baselines — not raw agreement — are the meaningful result. Without (a)-(c), E1 numbers are theater.

### 3.2 GuruAct taxonomy overlap and provenance regression

- The 17-act set has overlapping members (`EXPLAIN` vs `INTERPRET` vs `CITE_SCRIPTURE` can span identical text; `WARN` vs `CHALLENGE` intuition differs only by valence. No inter-rater reliability study exists, and the annotation will be done by AI — hence **GuruAct labels are themselves layer-5 inferences**, which collapses part of the provenance architecture at the annotation stage. The five layers are only as clean as the *extraction* pipeline; §3's architecture needs a sixth audit layer for annotation fidelity.
- **Closed-set constraint is premature.** Praying, silent attention, gift-giving, silence length — non-verbal or interpersonal acts visible in transcripts (e.g., who Prabhupāda *sits silently with*) are excluded by design.

### 3.3 Person model and relationship history are mostly aspirational

`H` (relationship history) in `G(S,P,H,Q,C)` is largely unavailable: transcripts anonymize interlocutors as "Devotee (2)", "Guest (1)" — 933 speakers are mostly non-persistent labels; only letters bind stable individuals (6,634 letter recipients, of which **40 letters ingested to date — 0.6%**). Counsel-mode "analogical retrieval on person-state" therefore currently draws on lecture-dominant data that is audience-facing, not person-specific. Either say the Corpus is biased toward the public register and letters ingestion is the priority, or drop the H-dependence from E4's claims.

### 3.4 Corpus skew and denominators must be stated wherever numbers appear

- 1,616 lectures vs 407 conversations vs 186 walks vs 40 letters ingested: the aggregate dialogue ratio (0.864, §4.3) conceals that the *behaviorally interesting* genres are an order of magnitude less covered than exposition.
- The manifest lists 28,725 records; the stats file 2,382 — the paper never says "verified fraction: ~8.3%" (if denominator is right). Percent-complete, not just counts, belongs in §4.3's table.
- Sample means (8.8 citations per transcript; 94/96 multi-speaker conversations) are small-n and early-run biased (early ingest chronological from VedaBase order); years 1972–1974 will dominate. Early-sample means will shift as the corpus completes; values should carry "(n=…" and date-stamps everywhere, or change.

### 3.5 Provenance UX: five layers, six flows

AI inference mediates each layer (retrieval choice, passage selection, act segmentation, translation display). The user-facing path "Śāstra → Siddhānta → Ācārya → Guru-behavior → a.guru" per-response is a simplification: the machine also chooses *which* Śāstra appears. Layer-5 leakage to layer selection is invisible in the current UI. The provenance chip should distinguish **selection provenance** (why these passages surfaced) from **assertion provenance** (whose claim this is); currently only the latter has a representation.

### 3.6 "No fine-tuning required" is asserted, not argued

For E1/E7 claims of behavioral fit, a retrieval-only baseline is asserted sufficient, but GuruAct-sequence decision quality is a *policy* problem, not a retrieval problem; hybrid retrieval alone may regress to modal-answer style. The MVP can test the central hypothesis — the paper is right — but §6.3's first bullet overstates what segmentation supervision alone yields. Mark as untested assumption (E5-ablation covers it).

## 4. Presentation defects

- §6 mixes "results" with "feasibility observations" — the latter are inferences; label them "design analyses," not Results, or a hostile reader writes the headline for you.
- The paper has **no related-work section** (digital religion studies, parasocial-AI literature, chatbot pastoral-care controversies, prior GuruBot experiments). At minimum one paragraph must situate against existing AI-preacher / devotional-companion deployments, or the novelty claims are unsourced.
- The evaluation appendix has no power-analysis, no pre-registration venue (OSF or similar), and no named criteria for "tradition-literate judges." "Scholars" as the reliability anchor is unfalsifiable until the panel composition is fixed.
- Sanskrit terms are used loosely in places (§4.5's `abhidheya` gloss in the illustration is right, but the style would fail a Sanskritist's copy-edit; the paper's ritual precision should equal its statistical precision).

## 5. Risks to the whole program (non-technical)

1. **A public instrument will attract the vulnerable.** The landing page is currently "research-first" — good — but the moment sessions route to a real model, dependency, thought-substitution ("I let a.guru decide"), and covert confession data arrive. The privacy/logging promise in AGENTS.md must state *who* reads consented person-models (no one by default; aggregate only under E-studies with separate consent).
2. **The instrument currently has no engine.** The site's chat returns "not connected." Fine for now; but the longer an honest empty state ships, the higher the temptation to attach *any* LLM politely — which would quietly violate maxim #1 the first time the model synthesized tradition. Engine integration must ship with E2's audit in the same release.
3. **The institute's own corpus is right-biased.** Until letters ingest and the Sandarbha layer exist, the guru-function being reconstructed is a *lecture* function. The project should resist interpolating from exposition to counsel.

## 6. Required actions before E-execution

| # | Action | Status (2026-09-29) |
|---|---|---|
| 1 | Pre-register E1–E7 externally (OSF) with panel composition, null models (E1), power analysis, stopping rules | scheduled — §8 validity-infrastructure para; external venue pending |
| 2 | Letters + conversations ingestion priority; re-balance sample; report % complete everywhere | documented — §4.3 coverage/skew para + §8 priority order; ingestion itself runs in bibliotech |
| 3 | Annotation-fidelity pilot (50 interactions, 3 independent raters, κ ≥ 0.6) before act-model work | ratified as E0 gate in §5.4; runs when MVP data pipeline exists |
| 4 | Add selection-provenance to the UI contract (AGENTS.md payload schema) | done — AGENTS.md updated; UI wiring on backend arrival |
| 5 | E7 rename + debriefing requirement + named review body | rename/bound done in §5.4; external review body name still owed |
| 6 | §2.1 reframe: thesis as contested hypothesis; corpus answers back | done — §2.1 + §2 limitation para |
| 7 | Related-work section (≥ 10 sources) | §9 sketch added with 5 clusters; counts as start, not completion |
| 8 | Update persona/a-guru.json affordances to separate measurable efficacy from authority | done — rationale + inquiry_strengths updated |

## 7. Verdict

**Direction: sound. Instrumentation: ahead of its validity.** The provenance-first architecture, the no-score/no-initiation constraints, and the student-side devotion thesis constitute a defensible research program; none of it has yet been *tested*, and several designs (E1 null models, E7 inference bound, letters sampling) are, as written, not capable of producing the conclusions their prose promises. The corpus work is real and citable. The ontological-remainder question is apt — and must be stated as unmeasurable *by behavior alone*, or the project trades the guru-impersonation sin for a subtler one: impersonating *an experiment*.

*Revision of 2026-09-29 (same day): all eight actions applied against the documents — E0 gate and E1 nulls ratified (§5.4), E7 renamed and bounded, §2 limitation paragraph, §4.3 coverage/skew reporting, §6.3 relabeled *design analyses*, §9 related-work sketch, selection-provenance added to the AGENTS.md contract, persona wording updated. Open still: external pre-registration venue, named review body for E7, full letters ingest, and a ≥10-source related-work review.*
