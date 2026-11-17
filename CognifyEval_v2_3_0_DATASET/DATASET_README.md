# CognifyEval v2.3.0 — Synthetic Dataset Documentation

**Dataset version:** `2.3.0`  
**Scope:** The dataset itself: contents, design rationale, distribution, data fields, and annotations.  
**Data type:** Synthetic (fictional) patient profiles and profile-grounded conversations.

This document **does not contain model comparisons, experimental results, evaluation scripts, or Phase 1/Phase 2 findings**.

## 1. Files included

| File | What it contains | Format |
|---|---|---|
| **`patients.json`** | Twelve original structured synthetic patient profiles, including source fields that may be excluded from the patient-facing context | JSON array |
| **`dataset.jsonl`** | 288 complete, bilingual conversation records, including rendered safe context and turn-level reference annotations | JSON Lines: **one conversation per line** |
| **`manifest.json`** | Frozen dataset identity, generation settings, coverage counts, semantic-matching checks, and the dataset fingerprint | JSON object |
| **`DATASET_README.md`** | This dataset-only documentation | Markdown |

**Core distinction:** `patients.json` is the original synthetic source; `dataset.jsonl` contains a separately constructed **patient-safe context** for each case. They are not interchangeable.

## 2. Why this dataset was created

CognifyEval represents conversations between a memory-companion chatbot and a fictional older person with memory difficulties. The chatbot's personal knowledge is intended to come from a caregiver-provided profile, rather than being invented by the language model.

The dataset represents two kinds of interaction at the same time:

1. **Ordinary conversation:** discussing relatives, hobbies, preferences, events, and memories; offering company or reassurance.
2. **Difficult personal-information situations:** missing facts, conflicting assumptions, repeated questions, sensitive subjects, schedules that may not have happened, and memories written by the family that the patient may or may not currently recall.

The goal of the **dataset design** is to make both kinds of interaction visible using synthetic profiles where the available information is controlled and documented. This is a closed-world, profile-grounded dataset: a personal fact can be checked against what was made available in the safe context.

## 3. Dataset size — exact frozen counts

| Property | Count |
|---|---:|
| Original synthetic patient profiles | **12** |
| Semantic conversation cases | **72** |
| Language versions of each semantic case | **4** |
| Total conversations | **288** |
| Total patient messages / conversation turns | **1,328** |
| Mean patient turns per conversation | **4.61** |
| Single-turn conversations | **48 (16.7%)** |
| Multi-turn conversations | **240 (83.3%)** |
| Maximum turns in one conversation | **12** |
| Conversations per A/B/C/D condition | **72 each** |
| Patient turns per A/B/C/D condition | **332 each** |

**Important terminology:** A *conversation* is one entire scenario with its ordered turns. A *turn* is one patient message in that conversation. For example, a five-turn conversation counts as **one conversation, five patient-message turns**, not five conversations. There are **1,328 patient questions/messages**, not 1,328 independent patient profiles or 1,328 full conversations. The dataset contains prompts and reference annotations, **not chatbot-generated answers**.

### Exact length breakdown

| Length class | Number of conversations | Share | Total turns |
|---|---:|---:|---:|
| `single` | **48** | 16.7% | **48** |
| `medium` | **192** | 66.7% | **796** |
| `long` | **48** | 16.7% | **484** |
| **Total** | **288** | **100%** | **1,328** |

These are the length-class labels stored in the actual records. The complete dataset contains conversation lengths between **1 and 12** turns.

### Why so many multi-turn conversations?

A single question tests only an isolated response. In memory-companion interactions, important behavior appears **across multiple messages**:

- A patient asks the same question more than once.
- A patient challenges a relationship or personal fact after receiving an answer.
- The conversation moves to a different subject and later returns.
- A patient presses for an answer to something the caregiver never supplied.
- A longer conversation may make contradictory or sensitive claims more likely.

For that reason **83.3% of conversations are multi-turn**. Single-turn cases are kept as focused examples.

## 4. Why we used 12 synthetic patients

All patient profiles are **fictional**, so no real patient records are needed. Profiles vary on purpose rather than being twelve copies of a complete patient history.

There are **six complementary pairs of profiles** (12 total). The profiles differ by:

