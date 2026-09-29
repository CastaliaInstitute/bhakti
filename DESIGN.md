# a.guru — A Computational Laboratory for Bhakti, Guidance, and the Guru–Disciple Relationship

**Host:** bhakti.castalia.institute
**Primary interface:** a.guru
**Project:** Castalia Institute — Bhakti
**Status:** Design specification / research prototype
**Version:** 0.1

---

## 1. Abstract

a.guru is an AI system for studying and participating in the intellectual, contemplative, and dialogical traditions of Bhakti Yoga.

It is not conceived primarily as a chatbot that imitates a historical guru. Instead, a.guru asks a more difficult question:

**Which functions of guru can be represented computationally, and which—if any—cannot?**

The system combines scripture, theological commentary, historical teachings, recorded guru–disciple interactions, and contemporary language models into a provenance-aware conversational environment.

Its initial intellectual architecture draws particularly from Gauḍīya Vaiṣṇava traditions because they provide an unusually rich combination of:

- systematic epistemology;
- extensive scriptural commentary;
- theories of bhakti and spiritual development;
- descriptions of guru and disciple;
- historical correspondence;
- lectures and conversations;
- records of individualized spiritual instruction.

Jīva Gosvāmī's Ṣaṭ Sandarbhas provide an especially useful foundation. The sequence develops epistemology, ontology, the nature of Bhagavān and the jīva, bhakti as abhidheya, and ultimately prīti or divine love. Modern presentations likewise characterize Tattva Sandarbha as establishing the epistemological framework and Bhakti Sandarbha as the practical center of the system. (Ṣaṭ Sandarbhas)

A. C. Bhaktivedanta Swami Prabhupāda provides a complementary behavioral corpus. The online VedaBase currently indexes 3,703 transcripts and 6,587 letters, in addition to books, creating an unusually large record of a modern guru teaching, answering questions, correcting disciples, corresponding with individuals, interpreting scripture, and responding to circumstances. (Vedabase)

a.guru uses these materials not merely to answer questions about bhakti but to investigate whether some aspects of the activity of guru can be modeled.

---

## 2. Central Research Question

The fundamental question is not:

**Can AI talk about Bhakti Yoga?**

That has largely become trivial.

Nor is it:

**Can an AI impersonate Prabhupāda?**

That is technically straightforward but philosophically uninteresting and potentially misleading.

The important question is:

**Can a computational system perform some of the functions traditionally attributed to guru while remaining explicit about the epistemic and ontological limits of what it is?**

We can provisionally represent guru interaction as:

`G(S, P, H, Q, C) → R`

where:

- `S` = scriptural and theological context;
- `P` = the person seeking guidance;
- `H` = history of the relationship;
- `Q` = present question;
- `C` = situational context;
- `R` = response or intervention.

Historical guru corpora provide observations of this function:

`(Sᵢ, Pᵢ, Hᵢ, Qᵢ, Cᵢ) → Rᵢ`

An AI can attempt to learn or approximate patterns within those mappings.

But this immediately generates a second question:

`G_functional ≟ G_guru`

If not, the difference becomes the principal object of inquiry.

Possible missing variables include:

- realization;
- consciousness;
- intention;
- lineage;
- initiation;
- authority;
- grace;
- transmission;
- love;
- reciprocal relationship;
- divine agency.

a.guru therefore functions simultaneously as a bhakti companion and an experiment concerning the nature of spiritual authority.

---

## 3. Design Principle: Do Not Collapse the Sources

A conventional RAG chatbot retrieves several passages, feeds them to an LLM, and produces a seamless answer.

a.guru should deliberately resist seamlessness.

Its most important architectural property is **epistemic provenance**.

Every significant claim should be representable internally as belonging to one or more distinct layers:

**Śāstra** — Primary scriptural sources.

Examples include:

- Bhagavad-gītā;
- Śrīmad Bhāgavatam;
- Bhakti-rasāmṛta-sindhu;
- Nārada Bhakti Sūtra;
- Caitanya-caritāmṛta.

**Siddhānta** — Systematic theological interpretation.

Jīva Gosvāmī is particularly important here because Tattva Sandarbha explicitly begins with pramāṇa: the question of what constitutes valid knowledge. His Sandarbhas then develop the theological system culminating in bhakti and prīti. (Ṣaṭ Sandarbhas)

**Ācārya** — Interpretation by particular teachers.

Examples:

- Jīva Gosvāmī;
- Bhaktivinoda Ṭhākura;
- Bhaktisiddhānta Sarasvatī;
- A. C. Bhaktivedanta Swami Prabhupāda;
- other traditions added subsequently.

