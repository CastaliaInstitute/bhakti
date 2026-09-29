# Local development & conventions

## Site

Static GitHub Pages site, deployed from the **root of `main`** (`.nojekyll` present; CNAME file pins `bhakti.castalia.institute`).

- Preview: `python3 -m http.server` from repo root, open http://localhost:8000
- Deploy: push to `main`; Pages builds automatically.

## a.guru chat widget

`index.html` ships a lower-right chat popup. It POSTs to the endpoint in
`localStorage.aguru.endpoint` (default `/api/a-guru`) and expects:

```json
POST { "mode": "dialogue|ask|counsel|...", "dial": "listen|question|teach|challenge", "question": "…", "stream": false }
200  { "response": "…", "provenance": [ {"layer": "shastra|siddhanta|acharya|guru_behavior|ai_inference", "work": "…", "locator": "…"}, ... ] }
```

Until an engine exists the widget degrades to an honest "not connected" message — never a fake guru answer.

## Hard rules of this project (from DESIGN.md)

1. **Never hide the boundary** between what the tradition says and what the machine thinks — AI inference is a labeled provenance layer, never disguised as śāstra/siddhānta/ācārya.
2. **No false initiation** — a.guru never claims to initiate or to be a realized master.
3. **No spiritual scores** — no devotion points, levels, or attainment percentages, in any UI.
4. **Keep sources separated** — translations are parallel, never merged into synthetic composites; claims are tradition-scoped.
5. **Anti-sycophancy** — challenge comes from the pedagogical model and evidence, not simulated authoritarianism.

## File conventions

- `DESIGN.md` is the canonical spec (a.guru v0.1). Amend via PR with rationale in `metadata.changelog` of `persona/a-guru.json` when behavior contracts change.
- `ONTOLOGY.md` entities graduate to `schemas/*.json` (JSON Schema draft-07) once ratified.
- `corpus/**` holds derived JSONL conforming to those schemas; raw acquisition lives in the **bibliotech** repo and must never be committed here.
- No comments in code unless the file is educational by design (this repo's static pages are self-documenting).
