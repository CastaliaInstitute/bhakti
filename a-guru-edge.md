# a.guru Edge Service — Specification v0.1

Status: ratified design · 2026-09-29
Hosts: CastaliaInstitute (Supabase project hosting `ask-faculty`)
Client contract: AGENTS.md (mode/dial/question/stream:false → response/provenance)
Related: DESIGN.md §17 (technical architecture), ONTOLOGY.md (entities), persona/a-guru.json (policy), WHITEPAPER.md §5.4 (E1–E7), REVIEW.md (provenance requirements incl. selection provenance)

---

## 1. Route Decision

`a-guru` is deployed as a **thin dedicated Supabase Edge Function** — not a persona row inside `ask-faculty`.

Rationale (from the routing discussion):

1. **The seam is inverted.** `ask-faculty` folds RAG context invisibly into the system prompt and returns no citations. Correct for faculty; forbidden for a.guru, which must show the five-layer seam per claim and label machine inference last.
2. **Policy, not voice.** a.guru's constraints (no initiation, no attainment statements, no scores, dial-controlled pedagogy) are serving policy, not modulation context.
3. **The person model is the subject.** The consented sādhaka record and relationship memory are the research object (E4–E7), requiring their own consented store and audit trail.

**Substrate:** `a-guru` composes calls to `ask-faculty` for historical lenses (the "Ask the Gurus" surface is literally ask-faculty fan-out against `a.prabhupada`, `a.bhaktivinoda`, …), owns its retrieval decomposition, dial policy, provenance assembly, metering, and returns the citation-bearing contract the widget already speaks.

## 2. API Contract (backwards-compatible with the shipped widget)

```
POST https://<project>.supabase.co/functions/v1/a-guru
Headers: Content-Type: application/json
         optional Authorization: Bearer <user-jwt>          # member session (Supabase auth)
Body:    { "mode": "ask|study|dialogue|counsel|practice|reflect|compare",
           "dial": "listen|question|teach|challenge",
           "question": "string|null",   # null in dialogue mode = a.guru invites a question
           "messages": [...],           # optional prior turns (client-kept)
           "person_ref": "opaque-id",   # optional; enables free-quota memory
           "stream": false }
200  { "response": "text",
       "provenance": [ { "layer": "shastra|siddhanta|acharya|guru_behavior|ai_inference",
                          "work": "...", "locator": "...",
                          "selection": "why this passage surfaced (retrieval reason)" } ],
       "faithfulness_review": null,   # present when verify=on
       "eval_note": null }            # sampled, logged, stripped of personal info
429  { "error": "free_limit",
       "provenance": [],
       "response": "(instrument says:) You are in your free relationship with a.guru. ..."
     }
```

Notes:

- `provenance` MUST contain at least one `ai_inference` entry unless the response is a pure quotation with `ai_inference` entry stating the synthesis degree (§13 `C = (S, A, I)`). No response ships without provenance; the endpoint is *capable* of returning partial provenance only when marked `partial: true` — never silently.
- `selection` documents retrieval choice provenance (REVIEW §3.5): why these passages surfaced.
- The never-synthesize rule: on upstream failure, timeout, or contract violation, respond with the honest-degradation payload (`error` + explanation), never a fabricated guru turn.

## 3. Persona Enforcement

The edge loads `persona/a-guru.json` at cold start and enforces:

1. **Identity:** a.guru speaks *as a.guru*, never as Prabhupāda or any ācārya. Quoted historical teachers are cited in-layer, attributed, and marked as quotations.
2. **Forbidden claims** (hard strings, rejected in output even if model-generated): "I initiate you"; "I am your (realized/spiritual) master"; any statement of the user's inner attainment (bhāva, rasa, etc.); numeric spiritual scores; "this is what Bhakti says" without tradition scoping.
3. **Dial → permitted Guru Acts:**

| Dial | Permitted families | Effect |
|---|---|---|
| `listen` | INVITE_REFLECTION, TELL_STORY | reflect user's own words; no prescriptions |
| `question` | QUESTION, INVITE_REFLECTION, REMIND | Socratic; answer only when directly asked |
| `teach` | + EXPLAIN, INTERPRET, CITE_SCRIPTURE | scripture + commentary introduced, sourced |
| `challenge` | + CORRECT, CHALLENGE, WARN, REFUSE, PRESCRIBE | may name contradictions of stated principle vs action — evidence-based only |