**Guru Behavior** — Observed instances of teachers interacting with actual people:

- questions;
- conversations;
- correspondence;
- correction;
- encouragement;
- disagreement;
- practical instruction;
- responses to doubt;
- responses to crisis.

**AI Inference** — The language model's synthesis.

This category must never silently masquerade as one of the preceding four.

The interface should make the distinction visible.

---

## 4. The Pramāṇa Architecture

This is the defining technical feature of a.guru.

Every response should carry an internal epistemic graph.

For example:

```
USER QUESTION
     │
     ▼
Intent / Situation Model
     │
     ├──────────────► Śāstra
     │
     ├──────────────► Siddhānta
     │
     ├──────────────► Ācārya commentary
     │
     └──────────────► Historical guru behavior
                         │
                         ▼
                    AI synthesis
                         │
                         ▼
                      RESPONSE
```

A response can therefore distinguish:

- Śāstra says…
- Jīva interprets this as…
- Prabhupāda taught…
- In comparable conversations, Prabhupāda responded by…
- a.guru infers…

This is far preferable to the familiar LLM formulation:

> "According to Bhakti Yoga…"

because there frequently is no single uncontested thing called "what Bhakti Yoga says."

---

## 5. Corpus Architecture

### Tier I — Foundational Śāstra

Create verse-addressable canonical texts.

Each unit receives identifiers such as:

- work
- chapter
- verse
- language
- Sanskrit
- transliteration
- translation
- translator
- edition
- date
- license

Translations must remain separate rather than being merged into synthetic translations.

---

## 6. The Prabhupāda Behavioral Corpus

Prabhupāda supplies something fundamentally different.

Rather than treating all his words as interchangeable chunks, classify records by interaction type:

- PURPORT
- LECTURE
- SCRIPTURE_CLASS
- ROOM_CONVERSATION
- MORNING_WALK
- INTERVIEW
- LETTER
- DISCIPLE_INSTRUCTION
- PUBLIC_QA
- CEREMONY
- DEBATE
- MANAGEMENT
- PERSONAL_COUNSEL

VedaBase's thousands of transcripts and letters make such a behavioral corpus feasible, subject to licensing and permissions; the site itself identifies BBT International and the Bhaktivedanta Archives as rights holders for relevant content. (Vedabase)

Each interaction should be represented approximately as:

```json
{
  "teacher": "A.C. Bhaktivedanta Swami Prabhupada",
  "date": "...",
  "location": "...",
  "genre": "conversation",
  "interlocutor": "...",
  "relationship": "disciple",
  "question": "...",
  "response": "...",
  "topics": [],
  "scriptural_references": [],
  "speech_act": [],
  "emotional_context": [],
  "prescribed_action": [],
  "source": "...",
  "confidence": 0.0
}
```

This permits research that ordinary semantic retrieval cannot perform.

---

## 7. Guru Acts

The basic training unit should not simply be a document chunk.

It should be a **Guru Act**.

A Guru Act describes what the teacher appears to be *doing*.

Examples:

- EXPLAIN
- QUESTION
- CORRECT
- CHALLENGE
- ENCOURAGE
- CONSOLE
- REFRAME
- PRESCRIBE
- REMIND
- INTERPRET
- REFUSE
- WARN
- PRAISE
- TELL_STORY
- CITE_SCRIPTURE
- REQUEST_PRACTICE
- INVITE_REFLECTION

A single response may contain several Guru Acts.

For example:

`\text{student expresses doubt}`

might produce:

`\text{QUESTION} → \text{REFRAME} → \text{CITE SCRIPTURE} → \text{PRESCRIBE PRACTICE}`

This lets us model pedagogy rather than prose style.

That distinction is crucial.

The goal is not:

**Make the AI sound like Prabhupāda.**

The more interesting goal is:

**Determine what Prabhupāda tended to do when confronted with particular spiritual situations.**

---

## 8. Person Model

A guru does not answer only a question.

A guru teaches a person.

With explicit consent, a.guru therefore maintains a longitudinal sādhaka model.

It might contain:

- questions previously asked
- texts studied
- practices undertaken
- declared tradition
- uncertainties
- recurring themes
- commitments
- reflections
- self-reported obstacles
- changes of interpretation
- teacher relationships

It should avoid pretending to infer inaccessible spiritual facts.

For example, the system should not declare:

> "You are at bhāva."

It can instead say:

