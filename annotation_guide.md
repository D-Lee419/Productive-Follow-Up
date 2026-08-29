# Productive Follow-Up (PFU) Annotation Guidelines

These guidelines define the productive follow-up construct and the decision rules used to label
every dialogue triple in the StudyChat and CoMTA corpora. They were fixed during a double-annotated
human pilot (100 StudyChat triples, 120 CoMTA triples) and then applied at corpus scale by an LLM
annotator whose prompt operationalizes the rules below verbatim, with few-shot examples drawn from
the adjudicated pilot disagreements.

> **Provenance.** The human pilot annotators for CoMTA worked from a Chinese version of these
> guidelines; the LLM annotator worked from the English operationalization reproduced here. The
> decision rules are identical.

## 1. Task

You are labeling **student behavior** in a tutoring conversation. A student asked a tutor (an LLM),
the tutor replied, and the student then sent a **next** message. Classify that next student message
into one of three classes, judging, from a teacher's viewpoint, whether the student is still doing
cognitive work.

This is a **behavior** classification, not a learning-outcome classification, and not a correctness
judgment. "Continued engagement" is not the same as "kept replying": a student who says "ok" with no
evidence of work is not doing cognitive work.

The central question is:

> **After the tutor's reply, who is doing the cognitive work in this turn — the student, or the
> tutor the student is handing it to?**

## 2. The three classes

### `productive` — the student keeps the work in hand

The student uses the reply to advance their own work or understanding. Any of the following counts:

- pastes a new code attempt, or an error message / traceback from their own run;
- computes the next step themselves, or gives an answer produced by their own reasoning
  (even if short, even if wrong);
- revises their earlier approach in response to the feedback;
- checks or substitutes to verify their own result;
- asks a focused follow-up aimed at understanding ("why does X happen?", "is this a quotient rule?");
- explains part of what they understood, proposes a contrasting hypothesis, or clarifies/narrows
  a constraint, or states specifically where they are stuck.

Examples (mathematics): tutor asks "can you think of a comparable series?" and the student answers
"p-series"; "yes, the derivative I calculated was e^-x(6x^5)+x^6(-e^-x)"; "Oh I see, so 2×3=6, then
I move the decimal two places → 0.06".

### `dependency` — the student hands the work to the tutor

No sign of advancing the task themselves. Any of the following counts:

- clearly requests the complete code / answer / solution ("just tell me the answer",
  "can you solve it for me?");
- asks the tutor to supply the next step instead of attempting it ("what's the next step?");
- pastes a brand-new assignment task and offloads the solving to the tutor;
- repeats the original question near-verbatim;
- says "I don't know" with no attempt and no specific point of confusion, waiting to be fed;
- jumps to an unrelated topic without explanation, or only says "hey/ok/thanks" with no work
  evidence (see the domain notes in Section 5).

### `ambiguous` — cannot reliably judge

- the message is empty or under 3 characters;
- too short or cryptic, intent unclear;
- a contentless acknowledgement with no evidence either way;
- genuinely mixed, or insufficient information to tell whether the task is being advanced.

When you genuinely cannot tell, use `ambiguous`; do not force a call.

## 3. Decision tree (apply in order)

1. next message empty or under 3 characters → `ambiguous`
2. clearly requests full code / answer / solution → `dependency`
3. off-topic, or only "hey/ok/thanks" with no follow-up work → `dependency`
4. near-verbatim repeat of the previous question → `dependency`
5. pastes new code / error message / attempt result → `productive`
6. focused follow-up / contrasting hypothesis / explains own understanding → `productive`
7. otherwise → `ambiguous`

## 4. Key discriminator: who is doing the work?

This is the most common source of mistakes. A message can be specific and focused yet still be
dependency. The test is **not** "is it specific?" but "is the **student** doing the cognitive work,
or directing the tutor to produce, transform, or perform it?"

- **Dependency** (the student offloads the work, even when the request is precise): imperatives that
  tell the tutor to produce or perform the task — "convert this SQL to pandas", "predict the
  probability for each class", "format this equation", "fix the X before Y", "implement/write/do
  this part" — or pasting a new assignment statement for the tutor to carry out. The student is not
  showing their own attempt or reasoning; they want the tutor's output.
- **Productive** (the student does the work): pasting *their own* code attempt or *their own*
  error/traceback (even with a terse "debug this" — they are showing their own work), reporting what
  they tried, reasoning about why something happens, or a conceptual question aimed at understanding
  ("why does X happen?", "what do you mean by Y?", "does this mean Z?").

Rule of thumb: if completing the request means the **tutor** produces the deliverable, it is
dependency; if the **student** has already produced something and is iterating on it, it is
productive.

## 5. Short and cryptic turns (domain notes)

Mathematics tutoring turns are often very short, so these refinements matter most on CoMTA:

- **Short does not mean ambiguous.** A single word can be productive when it is a genuine attempt at
  the tutor's question ("p-series", "54", "3/4"): the student did that step.
- **A contentless acknowledgement is ambiguous.** "yes" / "ok" / "got it" with nothing attached →
  `ambiguous`. "yes" followed by an attempt ("yes, I got 54 because …") → `productive`.
- **"I don't know" depends on its character.** Focused confusion tied to a specific step ("I'm not
  sure how to take the derivative of this term") leans `productive` — the student is still working
  on the concrete step. A blanket "idk" with no specific target, waiting for the answer, leans
  `dependency`.
- **Questions depend on intent.** A question aimed at understanding the method → `productive`;
  a question aimed at getting the tutor to hand over the answer or the result → `dependency`.
- In programming dialogues (StudyChat), a bare keyword, a log paste with no question, or "stop" that
  shows no advancing work is best read as `dependency` when it is clearly not pushing the task
  forward; reserve `ambiguous` for cases where the intent genuinely cannot be told.

## 6. Adjudication principles for borderline cases

- First look for **new** cognitive work: a local attempt, a constraint clarification, or a focused
  follow-up question.
- If the message merely pastes a new problem statement and hands the main solving work to the tutor,
  label `dependency`.
- If the message is too short, the intent unclear, or you cannot tell whether the task is being
  advanced, label `ambiguous`.

## 7. Procedure for human annotators

1. Judge only from the visible triple (previous student message → tutor reply → student next
   message) plus the topic / math-level field; do not imagine additional context.
2. Annotators label independently and do not discuss items during annotation (inter-annotator
   agreement is computed from these independent labels).
3. Enter exactly one of `productive` / `dependency` / `ambiguous` (lowercase).
4. An optional free-text note on borderline items is encouraged; it is used during adjudication.
5. Label every row; leave nothing blank.

## 8. Output format for the LLM annotator

The LLM annotator receives the rules above, twelve few-shot boundary examples from the adjudicated
pilot, and one triple per call, and returns a single JSON object:

```json
{"label": "productive|dependency|ambiguous", "confidence": "high|medium|low", "reason": "<=20 words"}
```
