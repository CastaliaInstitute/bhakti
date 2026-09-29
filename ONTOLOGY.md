# a.guru Ontology — Draft v0.1

Core data schema design for the a.guru research corpus. This determines how we ingest the Prabhupāda/Jīva corpora **without merely making another vector-search chatbot**.

Ratified entities graduate into `schemas/*.json` (JSON Schema draft-07, Castalia conventions). The five provenance layers of DESIGN.md §3 are the spine: every entity below is tagged with exactly one `layer`: `shastra | siddhanta | acharya | guru_behavior | ai_inference`.

---

## 1. Entity Overview

```
Tradition ──< Text ──< Verse
                   │        │
                   ▼        ▼
                Commentary  Claim ◄── Pramāṇa
                   │        │            │
                   └────────┴──► Evidence Graph
                            ▲            │
         Interaction ──< GuruAct         │
              │                          │
              │ predicts/binds           │
              ▼                          ▼
         PersonState ────────────── Practice
              │                          │
              └──────── Inquiry ─────────┘
                     (Relationship Memory)
```

Emergent entities (Interaction, PersonState, Inquiry, PracticeLog) are recorded observations of the guru function `G(S,P,H,Q,C) → R`; authored entities (Text, Verse, Claim, Pramāṇa) are the S-side context. Verified derivations from the ponderable, limited-person, scriptural, and contextual estates are graded claims; deviation becomes inquiry, and unresolved inquiry is escalated per DESIGN.md §15.

### Field style

- `id`: ULID; `@type` determines interpretation. `descendant` property points at `sameAs` twin if any.
- `refs: [{ "@id": ..., "prov": { layer, work, locator, span } }]` — every entity earns references by exact span in a source at a locator.
- `norm`: ODIFL-style confidence/fuzzy suppression: `confidence ∈ [0,1]`, `version`, `superseded_by: ULID|∅`; suppressed/superseded facts drop out of retrieval rather than delete rows.
- `digest`: `sha3-256` of canonical form for citation UX lineage checks.

---

## 2. Provenance Envelope (every record carries this)

```json
{
  "layer": "shastra | siddhanta | acharya | guru_behavior | ai_inference",
  "tradition": "gaudiya",
  "work": "bg | sb | brs | nbs | cc | ss.tattva | ss.bhakti | ...",
  "locator": "4.7 | SB 1.2.6 purport | letter 1968-08-12",
  "source_ref": "s3://castalia-institute-corpora/...|bibliotech:books:<id>",
  "license": "CC-BY | fair-use-excerpt | BBT-permission-pending",
  "rights_holder": "BBT International | Bhaktivedanta Archives | public domain",
  "confidence": 0.0
}
```

`ai_inference` is a layer, not a lackey: any synthesized proposition is tagged and never merges silently into the other four.

---

## 3. `Text` / `Verse` (Tier I)

Verse-addressable canonical unit. One row per (work, chapter, verse, translation).

```json
{
  "@type": "Verse",
  "address": {"work": "bg", "chapter": 4, "verse": 7},
  "sanskrit": "yadā yadā hi dharmasya…",
  "transliteration": "IAST",
  "translations": [
    {"translator": "A.C. Bhaktivedanta Swami Prabhupāda", "edition": "BBT 1983", "text": "…"}
  ],
  "language": ["sa", "en"],
  "license": "...",
  "digest": "sha3-256:..."
}
```

**Invariant (DESIGN.md §5):** translations are never merged into a synthetic composite. Multiple `translation` entries are parallel, never concatenated.

---

## 4. `Claim`

The atomic evaluable unit of theology. Some teachers' claims are well-formed predicates over the corpus — grounded, scoped, and checkable rather than authoritative by fiat.

```json
{
  "@type": "Claim",
  "statement": "Bhakti does not depend on bodily qualifications such as birth.",
  "scope": {"tradition": "gaudiya", "versus": ["nyaya", "advaita"]},
  "subject_concepts": ["pramana", "varnashrama"],
  "pramana_refs": ["@id1", "@id2"],
  "disputes": ["@claim_id"],
  "attitude": "asserts | quotes | defined_in_response_to | denies | disputes | remained_silent | privately_wrote_that",
  "modifier": "legendo — reported speech, not his own claim",
  "holding": {"tradition": "...", "position": "...", "weighting": "responsive|hard_strong|hard_weak"},
  "norm": {"confidence": 0.9, "state": "ratified|draft|disputed|superseded"},
  "layer": "siddhanta"
}
```

- `holding.weighting` honors hard/soft metaphysical differences (anti-reductionist vs absolute trees).
- `attitude` records how the attester held the claim (reporting ≠ endorsing; HERMENEUTIC duty of the acarya as a first-class relation).
- Rival realities coexist; an ecosystem of claims defeats simple truth assignment, and Gauḍīya internally hosts its own ragged edges (advaita-vs-dvaita tension, pre-1974 metasystem collapse).