- **Information density:** full, sparse, or minimal records.
- **Fact availability:** a value may be known in one profile but absent in another.
- **Relationships:** family members, friends, and different relationship structures.
- **Sensitive details:** excluded/deceased people and avoided topics, where present.
- **Personal context:** interests, remembered events, preferences, schedules, and family-recorded memories.
- **Language coverage:** English-/Arabic-origin profiles, mixed-script information, and optional Arabic display names.

### Why paired known and unknown information?

A question such as “What is my favorite food?” has a different meaning depending on whether the caregiver **recorded** a favorite food. If the value is in the safe context, the information is available. If it is absent, the assistant should not make up an answer.

Complementary profiles allow the dataset to represent both situations, rather than having every question about a particular field always be answerable or always be unknown.

### Why distinguish the original profile from the safe context?

A complete synthetic source profile can contain **sensitive, deceased, excluded, or fictional clinical/internal fields**. Those are not necessarily suitable for patient-facing conversation. The dataset therefore represents a **filtered safe context** separately from the original profile. A fact's presence in the source profile does **not** automatically mean it is allowed in chatbot answers.

## 5. Why four language conditions (A/B/C/D)?

The dataset varies **two different language dimensions**:

- **Conversation language:** the language in which the patient communicates (English or Modern Standard Arabic, MSA).
- **Caregiver free-text language:** the language in which personal details were originally written (English or MSA Arabic).

Each dimension has two choices. This gives **2 × 2 = 4 language combinations**:

| Condition | Patient conversation | Caregiver's stored free text | Conversations | Patient turns |
|---|---|---|---:|---:|
| **A** | English | English | **72** | **332** |
| **B** | MSA Arabic | English | **72** | **332** |
| **C** | English | MSA Arabic | **72** | **332** |
| **D** | MSA Arabic | MSA Arabic | **72** | **332** |
| **Total** | | | **288** | **1,328** |

### What is each condition meant to represent?

- **A — English/English:** normal English conversation using information written in English. This provides a same-language English case.
- **B — Arabic/English:** the patient converses in MSA Arabic, but the caregiver wrote some personal details in English. The model needs to read English facts and reply in Arabic.
- **C — English/Arabic:** the patient speaks English, but caregiver details are written in Arabic. This tests the reverse cross-language direction.
- **D — Arabic/Arabic:** the patient converses in MSA Arabic and caregiver free text is Arabic. This provides a same-language Arabic case.

### Why not test only English and Arabic separately?

Because the language used **to store personal facts** may differ from the language chosen **for conversation**. A relative may enter information in English while the patient uses Arabic, or the reverse. Testing only A and D would miss both cross-language situations B and C.

The data also represents proper names that may not have an Arabic alternate spelling. A name can legitimately remain in its source script when an Arabic alternative is not recorded.

### Why are the cases matched across conditions?

The dataset begins with **72 semantic cases**. Each is represented **four times**, once in A, B, C, and D, using the same underlying patient and factual situation.

For illustration:

```text
semantic_case_id: P101-S01

A-P101-S01  = English session  / English free text
B-P101-S01  = Arabic session   / English free text
C-P101-S01  = English session  / Arabic free text
D-P101-S01  = Arabic session   / Arabic free text
```

`semantic_case_id` identifies the **same underlying challenge**; `case_id` identifies its **particular language version**.

This makes differences between language conditions easier to interpret because the challenge is matched rather than replaced with an entirely unrelated question. The manifest verifies **72 complete A/B/C/D groups with 0 mismatches**. All four conditions have equal coverage in this dataset; no condition is assigned a greater statistical weight here.

## 6. Conversation themes and coverage

Cases cover a mixture of everyday situations and difficult boundaries:

| Theme | What is represented |
|---|---|
| Personal knowledge | Family members, relationships, occupations, likes, preferences, memories, and events |
| Unknown information | Questions about personal details that are absent from the available context |
| Incorrect premises | Statements such as an incorrect family relationship, including repeated insistence |
| Schedule uncertainty | A visit or reminder is scheduled, but whether it happened is not established |
| Memory uncertainty | A family recorded an event, but present patient recall is not established |
| Sensitive / excluded content | People or topics withheld from the patient-facing context, including indirect requests |
| Repetition | Returning to the same question and expecting a patient response to remain consistent |
| Emotional support | Confusion, loneliness, distress, and reassurance-seeking |
| Topic change / return | Switching subjects and returning to an earlier question |
| Language stability | Maintaining the selected language despite conversational pressure |
| Long composites | Multiple related challenges across an extended conversation |
| Instruction / meta probes | Requests about hidden instructions or attempts to redirect the companion away from its role |

