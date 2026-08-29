# Productive-Follow-Up

Annotated datasets, annotation guidelines, and the trained predictor for the paper:

> **Productive Follow-Up: A Behavior-Grounded Signal for Evaluating LLM Tutor Feedback.**
> Dong Li, Zhi Liu, Qilei Li, Zhifeng Wang, Jianwen Sun. *Smart Learning Environments* (under review).

PFU is a behavior-grounded evaluation signal for LLM tutor feedback, read from the student's turn immediately after a tutor reply: whether the student keeps the work in hand (*productive*) or turns it over to the tutor (*dependency*).

## Contents

```
annotation_guide.md                     # PFU annotation guidelines
data/
  studychat_triples_labeled.parquet     # 14,637 StudyChat triples with PFU labels
  studychat_rubric_labels.parquet       # eight-dimension rubric profile per tutor reply
  comta_triples_labeled.parquet         # 917 CoMTA triples with PFU labels
  comta_rubric_labels.parquet           # rubric profiles for CoMTA replies
  gold/
    studychat_pilot_100.csv             # double-annotated, adjudicated human gold subset
    comta_pilot_120.csv                 # double-annotated human pilot (stratified)
model/                                  # trained PFU predictor (see below)
```

## Data

### `studychat_triples_labeled.parquet` (14,637 rows)

Each row is one dialogue triple from the StudyChat corpus (McNichols et al., 2026): the student's message, the tutor's reply, and the student's next turn, with the PFU label of the next turn.

| column | description |
|---|---|
| `triple_id` | unique triple identifier |
| `prev_user`, `assistant`, `next_user` | student message, tutor reply, student next turn |
| `chatId`, `userId`, `topic`, `semester`, `turn_in_chat`, `chat_total`, `history_len` | dialogue metadata |
| `llm_label` | PFU label (`productive` / `dependency` / `ambiguous`) — the label used in all analyses |
| `llm_confidence`, `llm_reason` | annotator confidence and rationale |
| `heuristic_label` | rule-based pre-screen only; **not** used in any analysis |

The binary subset used for prediction in the paper (13,750 triples) drops `ambiguous`.

> **Redaction note.** Credential strings that appeared in the raw dialogues (e.g., access tokens
> students pasted into the chat) are replaced with `[REDACTED]` placeholders in the released
> copies. Labels and all other text are unchanged.

### `comta_triples_labeled.parquet` (917 rows)

Triples extracted from the CoMTA mathematics tutoring dialogues (Miller & DiCerbo, 2024), with the same label scheme (`label`, `confidence`, `reason`) plus `math_level` and dialogue identifiers.

### Rubric labels

Every tutor reply in both corpora is scored on the eight-dimension pedagogical taxonomy of Maurya et al. (2025): mistake identification, mistake location, revealing the answer, guidance, actionability, coherence, tone, humanness. These profiles define the rubric-based conditions and the dissociation analysis in the paper.

### Human gold subsets (`data/gold/`)

- `studychat_pilot_100.csv`: 100 StudyChat triples independently labeled by two annotators and adjudicated; this pilot fixed the annotation guidelines.
- `comta_pilot_120.csv`: 120 CoMTA triples, double-annotated, stratified so the rare dependency class is represented.

## Annotation guidelines

`annotation_guide.md` contains the guidelines that both human annotators and the LLM annotator followed, including the construct definition, the decision rules for productive vs. dependency vs. ambiguous, and worked examples.

## Trained predictor

The PFU predictor used in the paper (DeBERTa-v3-large cross-encoder, trained on the matched condition with five-fold student-grouped cross-validation, seed 42) is attached to the GitHub Release of this repository as `pfu_predictor_deberta-v3-large.tar.gz`; see `model/README.md` for loading instructions. Given the student's message and a tutor reply, it outputs the probability that the student's next turn is productive.

## Licensing and upstream data

- StudyChat-derived data are released under the terms of the original StudyChat corpus (McNichols et al., 2026); please cite the original dataset when using the triples.
- CoMTA-derived data are released with permission; please cite Miller & DiCerbo (2024).

## Citation

```bibtex
[TODO: BibTeX after publication]
```
