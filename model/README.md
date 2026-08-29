# Trained PFU predictor

The PFU predictor used in the paper is a DeBERTa-v3-large cross-encoder fine-tuned on the matched
condition (topic ⊕ student message ⊕ tutor reply) of the StudyChat binary subset, with five-fold
cross-validation grouped by student (seed 42). Given a student's message and a tutor reply, it
outputs the probability that the student's next turn is productive.

The checkpoint (~1.7 GB) is too large for the repository tree and is attached to the
**GitHub Release** of this repository as:

```
pfu_predictor_deberta-v3-large.tar.gz
```

The archive contains the `safetensors` weights, tokenizer files, and a `meta.json` with the
training configuration. Load with:

```python
from transformers import AutoModelForSequenceClassification, AutoTokenizer

tok = AutoTokenizer.from_pretrained("verifier")
model = AutoModelForSequenceClassification.from_pretrained("verifier")
text = f"TOPIC: {topic}\nSTUDENT: {student_message}\nTUTOR REPLY: {tutor_reply}"
```