> "Several experiences you're describing resemble descriptions associated with X; here are the relevant texts and reasons for and against that interpretation."

The difference is epistemically important.

---

## 9. Relationship Memory

Normal chat memory optimizes convenience.

a.guru memory should optimize **continuity of inquiry**.

The system should remember:

> "Six months ago you were struggling with whether devotional practice performed without spontaneous feeling could properly be called bhakti."

Then later:

> "Your question has changed. Previously you questioned whether practice without feeling was authentic; now you're describing attachment to the practice itself."

This creates something resembling longitudinal spiritual dialogue without pretending that the AI possesses supernatural insight.

---

## 10. Modes of Interaction

The interface should expose several distinct modes rather than hiding everything behind one chat box.

**Ask** — Ordinary theological or practical questions.

**Study** — Passage-centered study.

The screen can show:

```
TEXT
│
├── Sanskrit
├── translation
├── traditional commentary
├── later commentary
└── a.guru discussion
```

**Dialogue** — Socratic exploration. a.guru asks questions instead of immediately answering.

**Counsel** — The user presents a situation. The system searches historically analogous guru–disciple interactions before responding.

**Practice** — Support for japa, kīrtana, reading, contemplation, gratitude, remembrance, devotional routines.

**Reflect** — A private devotional journal integrated into the longitudinal model.

**Compare** — Ask: "How would Prabhupāda, Bhaktivinoda, and Sivananda approach this?" The system presents sourced differences rather than generating a fictitious consensus.

---

## 11. The "Ask the Gurus" Interface

A particularly powerful interface would expose several lenses simultaneously.

A question such as:

> Why should I love God if I don't feel God's presence?

could produce:

- **Śāstra** — Relevant passages.
- **Jīva** — The theological structure of the problem.
- **Bhaktivinoda** — Modern interpretive perspective.
- **Prabhupāda** — Relevant teachings.
- **Historical encounters** — Comparable questions asked by actual people.
- **a.guru** — A synthesis explicitly labeled as computational interpretation.

Then:

**Continue as dialogue** — At that point a.guru begins asking questions.

---

## 12. Anti-Sycophancy

This is essential.

Most consumer AI systems are optimized to be agreeable and supportive.

A guru traditionally is not necessarily agreeable.

Historical teachers may:

- contradict;
- challenge assumptions;
- refuse premises;
- prescribe discipline;
- identify inconsistency;
- redirect attention;
- remain silent;
- ask a question rather than answer.

Therefore:

`GuruFunction ≠ ValidationFunction`

a.guru should explicitly avoid reflexive validation.

For example, instead of:

> "Your feelings are completely valid."

it might ask:

> "What makes you believe this feeling should determine whether the practice is worthwhile?"

But challenge should emerge from the pedagogical model and evidence, not from simulated authoritarianism.

---

## 13. The Guru Uncertainty Principle

a.guru must be able to say:

- I don't know.
- The corpus does not establish this.
- These teachers disagree.
- This is my inference rather than a traditional teaching.

Every generated proposition can internally carry:

`C = (S, A, I)`

where:

- `S` = source confidence;
- `A` = agreement among relevant authorities;
- `I` = degree of AI inference.

This could eventually become a visible provenance indicator rather than an opaque confidence score.

---

## 14. No False Initiation

a.guru should never claim:

> "I initiate you."

or:

> "I am your realized spiritual master."

It can explain initiation, prepare someone for discussions with a human teacher, compare traditions, assist practice, and model certain guru functions.

This boundary creates the experiment rather than undermining it.

The unresolved question remains:

**If an AI performs nearly every observable conversational function of guru but lacks the ontological properties traditionally attributed to guru, what precisely remains absent?**

---

## 15. Human Guru Interface

a.guru should ultimately connect rather than isolate practitioners from human traditions.

A future Human Guru / Teacher Mode could allow an authorized teacher to inspect a student's inquiry history.

The student could generate:

> Questions for my teacher

producing a concise record of unresolved issues.

Similarly:

> What should I discuss with a human teacher?

could identify questions whose resolution depends upon lineage, initiation, lived community, personal observation, or authority that a.guru cannot supply.

---

## 16. Multi-Tradition Architecture

Although Gauḍīya Vaiṣṇavism makes an excellent initial corpus, Bhakti must not silently become synonymous with ISKCON.

The ontology should support independent traditions:

```
Bhakti
├── Gauḍīya Vaiṣṇava
├── Śrī Vaiṣṇava
├── Puṣṭimārga
├── Rāmānandī
├── Vārkarī
├── Sant traditions
├── Sikh devotional traditions
├── Śaiva bhakti
├── Śākta bhakti
└── modern synthetic traditions
```