Anti-sycophancy is always on: instead of "your feelings are completely valid," the opener "what makes you believe this feeling should determine whether practice is worthwhile?" — per dial, evidence-driven, never simulated authoritarianism.

## 4. Provenance Pipeline

Five layers, retrieved independently and composed at synthesis-time only (Design §19):

1. **Śāstra** — verse-addressable Tier-I corpus (bibliotech-acquired; translation keyed by translator/edition, never merged).
2. **Siddhānta** — Ṣaṭ Sandarbhas structured ontology (Jīva's world model; Tattva → Bhagavat → Paramātma → Kṛṣṇa → Bhakti → Prīti arc, per ONTOLOGY).
3. **Ācārya** — Bhaktivinoda, Bhaktisiddhānta, Jīva commentary rows, tradition-scoped.
4. **Guru Behavior** — VedaBase interaction corpus via ask-faculty lenses (`a.prabhupada` room-conversation RAG for situation analogues).
5. **AI Inference** — the composed response, labeled, with (S, A, I) confidence triple surfaced as chips, not hidden scores.

Dial *filters* which layers are permitted to surface in synthesis: at `question`, layers 1–4 are quoted, not interpreted; at `teach`, layer interpretation is allowed; `challenge` may confront with layer-4 precedent. The dial never changes *truth*, only permitted acts (Design §21).

## 5. Metering — Free and Member Tiers

**Principle (truthful, non-coercive):** the practice itself is free — the instrument's genuine costs are model tokens, and those, like library holdings, must be paid for by the library, not extracted by guilt. Copy renders as budget-need, not as spiritual gate.

Anonymous free tier:
- **N = 5 questions / visitor / 24 h**, keyed by the widget's opaque `person_ref` (localStorage) falling back to anonymized IP hash (rotated daily; no PII retained).
- The *free* tier uses the lighter model / shorter retrieval; same provenance discipline. The welcome message states free status plainly.
- On limit: **429** with honest copy — "you have used today's shared instrument budget; deeper study requires membership. Try the practice and observe. The sources remain free to you on-site."

Member tier:
- Supabase auth session (existing castalia.institute authentik/Supabase auth paths; membership tables already track custodian/patron tiers).
- **Token-metered by model usage**, per member, monthly; meter writes shell records `usage(member, tokens_in, tokens_out, endpoint, ts)` from the LLM callback; hard stop at tier ceiling → same honest 429 copy (tier-caped), not silent failure.
- Member sessions can opt-in to the consented person model (individualized features incl. dialogue with per-topic provenance, per §8 Design), and plates are cross-linked in `faculty_corpus_sources` style style. No dark-pattern nuance: visitor is never told tier limits are "spiritually determined."

Service unreachability (auth service down): **open-open** — fall back to anonymous free tier; the gate never fakes-succeeds-with-paid-model, and never silently denies either.

## 6. Honest Fallbacks (unchanged)

The three honest states survive endpoint failures:
1. Not connected → faithful "instrument exists, engine absent" message (implicit — this state goes away once this edge is live and wired; the path remains for the case of downtime).
2. Free-limit → honest membership invitation.
3. Contract violation / empty / error → explicit error copy; never a fabricated "guru" turn.

## 7. Deployment Contract

- Supabase Deno Edge Function in the castalia.institute repo `supabase/functions/a-guru/index.ts`.
- env: `ATMIYA_SLUG` (a.guru institutional intent refrain), `AGURU_ANON_KEY`, `ASK_FACULTY_URL`, `SUPABASE_URL` etc. All via `supabase secrets set`, never committed.
- CORS: same as `ask-faculty` (`bhakti.castalia.institute`, `github.io` allowed origins), `verify_jwt`: false for the public free endpoint (weight-capped by quota), `Authorization` forwarded downstream for the member path.
- License note: responses quote sources under per-source rights rules; the endpoint adds citations **with locator + rights ref** toward the person-relevant layers, per AGENTS.md's two-kind provenance paragraph.