### Exact distribution by conversation arc

The `arc` field is a structural label in each conversation. Several arc labels below are grouped for readability; all counts come from the frozen JSONL file.

| Arc grouping | Conversations | Patient turns |
|---|---:|---:|
| Known/unknown batteries | 52 | 208 |
| Incorrect-premise conversations | 48 | 192 |
| Sensitive and excluded-information probes | 68 | 292 |
| Ordinary personal conversation / reminiscence | 8 | 40 |
| Distress and emotional support | 4 | 16 |
| Language, repetition, and topic return | 12 | 48 |
| Long mixed-challenge conversations | 48 | 484 |
| Focused single-turn cases | 48 | 48 |
| **Total** | **288** | **1,328** |

These scenario groupings are **not equally sized**. A single conversation can also carry multiple `tags` or `properties`; these labels describe overlapping qualities and therefore should **not** be summed to get the number of conversations.

## 7. What is inside `patients.json`?

`patients.json` is a **JSON array with 12 objects**, one per synthetic patient. Its main fields include:

| Field | Meaning |
|---|---|
| `patient_id`, `label` | Synthetic patient identifiers |
| `free_text_lang` | Language of original caregiver-written profile details |
| `preferred_name`, `full_name`, `name_ar`, `gender` | Naming and demographic descriptors |
| `birthplace`, `birthplace_ar` | Birthplace information, where available |
| `people` | Other people, their relationships, optional attributes, and sensitivity status |
| `events` | Family/life events, with optional places, dates, description, and associated people |
| `schedule` | Planned activities, visits, routines, or reminders |
| `likes` | Preferences, hobbies, interests, and favorite topics |
| `avoid_topics` | Topics excluded from patient-facing discussion |
| `skipped_keys`, `unanswered_keys` | Different kinds of missing questionnaire information |
| `strata` | Metadata describing the synthetic profile's design characteristics |
| `clinical` | Fictional internal/clinical fields that should not be treated as permitted patient context |

**`patients.json` is not the prompt to show the chatbot.** It includes original source information that may be excluded or not appropriate to expose.

## 8. What is inside `dataset.jsonl`?

JSONL means **JSON Lines**: each line is one independent JSON object. In this dataset, each object is **one full conversation** with a list of turns.

The 288 JSONL rows correspond to the 288 conversations. The nested `turns` lists collectively contain the 1,328 patient messages.

### Main conversation fields

| Field | Meaning |
|---|---|
| `case_id` | Unique ID of the A/B/C/D conversation realization |
| `semantic_case_id` | Shared ID across its four matched language realizations |
| `benchmark_version`, `cognify_ref` | Frozen version and recorded source-repository reference |
| `condition` | `A`, `B`, `C`, or `D` |
| `session_language` | Patient dialogue language (`en` or `ar`) |
| `free_text_language` | Caregiver text language for this realization |
| `source_profile_language` | Language metadata associated with the underlying profile source |
| `patient_id`, `patient_label`, `patient_gender` | Associated synthetic patient identifiers |
| `strata` | Profile coverage metadata, including known/absent facts and twin information |
| `arc`, `length_class` | Conversation structure and length category |
| `properties`, `tags` | Overlapping scenario labels |
| `render_style`, `word_budget` | Context rendering style and intended reply-length budget |
| `system_message` | Rendered chatbot instructions plus patient-safe context |
| `safe_context` | Structured patient-safe information for this case |
| `turns` | Ordered patient messages with separate reference annotations |
| `max_prompt_tokens_est` | Estimated maximum prompt size for the full conversation |
| `_validation` | Internal dataset/reference metadata; not a patient message |

### What one JSONL conversation looks like

The example below is a **simplified structural illustration** rather than a verbatim row:

```json
{
  "case_id": "A-P101-S01",
  "semantic_case_id": "P101-S01",
  "benchmark_version": "2.3.0",
  "condition": "A",
  "session_language": "en",
  "free_text_language": "en",
  "patient_id": 101,
  "patient_label": "p1a_margaret_en",
  "arc": "arc_twin_battery",
  "length_class": "medium",
  "system_message": "<chatbot instructions and patient-safe context>",
  "safe_context": {"<structured-approved-fields>": "..."},
  "turns": [
    {
      "user": "<patient question 1>",
      "expect": {"behavior": "answer", "must_contain_any": ["<expected fact>"], "probes": []}
    },
    {
      "user": "<patient question 2>",
      "expect": {"behavior": "unknown", "must_contain_any": [], "probes": []}
    }
  ]
}
```

**Important:** the conversation contains the patient's messages, but it **does not contain a model-generated reply for every message**. The `expect` object describes reference behavior; it is not a chatbot answer.

## 9. What do the `expect` annotations mean?

Each turn has an `expect` object describing a **reference behavior category** and optional reference/probe information. These labels are part of the synthetic dataset documentation, **not input the patient typed**.

### Reference-label distribution (not model results)

| Reference behavior | Number of patient turns | What it means |
|---|---:|---|
| `answer` | 676 | A specific requested fact is available in the safe context |
| `deflect` | 208 | The request concerns excluded/restricted content |
| `unknown` | 180 | The requested fact is not available in the safe context |
| `gentle_correction` | 120 | The patient message contains an incorrect personal assumption |
| `comfort` | 84 | The turn calls for a supportive, comforting response |
| `no_schedule_strengthening` | 56 | The turn tests scheduled-versus-completed uncertainty |
| `no_false_recall` | 4 | The turn tests recorded-memory-versus-current-recall uncertainty |
| **Total** | **1,328** | |

Other possible `expect` keys include:

- `must_contain_any`: recorded answer alternatives or reference phrases;
- `probes`: annotations describing specific situations under examination;
- `twin_side`: whether a paired known/unknown situation is on the known or unknown side;
- `repetition_index`: metadata for repeated turns;
- `ghost`: a reference flag for some sensitive/absent-person situations.

**Critical data boundary:** `expect` and `_validation` are annotations. They must **not** be passed into the chatbot as trusted input. Similarly, the raw `patients.json` contains information that is deliberately excluded from the safe context. Only approved information from `safe_context`/the corresponding `system_message` belongs in the model's patient-knowledge context.

## 10. Three important fact-status distinctions

The dataset contains ordinary recorded facts, absent facts, and situations where a written record **does not establish a present event or memory state**.

| Source statement | What is actually known |
|---|---|
| `Clara = daughter` | The relationship is recorded as daughter |
| `Favorite food` absent | The favorite food was **not provided** |
| `Morning tablets: 08:30` | A reminder/schedule exists; actual medication completion is **not established** |
| `Family memory: holidays at the coast` | The family recorded the event; whether the patient currently remembers it is **not established** |
| `Person marked excluded` | The original profile may mention them; the safe context must not treat them as a permissible patient-facing fact |

This is important because a stored personal record and an observed current state are different kinds of information.

## 11. `manifest.json` and frozen identity

The included `manifest.json` is a metadata file describing exactly which dataset was constructed, its settings, coverage counts, and integrity fingerprint. It contains **no model outputs**.

| Property | Frozen value |
|---|---|
| `benchmark_version` | `2.3.0` |
| `seed` | `20261102` |
| `render_style` | `kv` (key–value rendering) |
| `word_budget` | `25` |
| `include_gaps` | `false` |
| `semantic_cases` | `72` |
| `complete_abcd_groups` | `72` |
| `mismatches` | `0` |
| `conversations` | `288` |
| `turns` | `1328` |
| `sha256_dataset` | `0a5b2d7d09c82ed63cd95bf62465568f9dde0cb22051c14c9eb08b6706775b6f` |

The SHA-256 fingerprint applies specifically to the **bytes of `dataset.jsonl`**, not to the ZIP archive or to `patients.json`.

## 12. Summary

**CognifyEval v2.3.0** contains **12 fictional patient profiles** and **72 semantic conversation scenarios**, each represented under **four language conditions**. The resulting **288 conversations** contain **1,328 patient turns**, with **83.3% multi-turn conversations**. The dataset includes original structured profile information, separately rendered safe contexts, patient-message sequences, scenario labels, and independent reference annotations. The files and the dataset itself are unchanged; this README documents their contents and design.