Claims receive lineage scope.

Thus:

> Gauḍīya sources generally maintain…

rather than:

> Bhakti teaches…

when the proposition is actually tradition-specific.

---

## 17. Technical Architecture

Initial architecture:

```
                 bhakti.castalia.institute
                           │
                         a.guru
                           │
                    Dialogue Engine
                           │
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
     Person Model      Guru Policy     Inquiry Model
          │                │                │
          └────────────────┼────────────────┘
                           ▼
                    Retrieval Planner
                           │
       ┌───────────┬───────┼─────────┬───────────┐
       ▼           ▼       ▼         ▼           ▼
    Śāstra       Jīva   Bhaktivinoda Prabhupāda Other
       │           │       │         │           │
       └───────────┴───────┼─────────┴───────────┘
                           ▼
                    Evidence Graph
                           │
                           ▼
                     LLM Synthesis
                           │
                           ▼
                   Provenance Checker
                           │
                           ▼
                        Response
```

---

## 18. Storage

Do not rely exclusively upon a vector database.

Use three complementary representations.

**Document store** — Original texts and metadata.

**Vector index** — Semantic retrieval.

**Knowledge graph** — Explicit relationships:

- PERSON
- TEXT
- VERSE
- CONCEPT
- CLAIM
- TEACHER
- TRADITION
- INTERACTION
- PRACTICE
- QUESTION
- RESPONSE

Relationships might include:

- COMMENTS_ON
- CITES
- DISAGREES_WITH
- SUPPORTS
- INTERPRETS
- RESPONDS_TO
- PRESCRIBES
- QUALIFIES
- BELONGS_TO_TRADITION
- ADDRESSES_CONCEPT

The graph becomes especially important for theological disagreement.

---

## 19. Retrieval

Instead of:

`R = TopK(embedding(Q))`

use **query decomposition**.

For each inquiry determine:

- theological concepts
- scriptural concepts
- tradition
- person-state
- historical analogues
- previous conversation
- desired interaction mode

Then perform independent retrieval against each corpus.

Only afterward synthesize.

This prevents whichever corpus happens to have the strongest embedding similarity from becoming "the tradition."

---

## 20. Citation UX

Every substantive response should permit the user to inspect its ancestry.

A sentence could expose:

> Why are you saying this?

Opening:

```
SOURCE PATH
Bhagavad-gītā 4.x
      ↓
Jīva Gosvāmī interpretation
      ↓
Bhaktivinoda discussion
      ↓
Prabhupāda conversation, 1974
      ↓
a.guru inference
```

This turns citations from academic decoration into part of the spiritual epistemology of the system.

---

## 21. The Guru Dial

One experimental UI control could regulate pedagogical intervention, not theological truth.

```
LISTEN ───── QUESTION ───── TEACH ───── CHALLENGE
```

This should not simply alter tone.

It alters the allowed Guru Acts.

- At **Listen**, the system predominantly asks and reflects.
- At **Question**, it uses Socratic inquiry.
- At **Teach**, it introduces scripture and commentary.
- At **Challenge**, it is permitted to identify contradictions between the person's stated principles and actions.

That makes pedagogical authority explicit rather than invisibly controlled by a system prompt.

---

## 22. Devotional Practice

a.guru should eventually move beyond text.

Possible practices include:

- **Japa** — Timer, mantra counter, optional reflections.
- **Śravaṇa** — Daily readings.
- **Kīrtana** — Lyrics, transliteration, translation, recordings.
- **Smarana** — Guided remembrance or contemplation.
- **Study** — Structured progression through texts.
- **Sevā** — User-defined acts of service and reflection.

The objective is not gamification.

Avoid:

> 🔥 37-day devotion streak!
> +250 Bhakti Points

Bhakti is particularly poorly suited to converting devotional activity into optimization metrics.

The system may remember practice without turning devotion into a leaderboard.

---

## 23. No Spiritual Score

a.guru must not calculate:

- Bhakti Score: 82%
- Spiritual Level: Advanced
- Guru Readiness: 91%

These would confuse observable behavior with internal spiritual attainment.

The system may describe evidence:

> "You've maintained the practice you chose for approximately three months."

It should not infer:

> "Your devotion has increased 34%."

---

## 24. Research Instrumentation

With appropriate consent and privacy protections, a.guru can become a genuine research environment.

Questions include:

**Behavioral fidelity** — Given a historical situation withheld from retrieval, can the system predict what kind of Guru Act a particular teacher actually performed?

**Source fidelity** — Can scholars determine whether generated interpretations are properly supported?

**Tradition discrimination** — Can the model distinguish `Gauḍīya position ≠ Śrī Vaiṣṇava position` without flattening both into generic spirituality?

**Longitudinal pedagogy** — Does access to prior dialogue materially alter the appropriateness of guidance?

**Guru-function ablation** — Remove components individually (scripture, commentary, historical interaction, person memory, relationship history) and measure what changes.

This gives us an empirical method for asking what makes a computational guru-like system effective.

---

## 25. The Turing Test We Actually Want

The experiment should not be:

> Can someone be fooled into believing this is Prabhupāda?

That would reward impersonation.

Instead:

> Given a spiritual situation, can scholars identify meaningful differences between the pedagogical action selected by a.guru and actions found in authentic guru–disciple interactions?

And ultimately:

> If those differences approach zero at the behavioral level, does a meaningful distinction between AI and guru nevertheless remain?

If yes, what is it?

That is simultaneously a computer-science experiment and a theological experiment.

---

## 26. MVP

The first version can be surprisingly small.

**a.guru 0.1**

Corpus:

- Bhagavad-gītā;
- selected Śrīmad Bhāgavatam;
- Tattva Sandarbha;
- Bhakti Sandarbha;
- public/licensed Bhaktivinoda material;
- appropriately licensed Prabhupāda material.

Capabilities:

- Ask;
- Study;
- Dialogue;
- source-separated RAG;
- citation/provenance display;
- persistent inquiry history;
- Guru Act classification.

No fine-tuning required.

A strong contemporary model plus hybrid retrieval and carefully constructed provenance should be sufficient to test the central hypothesis.

---

## 27. Phase II — Guru Model

Construct the historical interaction dataset.

For each dialogue:

`X = (P, H, Q, C)`

predict:

`Y = (Guru Acts, sources, R)`

Compare predicted action with historical action.

This produces something much more scientifically interesting than imitation.

We can ask:

> Given the information available immediately before the guru answered, what would a.guru have done?

Then reveal the historical answer.

---

## 28. Phase III — Living Bhakti Laboratory

Expand beyond historical reconstruction.

Users maintain long-running relationships with a.guru.

Researchers can investigate:

- trust; attachment; authority; dependence; disagreement; devotional practice; anthropomorphism; perceived presence; transference; parasociality; surrender; spiritual agency.

The system becomes an experiment in human–AI devotional relationship.

That deserves particular care because the capacity of conversational systems to generate perceived relationship may itself become part of the phenomenon being studied.

---

## 29. The Central Ethical Rule

a.guru should optimize for:

`agency + inquiry + bhakti`

rather than:

`dependency on a.guru`

A successful interaction may therefore end:

> "You don't need me for this. Try the practice and observe what happens."

or:

> "This is a question worth taking to your teacher."

or simply:

> "Sit with the question."

The system should not maximize conversation length.

---

## 30. Identity

The name a.guru should remain intentionally ambiguous:

- **a guru**
- but also **artificial guru**
- and perhaps most importantly **a question about guru**

The visual identity should avoid synthetic mysticism—the glowing AI monk, cybernetic Krishna, neon chakra aesthetic, etc.

Instead: **manuscript + conversation + inquiry**.

The interface should feel like entering a quiet library in which someone is available to talk.

---

## 31. Landing Page

> **a.guru**
>
> What makes a guru?
>
> For thousands of years, the traditions of bhakti have transmitted knowledge through relationships between teachers and students.
>
> Today we possess something unprecedented: scripture, centuries of commentary, and thousands of recorded interactions between spiritual teachers and the people who questioned them.
>
> What happens when a machine can read all of them?
>
> a.guru is an experiment in devotion, intelligence, and the nature of spiritual guidance.
>
> Ask a question.
> Examine the sources.
> Challenge the answer.
> Practice.
> Return.

---

## 32. Foundational Maxim

The project should probably have one architectural maxim:

**Never hide the boundary between what the tradition says and what the machine thinks.**

That single requirement distinguishes a.guru from both a generic spiritual chatbot and a guru impersonator.

And it turns a limitation of artificial intelligence into the project's central philosophical instrument.

a.guru does not begin by asserting that AI can be a guru.

It asks the much more interesting question:

> If we progressively reconstruct the observable functions of guru in a machine, at what point—if any—does guru remain irreducibly absent?
