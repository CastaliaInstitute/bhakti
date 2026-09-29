# bhakti.castalia.institute

**Can an AI be a guru?** An inquiry of the Castalia Institute into Bhakti Yoga, devotional guidance, and the guru–disciple relationship.

**bhakti** is the inquiry. **a.guru** is the experimental instrument.

- Site: https://bhakti.castalia.institute (via `a.guru`)
- Spec: [DESIGN.md](DESIGN.md) — a.guru v0.1 design specification
- Ontology: [ONTOLOGY.md](ONTOLOGY.md) — Claim, Pramāṇa, GuruAct, PersonState, Practice, Tradition, Interaction
- Persona: [persona/a-guru.json](persona/a-guru.json) — Castalia persona-schema definition for the a.guru computational guru-function

## The research question

Not *can a chatbot discuss bhakti* (trivial), and not *can an AI impersonate Prabhupāda* (technically easy, philosophically uninteresting). Rather:

> Can a computational system perform some of the functions traditionally attributed to guru — while remaining explicit about the epistemic and ontological limits of what it is?

Formally: `G_functional ≟ G_guru`. If not, the difference is the principal object of inquiry.

## Foundational maxim

**Never hide the boundary between what the tradition says and what the machine thinks.**

Every response carries a provenance graph through five layers: Śāstra → Siddhānta → Ācārya → Guru Behavior → AI Inference. The last layer must never silently masquerade as one of the first four.

## Repository structure

```
bhakti/
├── DESIGN.md        # a.guru v0.1 design specification
├── ONTOLOGY.md      # core data-schema design (draft → schemas/ when ratified)
├── schemas/         # JSON Schemas (from ONTOLOGY.md when ratified)
├── persona/         # a.guru faculty persona (Castalia persona schema v1.1.0)
├── corpus/          # derived, structured corpora (ingested from bibliotech; see below)
│   ├── shastra/
│   ├── siddhanta/
│   ├── acharya/
│   └── guru-behavior/
├── index.html       # bhakti.castalia.institute landing page
└── CNAME            # bhakti.castalia.institute (GitHub Pages)
```

## Corpus pipeline

Raw acquisition lives in [bibliotech](https://github.com/CastaliaInstitute/bibliotech) — the Castalia library system, which ingests the Vedabase corpus (Prabhupāda: thousands of transcripts and letters) using its S3/GCS path convention (`s3://castalia-institute-corpora/corpora/<source>/<faculty_id>/…`, see bibliotech's `docs/S3_CORPUS_ORGANIZATION.md`) and registers works in its `books` / `faculty_corpus_sources` tables.

bhakti consumes **derived, structured artifacts**. From the bibliotech-held sources, ingestion MUST emit JSONL records under `corpus/<layer>/<tradition>/` that:

1. **Keep sources separated** — one record per provenance layer (Śāstra / Siddhānta / Ācārya / Guru Behavior); translations are never merged into synthetic composites.
2. **Classify genre** — `PURPORT`, `LECTURE`, `ROOM_CONVERSATION`, `MORNING_WALK`, `LETTER`, `DISCIPLE_INSTRUCTION`, `PUBLIC_QA`, `PERSONAL_COUNSEL`, etc. (see DESIGN.md §6).
3. **Emit Guru Acts** — each record is segmented into Guru Acts (`EXPLAIN`, `QUESTION`, `CORRECT`, `REFRAME`, `PRESCRIBE`, `REFUSE`, …) per DESIGN.md §7 and `schemas/guru-act.schema.json`.
4. **Carry verse addressing** — Tier I sources use `work.chapter.verse` IDs with translator/edition metadata (DESIGN.md §5).
5. **Respect lineage scope** — tradition field required; claims are scoped (`Gauḍīya …`, `Śrī Vaiṣṇava …`) per DESIGN.md §16.
6. **Record license and rights** — Vedabase content is © BBT International / Bhaktivedanta Archives; every record carries `license` and `rights_holder`, matching bibliotech's rights-tracking conventions.
7. **Backreference bibliotech** — each record carries a `source_ref` (S3 path and/or bibliotech `books` row id) so provenance resolves back to the acquisition layer.

The retrieval planner consumes these schema-conformant records, not raw transcripts.

## Building locally

The site is static HTML — open `index.html`, or run any static server from the repo root, e.g. `python3 -m http.server`.

## Relationship to the Castalia faculty system

a.guru participates in the Castalia Institute faculty-persona framework (`persona.schema.json` v1.1.0) but is a **research instrument, not a faculty persona** in the usual sense: it is not a reconstructed historical figure and makes no impersonation claims. Its persona artifact encodes the guru-function contract (provenance discipline, anti-sycophancy, no false initiation) rather than a historical voice.

## Status

Design specification / research prototype — v0.1. MVP (DESIGN.md §26): corpus for Gītā, selected Bhāgavatam, Tattva + Bhakti Sandarbha, licensed Bhaktivinoda and Prabhupāda material; modes Ask / Study / Dialogue; source-separated RAG with provenance display.