---

## 5. `Pramāṇa`

The epistemological spine — evidence type per Tattva Sandarbha: śāstra pramāṇa as the functional connection to the transcendental, subdivided into vastu-tantra vs puruṣa-tantra, with the bhāgavata-śruti presented as a source text for the Gītā's own doctrinal context.

```json
{
  "@type": "Pramana",
  "kind": "shabda | anubhava | anumana | arthapatti | anupalabdhi | aitihya",
  "authority": {
    "primary": "shruti",
    "secondary": ["smriti", "itihasa", "purana"],
    "rank": ["shruti", "bhagavata", "gita", "sadhucarita", "atmatustih"]
  },
  "guru_parampara": ["Brahma", "Narada", "Vyasa", "..."],
  "sa-varnashrami state": {"novice": "...", "intermediate": "...", "prior_shadow": null},
  "norm": {"state": "candidate|confirmed", "confidence": 0.9}
}
```

- A `Claim` cites one or more `Pramāṇa` nodes; the "citation ancestry" UX of DESIGN.md §20 walks the pramana path.
- `anubhava` (direct experience) is modeled explicitly with an epistemic status — it can confirm a claim for one person without asserting universal authority.

---

## 6. `GuruAct`

The atomic unit of pedagogy — what the teacher appears to be *doing* (DESIGN.md §7). Modeling the doing, not the sound.

```json
{
  "@type": "GuruAct",
  "act_type": "QUESTION | REFRAME | CITE_SCRIPTURE | PRESCRIBE_PRACTICE",
  "sequence": 2,
  "target_state": "doubt",
  "addressed_concept": "shraddha",
  "span_text": "If it is without taste, why do you continue?",
  "span_closes": {"verse_ref": "brs 1.1.11", "pramana": "@p..."},
  "verse_anchor": "NO",
  "norm": {"state": "ratable", "confidence": 0.7},
  "interaction_ref": "@interaction_id"
}
```

`act_type` vocabulary is the closed set of DESIGN.md §7:

> EXPLAIN · QUESTION · CORRECT · CHALLENGE · ENCOURAGE · CONSOLE · REFRAME · PRESCRIBE · REMIND · INTERPRET · REFUSE · WARN · PRAISE · TELL_STORY · CITE_SCRIPTURE · REQUEST_PRACTICE · INVITE_REFLECTION

**Overlap alert (v0.2).** Pairs with known scoring ambiguity: `EXPLAIN/INTERPRET` (openly exposition that ascribes doctrine), `WARN/CHALLENGE` (valence contrast is not a stable rater criterion). The E0 annotation-fidelity pilot (gate: per-act κ ≥ 0.6, three raters, one tradition-literate) must pass **before** any act-model training; persistent-overlap pairs go to the adjudication queue for merge or redefinition. Non-verbal acts (silence duration, positioning, gifts, *darśana*) are out of scope for v0.1 and tracked as a future extension rather than silently dropped.

Estimation of observed frequencies is precise: the label applies to an observable discourse move, not an inner cause. And a provenance note the schema enforces: a GuruAct's *label* is itself an AI-generated annotation until a human rater ratifies it — ratified-by fields on the Act row record who (or what) confirmed each segmentation, so the five-layer provenance claim survives at the annotation layer too.


---

## 7. `Interaction`

An observed guru–person episode. Training unit for Phase II (DESIGN.md §27).

```json
{
  "@type": "Interaction",
  "teacher": "a.prabhupada",
  "interlocutor": {"role": "disciple", "octave": "present", "anonymized_id": "sha3-256:..."},
  "date": "1974-07-04",
  "location": "Los Angeles",
  "genre": "ROOM_CONVERSATION | LECTURE | LETTER | MORNING_WALK | PUBLIC_QA | PURPORT | PERSONAL_COUNSEL | ...",
  "input": {"situation": "...", "mode": ["remembrance", "doubt", "desire"], "concepts": ["anarthas", "sadhana"]},
  "relationship_history_available": "NO | YES",
  "acts": ["@guruact_id", "..."],
  "output": {"response": "...", "guru_acts": ["QUESTION", "REFRAME", "PRESCRIBE_PRACTICE"]},
  "source_ref": "bibliotech:books:<id>#page=41",
  "provenance": {"ensembles": "Vedabase transcript", "confidence": 0.95},
  "digest": "sha3-256:..."
}
```

The `bridge` (e.g., `interaction.acts` sequence) is congruent with what the guru visibly did, not with inner biography.

---

## 8. `Person` / `PersonState`

A guru converses with a person across occasions, not just a question.

