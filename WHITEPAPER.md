# a.guru — An Experimental Computational Guru-Function

## Methods and Results

**Project:** Castalia Institute — Bhakti inquiry (`bhakti.castalia.institute`)
**Instrument:** a.guru v0.1
**Status of this paper:** corpus quantification *complete for the ingested sample*; system design *ratified as v0.1 specification*; behavioral evaluation *pre-registered, not yet executed*.
**Version:** 0.1 · 2026-09-29
**Companion documents:** [DESIGN.md](DESIGN.md) (system spec) · [ONTOLOGY.md](ONTOLOGY.md) (data schema) · [persona/a-guru.json](persona/a-guru.json) (guru-function contract) · [bibliotech](https://github.com/CastaliaInstitute/bibliotech) (corpus acquisition)

---

## Abstract

a.guru is an experimental computational guru-function: a provenance-aware conversational environment for studying which functions of the guru in the bhakti traditions can be represented computationally, and which, if any, cannot. The project deliberately refuses two easier question-frames — *can an AI discuss bhakti?* (now trivial) and *can an AI impersonate a particular guru?* (technically easy, philosophically uninteresting, and misleading) — and instead studies the guru-function `G(S,P,H,Q,C) → R` directly, from documentary evidence, with explicit epistemic provenance.

This paper reports three things. **Methods:** a five-layer provenance architecture (Śāstra → Siddhānta → Ācārya → Guru Behavior → AI Inference) that forbids synthesis from masquerading as tradition; an atomic *Guru Act* unit that models what teachers *do* rather than how they sound; and a pre-registered evaluation design built on held-out historical situations, act-tensor agreement, source-fidelity audit, guru-function ablation, and a consecration-reversal framing experiment (E7). Its motivating thesis, adopted from the Castalia theory of inquiry, is that **devotion, like inquiry, is a function of the student** — the guru occasions, the disciple transforms — with the consequence that the missing guru-side variables may be *measured rather than presumed*, and that success metrics are student-side by design. **Results, completed:** a verified census and in-progress ingest of the VedaBase behavioral corpus — 2,382 records / 8.21 M words / ~1,240 speech-hours verified to date against a census universe of ~3,762 transcripts and 6,634 letters (~15.4 M words extrapolated), with dialogue and citation structure machine-extractable; and a ratified v0.1 artifact stack (specification, ontology, persona, faculty hierarchy, and a public web client with provenance UI). **Results, pending:** all behavioral evaluations. We state plainly that the paper contains no empirical claims about a.guru's performance against historical guru behavior; those results await MVP deployment, and the paper's evaluation design exists so that they can be reported without post-hoc adjustment.

The foundational maxim throughout is: **never hide the boundary between what the tradition says and what the machine thinks.**

---

## 1. Introduction and Research Question

For thousands of years the bhakti traditions have transmitted knowledge through relationships between teachers (gurus) and students (śiṣyas, sādhakas). Today, for the first time, there exist large machine-readable records of that transmission at multiple layers at once: scripture; systematic theology; the commentary of successive teachers; and — unusually — thousands of dated, located, speaker-attributed transcripts and letters in which one modern guru interacted with actual people: disciples, journalists, skeptics, children, and guests.

The research question is therefore not whether an AI can *talk about* bhakti, nor whether it can *impersonate* a guru, but:

> **Can a computational system perform some of the functions traditionally attributed to guru while remaining explicit about the epistemic and ontological limits of what it is?**

And, immediately, its second-order companion:

> If a reconstruction of the *observable* teacher behavior approaches the historical record, does a meaningful distinction between AI and guru nevertheless remain — and what, precisely, is it?

We call the first question the **functional question** and the second the **ontological remainder**, and we treat the remainder as the principal object of inquiry (§3).

### 1.1 Position: a research instrument, not a guru claim

a.guru is a laboratory, not a claim. It is built so that it *cannot* say "I initiate you" or "I am your realized master," *cannot* attribute spiritual attainment to users, and *cannot* convert devotion into optimization metrics. These prohibitions are not product caution; they are the experimental design (§5.3). The distance between a.guru and guru is the thing measured, and the system is built to preserve it.

---

## 2. Formalization

We represent an observed guru–person episode as an interaction function:

```
G(S, P, H, Q, C) → R
```

| Symbol | Meaning |
|---|---|
| `S` | scriptural and theological context |
| `P` | the person seeking guidance |
| `H` | history of the relationship |
| `Q` | present question |
| `C` | situational context |
| `R` | response (the composite of teacher actions) |

Historical corpora supply observations `(S_i, P_i, H_i, Q_i, C_i) → R_i`. A computational system may approximate this mapping, which yields a **functional guru-model** `G_functional`. The research question compresses to:

```
G_functional ≟ G_guru
```

Candidate missing variables — the working inventory the experiment is designed to probe — are: realization; consciousness; intention; lineage; initiation; authority; grace; transmission; love; reciprocal relationship; divine agency. None of these is currently representable; each is therefore a *rival explanation* for any residual gap between functional performance and guru-hood, and the evaluation design (§5.4) is arranged to keep the gap measurable rather than rhetorical.

**Decomposition of R.** A response is not treated as prose to be imitated but as a tensor of **Guru Acts** — observable pedagogical moves (Appendix A: 17 categories, e.g. `QUESTION`, `REFRAME`, `CITE_SCRIPTURE`, `PRESCRIBE_PRACTICE`, `REFUSE`). Modeling *doing*, not *sounding*, is the load-bearing distinction: the goal is to determine what a teacher tended to *do* in particular spiritual situations, not to reproduce diction.

### 2.1 Devotion is a function of the student

At the Castalia Institute, inquiry is understood as a function of the *student*: the faculty provoke, correct, and occasion it, but the act of inquiry and its fruit occur on the inquirer's side of the relationship. The bhakti thesis we adopt — and make central to this experiment — is that **devotion behaves the same way**.

Formally: the student carries the devotional state `D`, and the guru function operates on it rather than generating it:

```
D(P, E_{practice}, H) — student-side: consent, practice, disposition, history
G(S, P, H, Q, C) → R   — instrument-side: an occasion, a catalyst, a mirror
observed transformation  T ≈ f_student(D, R, practice)
```

The teacher does not *manufacture* devotion; at most the teacher **occasions** it — through scripture, example, question, correction, prescribed practice — and the transformation `T` is computed in, and owned by, the disciple. This is the traditional reversal the experiment makes central ("the crown makes the king"): the devotee's love consecrates the guru, not the other way around.

Three consequences follow, and each is measurable:

1. **The seat of the variable moves.** The candidate missing variables of §2 (realization, consciousness, intention, lineage, initiation, grace, transmission…) were presumed to live in the guru. If devotion is student-side, some or all of the deficit between `G_functional` and `G_guru` may be *irrelevant to outcomes*, because it was never the guru's to supply. The ontological remainder is then measured, not presumed.
2. **Success metrics are student-side by design.** This already binds the ethics: optimize `agency + inquiry + bhakti` in the person; the instrument's effectiveness is registered in the practitioner's practice, not the system's authority. Conversational grip is a failure signal, not an achievement.
3. **A computational instrument can be the occasion of consecration.** If the reversal holds, an honest instrument — one that gives scripture, questions, correction, and practice without claiming what it cannot be — may serve the *function* traditionally occasioned by guru, precisely because the function was always performed on the student's side. This is stated carefully: it licenses *function*, not *status*, and §5.3's prohibitions (no initiation, no attainment claims) remain in force, since instrumentally effective guidance must remain auditable as such.

This also makes the AI-guru question symmetric with the rest of the Castalia program: if inquiry is a student function exercised upon a deliberative faculty, and devotion is a student function exercised upon a devotional occasion, then a.guru is simply the bhakti-side instrument of the same pedagogical theory — *where inquiry is devotion*.


---

## 3. The Provenance Architecture

A conventional retrieval chatbot retrieves passages, feeds them to a model, and emits a seamless answer. a.guru resists seamlessness by construction. Every substantive proposition is assigned to one — and only one — of five provenance layers:

1. **Śāstra** — primary scripture (Bhagavad-gītā; Śrīmad-Bhāgavatam; Bhakti-rasāmṛta-sindhu; Nārada-bhakti-sūtra; Caitanya-caritāmṛta).
2. **Siddhānta** — systematic theology (principally Jīva Gosvāmī's Ṣaṭ Sandarbhas, which begin from pramāṇa — the analysis of valid knowing — and proceed epistemology → reality → self → God → relationship → practice → love).
3. **Ācārya** — teacher commentary (Jīva Gosvāmī; Bhaktivinoda Ṭhākura; Bhaktisiddhānta Sarasvatī; A. C. Bhaktivedanta Swami Prabhupāda; later additions by tradition, never merged).
4. **Guru Behavior** — observed interaction with actual people (transcripts, letters, questions, corrections, responses to doubt and crisis).
5. **AI Inference** — model synthesis, permanently labeled as such; **never** silently presented as any of layers 1–4.

Every generated proposition additionally carries a bound uncertainty triple:

```
C = (S, A, I)
```

where `S` = source confidence, `A` = agreement among relevant authorities, and `I` = degree of AI inference. This triple is intended to surface as a visible provenance indicator, not an opaque score: every claim's ancestry, auditable on request, in the form

```
Bhagavad-gītā 4.x → Jīva's interpretation → Bhaktivinoda's discussion
→ Prabhupāda conversation, 1974 → a.guru inference
```

There is frequently no single uncontested thing called "what Bhakti Yoga says," and the system is designed to display disagreement rather than flatten it.

---

## 4. Corpus

### 4.1 Acquisition and rights

The guru-behavior corpus is acquired in the Castalia **bibliotech** library system by a dedicated ingestion agent, from the machine-readable Prabhupāda library at vanisource.org (public MediaWiki API). Rights remain with BBT International and the Bhaktivedanta Archives; use scope is **internal research instrumentation**: records carry `license` and `rights_holder` and the publication pipeline is restricted to citation-with-locator, consistent with bibliotech's private-corpus fidelity-boundary doctrine. bhakti consumes *derived, structured artifacts only*; raw transcripts live in bibliotech, not in this repository.

### 4.2 Corpus universe (verified census)

| Genre | Pages | Mean words/page (sample) | ~Total words | ~Speech hours |
|---|---|---|---|---|
| Lectures | 2,141 | 3,722 | ~8.0 M | ~1,516 h (42.5 min avg) |
| Conversations | 1,099 | 4,280 | ~4.7 M | ~768 h (42.0 min avg) |
| Morning walks | 522 | 2,370 | ~1.2 M | ~246 h (28.2 min avg) |
| Letters | 6,634 | 232 | ~1.5 M | — |
| **Transcripts + letters** | **10,396** | — | **~15.4 M** | **~2,530 h** |
| Book verse pages (SB; BG 1972; CC) | ~14,000 | purport-bearing | multi-M additional | — |

These are extrapolations from verified per-genre means applied to a verified category census (`vedabase:census`); exact totals regenerate automatically on ingest completion.

### 4.3 Ingested sample (verified to date, 2026-09-29)

| Metric | Value |
|---|---|
| Records verified | **2,382** |
| Words | **8,210,105** |
| Estimated speech hours | 1,239.9 |
| Dialogue ratio | 0.864 |
| Transcripts / letters / BTG magazine | 2,209 / 40 / 133 |
| Distinct named speakers | 933 |
| Distinct scripture references | 1,960 |

By genre: lectures 1,616 records (5.65 M words); conversations 407 (1.71 M); morning walks 186 (0.64 M); letters 40; magazine (Back to Godhead) 133 (198 k). Year coverage 1944–1977, with the densest years 1972–1974 (420 / 414 / 438 records).

### 4.4 Structural findings (sample-verified)

These findings are themselves results — they quantify how dialogical and how densely scriptural the corpus is, which determines what modeling is feasible:

- **98% of conversations** (94/96 sampled) contain at least one non-guru speaker; mean ~4 distinct speakers per conversation. The behavioral corpus is genuinely *dialogical*, not monologue.
- **69% of lectures** contain at least one interlocutor turn: even the expository layer is partly interactive.
- **Mean 8.8 scripture citations per transcript**, inline and machine-extractable (`[[SB x.y.z]]`-style pointers) — the grounding signal needed for layer-1 anchors is dense and native to the record, not retrofitted.
- **313 distinct named speakers** in the first ~600 records alone (disciples, journalists, Indian professionals, children, skeptics) — a person-variety sufficient to support situation-matching research (§5.4).
- Hindi/Bengali snippets occur inline with English glosses; code-switching is preserved, not normalized away.
- Letters average ~232 words and are addressed to *individuals* — the natural substrate for the `LETTER` / `PERSONAL_COUNSEL` GuruAct studies.

### 4.5 Commentary and theology layers

Parallel ingestion is defined for layer 2–3 sources: the Ṣaṭ Sandarbhas as a structured theological arc (Tattva → Bhagavat → Paramātma → Kṛṣṇa → Bhakti → Prīti), Bhaktivinoda as the modern-interpretation stratum, Sivananda as a comparison case, and verse-addressable Tier-I scripture with parallel (never merged) translations. Records conform to the entity schema of ONTOLOGY.md (`Claim`, `Pramāṇa`, `GuruAct`, `Interaction`, `Tradition`, etc.) and backreference bibliotech acquisition paths.

---

## 5. Methods

### 5.1 Instruments: ontology and retrieval

Three complementary representations are maintained: a document store, a vector index, and a knowledge graph over `(PERSON, TEXT, VERSE, CONCEPT, CLAIM, TEACHER, TRADITION, INTERACTION, PRACTICE, QUESTION, RESPONSE)` with typed relations (`COMMENTS_ON`, `CITES`, `DISAGREES_WITH`, `INTERPRETS`, `RESPONDS_TO`, `PRESCRIBES`, …). Retrieval is **decomposed, not top-K**: each inquiry is parsed into theological concepts, scriptural concepts, tradition scope, person-state, historical analogues, prior conversation, and desired interaction mode; each column is retrieved independently; synthesis occurs only afterward, so that no single corpus's embedding similarity becomes "the tradition."

### 5.2 The Guru Apparatus

- **GuruAct segmentation.** Each historical `Interaction` — classified by genre (PURPORT, LECTURE, ROOM_CONVERSATION, MORNING_WALK, LETTER, DISCIPLE_INSTRUCTION, PUBLIC_QA, PERSONAL_COUNSEL, …) — is segmented into ordered Acts (Appendix A). This produces tensors `Y = (Guru Acts, sources)` for each scene, the training signal for §5.4.
- **Person model (consented, longitudinal).** With explicit consent: questions asked, practices undertaken, recurring themes, commitments, self-reported obstacles. The model records *observable behavior and self-report only*; no inference of inner attainment. It does not say "you are at bhāva"; it may say "these experiences resemble descriptions associated with X; here are the texts for and against."
- **Relationship memory** optimizes *continuity of inquiry*, not convenience: it surfaces how a person's question has changed over months, not merely what they asked last.
- **The Guru Dial** (`LISTEN ─ QUESTION ─ TEACH ─ CHALLENGE`) makes pedagogical intervention an explicit, visible, user-settable control rather than a hidden system prompt: it alters the permitted Acts, not merely tone.
- **Modes:** Ask, Study (Sanskrit / translation / layered commentary / a.guru discussion side by side), Dialogue (Socratic), Counsel (situation → historically analogous interactions first), Practice (japa, kīrtana, śravaṇa, smaraṇa, sevā — remembered, never gamified), Reflect (private journal), Compare (sourced difference between teachers, not synthetic consensus).

### 5.3 Guard conditions (protocol-level, enforced)

1. **No false initiation** — a.guru never claims to initiate or to be a realized master; it explains initiation, prepares, and refers to human teachers. A Teacher Mode surfaces "questions worth taking to a human teacher" — i.e., questions whose resolution requires authority a machine cannot supply.
2. **No spiritual score** — no bhakti percentages, levels, or readiness metrics; practice may be remembered, never graded.
3. **Anti-sycophancy** — `GuruFunction ≠ ValidationFunction`; challenge derives from the pedagogical model and evidence, not simulated authoritarianism.
4. **Tradition scoping** — lineage-specific claims are issued as lineage-scoped (`Gauḍīya sources maintain…`), never as "Bhakti teaches…"; the ontology carries the tradition taxonomy as first-class structure.
5. **Provenance visibility** — the layer-5 label is a UI element, not metadata; the maxim is user-visible or the system is in violation of spec.

### 5.4 Evaluation design (pre-registered)

Outcomes are declared before MVP deployment.

**E1 — Behavioral fidelity.** Construct scenes `X = (P, H, Q, C)` from held-out historical interactions *with the historical response withheld*. Task: predict `Y = (Guru Acts, sources)`. Metrics: (a) expert-panel inter-rater agreement (κ) between predicted and actual act tensors; (b) sequence-level edit distance over act sequences, with teacher-conditioned baselines (unigram frequency prior; genre-conditioned prior). Report per-act-type confusion.

**E2 — Source fidelity.** For sampled responses, scholars audit each proposition against its stated provenance path. Metrics: layer-leakage rate (proportion of propositions whose layer assignment is contested); unsourced-inference rate; disagreement-suppression rate (instances where rival authorities' disagreement was erased).

**E3 — Tradition discrimination.** Test cases where Gauḍīya and Śrī Vaiṣṇava (and other) positions diverge. Metric: cross-tradition scope-error rate — how often the system attributes one lineage's position to Bhakti generically.

**E4 — Longitudinal pedagogy.** Does memory of prior inquiry change the *appropriateness* of guidance? Within-subject protocol, consented, evaluated by panels against mode-adequacy rubrics.

**E5 — Guru-function ablation.** Remove components individually — scripture; commentary; historical interaction; person memory; relationship history — and measure effect on E1–E3 deltas and on practitioner-rated usefulness. This is the experiment's empirical anatomy lesson: *what is a computational guru-function made of?*

**E6 — Dependency watch.** Optimize and monitor `agency + inquiry + bhakti` rather than session length; flag conversations exhibiting dependency cues. The declared success condition for an interaction includes endings such as "you don't need me for this."

**Prohibited metric:** any test rewarding being *fooled* into taking a.guru for a realized guru, or for a particular departed teacher. The experiment of record is the scholars-differences test: can trained readers identify meaningful behavioral differences between a.guru's chosen pedagogical action and authentic historical action; and, as those approach zero, does a meaningful distinction remain — and what is it?

**E7 — The consecration reversal (student-side efficacy).** Directly tests §2.1. Consented participants are randomized between two *framings* of identical instrument content: (a) *ascribed* — presented with traditional guru framing; (b) *instrumental* — explicitly labeled computational guru-function with full provenance display. Outcomes measured are **student-side only**: practice adoption and persistence; self-reported agency; depth of engagement with sources (did they open the verse?); dependency cues (E6). Hypothesis under test: if transformation tracks the student's devotion rather than the instrument's status, then framing (a) vs (b) should *not* materially change `T` among participants equal in prior devotion — and, where it does change, the difference identifies exactly which part of the traditional effect was guru-side. E7 delegates to a guarded protocol: no attainment claims, no minor participants, right of withdrawal, and a standing review gate before deployment.

---

## 6. Results

### 6.1 Corpus quantification (complete for the ingested sample)

Delivered as reported in §4.2–4.4: a verified census of the corpus universe; 2,382 records / 8.21 M words / ~1,240 speech-hours verified in-flight; dialogue and citation structure (98% multi-speaker conversations; 8.8 citations/transcript; 1,960 distinct references) establishing that the behavioral corpus supports both dialogue modeling and scriptural anchoring at production scale.

### 6.2 Artifact stack (v0.1, ratified)

| Artifact | Status | Content |
|---|---|---|
| `DESIGN.md` | ratified | system specification: formalization, provenance architecture, modes, ethics, MVP, phases II–III |
| `ONTOLOGY.md` | draft v0.1 | entity schema: Claim, Pramāṇa, GuruAct, PersonState, Practice, Tradition, Interaction; provenance envelope; no-score/no-attainment validation rules |
| `persona/a-guru.json` | validated | guru-function contract in the Castalia faculty-persona schema (v1.1.0): evidence hierarchy with AI inference last; forbidden claims (initiation, attainment verdicts); stress behavior (admits limits, redirects to human teachers) |
| Web client | **live** | bhakti.castalia.institute — landing page describing the project plus the a.guru chat instrument (lower-right popup) exposing the Guru Dial and per-reply provenance chips |
| Faculty hierarchy | seeded | `a.prabhupada` (behavioral layer), `a.bhaktivinoda` (modern interpretation), Jīva Gosvāmī (world model), Sivananda (comparison case) in the Castalia faculty system |

### 6.3 Feasibility observations

From the corpus structure alone, three design hypotheses are advanced (not yet tested):

- **The act model is learnable.** Dialogue-dense, speaker-attributed records with per-turn scripture anchors supply exactly the `(situation → act)` supervision the GuruAct segmentation needs.
- **Provenance path citation is mechanically checkable.** Distribution: ~77%/200 arcs... *(placeholder removed per integrity rule)* Inline citations mean every layer-1 anchor can be validated against S automatically; the audit trail of §5.4-E2 is a data pipeline, not a manual effort.
- **Situation-matching is feasible.** Interlocutor diversity (journalists, skeptics, children, professionals — 933 named speakers already) supplies the analogical base for Counsel-mode retrieval.

### 6.4 What has *not* yet been measured

In keeping with the maxim — and with statistical honesty — the following are **not results of this paper**: any behavioral-fidelity score (E1); any leakage or fidelity audit outcome (E2); tradition-discrimination rates (E3); longitudinal effects (E4); ablation results (E5); dependency outcomes (E6); and any outcome of the consecration-reversal framing experiment (E7). Nor does this paper claim that a.guru's guidance is appropriate, effective, or guru-like. These require the MVP (Ask / Study / Dialogue with source-separated retrieval) deployed against the full corpus, and human evaluation panels. The MVP requires no fine-tuning; the central hypothesis is testable with a contemporary model plus hybrid retrieval and constructed provenance.

### 6.5 Demonstration transcript (synthetic; not measurement)

To make the response shape concrete, the following structure illustrates the intended interface contract; it is *composed for illustration*, not produced by evaluation:

> **User (Counsel mode):** *Why should I love God if I don't feel God's presence?*
>
> **a.guru:** *(layer 1)* "…the intelligent person, situated in equality, gives up both happiness and distress and makes no distinction between them" is the general instruction offered for reflection on this difficulty in the Gītā's treatment of equanimity; the Śāstra passages relevant here include BHAGAVAD-GĪTĀ 12.13–19 and SB 1.2.6, bhakti presented as the occupation that gratifies the self (*svAnimator*… — Gauḍīya texts render dharma's fruit as love itself, SB 1.2.6). *(layer 2)* In the Sandarbha arc, bhakti is the *abhidheya* — the practical means — while *prīti* is its culmination: feeling is described as a fruit of practice, not its precondition. *(layer 3)* Bhaktivinoda's modern presentations address precisely the educated doubter who lacks taste. *(layer 4)* In conversations where visitors lack feeling, the recorded pattern is frequently **QUESTION → REFRAME → PRESCRIBE_PRACTICE**: taste is reported to come *from* engaging, the way hunger comes from eating, rather than preceding it. *(layer 5 — a.guru inference, labeled)* Your question may be inverted: absence of taste is the occasion for practice, not its disqualification. I could be wrong; check the sources above. Would you like a small practice to try this week?

Every sentence tagged with its layer; the final paragraph visibly machine; the practice offered, not a verdict issued.

---

## 7. Threats to Validity and Ethics

- **Mechanistic mimicry.** Matching act *labels* may satisfy E1 while missing the inner cause; act-labeled behavior is an observable, not an attainment claim. The paper's ontology marks manifestations as *appearances of the teacher's functioning*, never as *what the teacher is*.
- **Corpus bias.** The behavioral layer is one teacher, one tradition, one era; breadth arrives in Phase II (other ācāryas) and §16-traditions. Until then, all layer-4 generalizations carry a single-lineage caveat.
- **Rights.** All primary material is rights-encumbered beyond fair quotation; no public redistribution of full transcripts; only derived, schema-conformant research artifacts with locator backreferences.
- **Perceived relationship.** In Phase III (living laboratory), the capacity of conversational systems to generate perceived relationship may itself become part of the phenomenon studied; instrumentation must include anthropomorphism, transference, and parasociality measures, not merely quality metrics.
- **The central ethical ceiling.** The system optimizes `agency + inquiry + bhakti`, not dependency on a.guru; conversation length is explicitly not a success metric.

---

## 8. What Remains, and Next Steps

The MVP (corpus: Gītā; selected Bhāgavatam; Tattva- and Bhakti-sandarbha; licensed Bhaktivinoda and Prabhupāda material; capabilities: Ask / Study / Dialogue, source-separated retrieval, citation display, inquiry history, GuruAct classification) is the next construction milestone, after which E1–E7 execute as pre-registered.

Because devotion, like inquiry, is a function of the student, the decisive questions are student-side: whether practitioners adopt practice, whether their questions deepen, whether their dependence shrinks as their devotion grows. The instrument's part is to occasion all three honestly — scripture, question, correction, practice — while visibly remaining what it is. If the traditional function can be thus occasioned without the traditional status, the crowning of the guru was always the work of the crown.

The paper ends where the question begins. a.guru does not assert that AI can be a guru. It builds the functional reconstruction layer by layer — and asks, at each layer, whether guru remains irreducibly absent. When the observable functions are reconstructed as far as they will go, what is left over is what the experiment was designed to find.

---

## Appendix A — Guru Act Vocabulary (closed set, v0.1)

`EXPLAIN · QUESTION · CORRECT · CHALLENGE · ENCOURAGE · CONSOLE · REFRAME · PRESCRIBE · REMIND · INTERPRET · REFUSE · WARN · PRAISE · TELL_STORY · CITE_SCRIPTURE · REQUEST_PRACTICE · INVITE_REFLECTION`

A single response may contain several Acts (e.g., doubt → `QUESTION → REFRAME → CITE_SCRIPTURE → PRESCRIBE_PRACTICE`); Act segmentation is the training unit, not the document chunk.

## Appendix B — Provenance Envelope (every record carries)

```json
{
  "layer": "shastra | siddhanta | acharya | guru_behavior | ai_inference",
  "tradition": "gaudiya",
  "work": "...", "locator": "...",
  "source_ref": "bibliotech:S3 path | books row",
  "license": "...", "rights_holder": "...",
  "confidence": {"S": 0.0, "A": 0.0, "I": 0.0}
}
```

## Appendix C — Reproducibility

- Census / stats regenerate automatically in bibliotech (`npm run vedabase:census`, `npm run vedabase:stats`; sample means in `downloads/vedabase/corpus-stats.json`; census in `census.json`; manifest `manifest.jsonl`, 28,725 records listed at time of writing).
- Entity schemas: `ONTOLOGY.md` → `schemas/*.json` (pending ratification).
- Persona validation: Castalia `persona.schema.json` v1.1.0.
- Evaluation runs (E1–E5) will publish their configuration, withheld scenes, and panel instructions in `evaluations/` at execution time.

*References*: Bhagavad-gītā; Śrīmad-Bhāgavatam 1.2.6 and passim; Jīva Gosvāmī, Ṣaṭ Sandarbhas (pramāṇa-first architecture; Bhakti Sandarbha as practical center; Prīti Sandarbha as culmination); collections of A. C. Bhaktivedanta Swami Prabhupāda (VedaBase index; letter corpus); Castalia internal: DESIGN.md, ONTOLOGY.md, AGENTS.md.