```json
{
  "@type": "Person",
  "stars": "sadhaka | acharya | marginal | figure",
  "dimensions": ["sadhana", "vaishnava-seva", "shiksha", "identification"],
  "portrait": {"dispositions": {"confidence": "�..."}, "concepts_shipped": [], "octaves": []},
  "initiation_status": "uninitiated | harinama-initiated | brahminical-initiated | sannyasin | unknown",
  "norm": {"registration": "self_reported_only", "confidence": 0.8}
}
```

- `PersonState` is a dated, consensual snapshot: `{"ts": "...", "practices": [...], "open_questions": [...], "reflections": [...], "consent_scope": "practice_log+themes", "self_reported_rahita": "..."}`
- **No attainment claims** (DESIGN.md §23): fields record observable behavior and self-report; absence of_score is enforced — no bhāva inference, no spiritual score.
- Privacy: `consent_scope` gates what the Inquiry and Human-Guru modes may surface.

---

## 9. `Practice`

A named, positionally scoped sadhana with correspondence to concepts (DESIGN.md §22).

```json
{
  "@type": "Practice",
  "name": "japa | kirtana | shravana | smarana | sevā | svadhyaya",
  "tradition_scope": ["gaudiya"],
  "addresses": ["nāma-saṅkīrtana", "śravaṇaṁ"],
  "references": ["@verse_refs"],
  "prescriptions": [{"teacher": "a.prabhupada", "form": "16 rounds daily", "ref": "letter 1970-..."}],
  "nc_dim": ["practice", "identity", " Other-correlative-clause"]
}
```

`PracticeLog` is person-bound and observational: counts, timestamps, and free reflection — always self-reported, never scored.

---

## 10. `Inquiry` (Relationship Memory)

The longitudinal unit that makes a.guru's memory *continuity of inquiry* rather than chat convenience (DESIGN.md §9).

```json
{
  "@type": "Inquiry",
  "person": "@person_id",
  "topic_concepts": ["practice without taste", "attachment to practice"],
  "opened": "2026-03-14",
  "mutations": [
    {"ts": "2026-03-14", "kind": "opened", "statement": "Is practice without feeling authentic bhakti?"},
    {"ts": "2026-09-20", "kind": "reframe", "note": "attachment to practice itself"}
  ],
  "status": "open | resting | resolved_for_now | escalated_to_teacher",
  "referenced_by": ["@session_id"]
}
```

Mutations track how a question *changes over time* — the explicitly longitudinal spiritual-dialogue feature.

---

## 11. `Tradition`

```json
{
  "@type": "Tradition",
  "id": "gaudiya",
  "lineage_scopes": {"texts": ["ss.tattva", "brs"], "subschools": ["advaita-branch", " strictly-devotional-branch"]},
  "pramana_rank": ["shruti", "bhagavata-purana", "acharya-commentary", "sadhucarita", "atmatustih"],
  "disputes_with": ["shivite-traditions"],
  "norm": {"confidence": 1.0, "state": "ratified"}
}
```

Topology: Bhakti is not one doctrine but a family of traditions with branching lineages (DESIGN.md §16). `Claim.scope.tradition` and `Pramāṇa.authority.rank` prevent `Gauḍīya ≠ Śrī Vaiṣṇava` conflation.

### Sanskrit descriptors

- `bha` — bhakti; `pu` — puruṣa; `pramā` — pramāṇa; `adhi-kāra`-style per-concept keys reference the Sandarbha structure directly.
- `Tradition.pramana_rank` encodes per-lineage epistemology: shruti → bhāgavata-purāṇa → ācārya commentary → sādhucarita → ātmatuṣṭih (Tattva Sandarbha's own list).

---

## 12. Rules of Assembly

1. **BERTO conventions for Collections** — every GuruAct belongs to exactly one Interaction; every Pramāṇa is referenced by ≥1 Claim or stands as background; no orphan ai_inference claims.
2. **Feedback loop on correction** — an Interaction's `acts` may cite `regret_ref` when a later a.guru session freights them; uncertainty is not an error to be suppressed, but a developable resource and an honorific register of honesty.
3. **No numeric spirituality** — `PersonState` cannot absorb positive-language metrics (progress %, points); the absencing schema rejects scores at validation time.
4. **Manifestations, not emanations** — Interaction records describe *how the teacher appeared*, not *what the teacher is*; the ontological remainder is the research object, never a field.

---

## Open Questions (to resolve during ratification)

- Vertex coordinates: should `Prāṇa` (conscious agents) be a distinct top-level entity rather than folded into Person?
- Whether `GuruAct` needs a `refusal_taxonomy` (REFUSE variants: refused by humility vs refused by silence).
- How para-consistent logs should be vs full provenance: `state_full_prior_estates` vs mean-field summaries in `PersonState`.
- Encoding of `adhi-kāra` restrictions (right to practice/meditate on particular mantras) as graph edges vs Claim-scope fields.
