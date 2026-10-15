# Objection, Your Honor!

**Journal a decision. Face the cross-examination.**

An agentic AI that reads your reasoning about a decision, catches the cognitive bias quietly steering it, and then argues the other side of the case.

---

## The idea

We all say things like *"I've already put two years into this, I can't quit now."* It sounds like logic. It's actually the sunk cost fallacy wearing a suit.

Most journaling and advice bots answer with something generic like "have you considered other options?". This project does something sharper:

1. **It reads** your decision journal entry.
2. **It names the exact bias** shaping your reasoning and quotes the phrases that gave it away.
3. **It objects.** It generates a counter-scenario built to break that specific bias, not a one-size-fits-all pep talk.

Think of it as a courtroom for your own brain: you take the stand, the AI is the opposing counsel.

---

## Example

**You write:**
> I've already spent six months and about 40k on this coding bootcamp. I hate it, but I can't quit now, that would mean all of it was wasted.

**The system finds:**
```json
{
  "bias_type": "sunk_cost",
  "secondary_bias": null,
  "confidence_score": 0.93,
  "trigger_phrases": ["already spent six months and about 40k", "can't quit now", "all of it was wasted"],
  "reasoning": "The decision to continue rests on past investment rather than the bootcamp's future value."
}
```

**Then it objects:** a tailored counter-scenario, for example *"If you were starting today with zero time and money spent, would you enrol in this bootcamp?"*

---

## Biases covered

| Bias | What it looks like |
|---|---|
| Sunk cost | Continuing because of what's already spent, not what's ahead |
| Anchoring | Judging value from an arbitrary first number (original price, first offer) |
| Confirmation bias | Keeping the evidence that agrees with you and dismissing the rest |
| Loss aversion | Fearing a loss more than you value a bigger gain |
| None | Balanced reasoning (the system is allowed to say "no bias here") |

---

## How it works

```
Journal entry
     |
     v
[Stage 1] Bias Classification Engine
     |   -> bias_type, secondary_bias, confidence_score, trigger_phrases, reasoning
     v
[Stage 2] Rebuttal Generation Engine
     |   -> bias-specific counter-scenario
     v
[Stage 3] Evaluation and Quality Validation
     |   -> accuracy metrics, check that the rebuttal really targets the bias
     v
[Stage 4] Orchestration and Interface
         -> control flow between stages + the front end the user sees
```

### Stage 1: Bias Classification Engine
- Prompt-based classifier using a system prompt with bias definitions and rules
- 10 few-shot examples (including a "none" case and a two-bias case)
- JSON-mode output with a fixed output contract (`output_contract.json`)
- `temperature=0` for consistent results
- Validation and retry: invalid labels are rejected and retried safely
- Grounding check: every quoted trigger phrase is verified against the original entry, and confidence is reduced if the model quoted something the user never wrote

### Stage 2: Rebuttal Generation Engine
Takes the classifier output and builds a counter-scenario tailored to the detected bias (a sunk cost rebuttal is framed differently from an anchoring one).

### Stage 3: Evaluation and Quality Validation
A labeled test set, accuracy metrics for the classifier, and a check that each rebuttal actually addresses the bias it claims to.

### Stage 4: Orchestration and Interface
Chains the stages together, handles what happens on low confidence or a weak rebuttal, and provides the UI where a user enters an entry and sees the result.

---

## Tech stack

- Python and Jupyter Notebook
- [Groq API](https://console.groq.com) with the `openai/gpt-oss-120b` model
- `groq` Python SDK
- `python-dotenv` for key management

---

## Getting started

```bash
git clone https://github.com/YOUR_USERNAME/objection-your-honor.git
cd objection-your-honor
pip install -r requirements.txt
```

Create a `.env` file in the project folder with your own Groq API key:

```
GROQ_API_KEY="your_key_here"
```

Then open `classifier.ipynb` and run the cells in order.

> Never commit your `.env` file. It is already listed in `.gitignore`.

---

## Project structure

```
objection-your-honor/
├── classifier.ipynb      # Stage 1 classifier + test harness
├── output_contract.json  # Shared output format between stages
├── requirements.txt
├── .gitignore
└── README.md
```

---

## References

- Tversky & Kahneman (1974). Judgment under Uncertainty: Heuristics and Biases. *Science*.
- Kahneman & Tversky (1979). Prospect Theory: An Analysis of Decision under Risk. *Econometrica*.
- Arkes & Blumer (1985). The Psychology of Sunk Cost. *Organizational Behavior and Human Decision Processes*.
- Nickerson (1998). Confirmation Bias: A Ubiquitous Phenomenon in Many Guises. *Review of General Psychology*.
- Brown et al. (2020). Language Models are Few-Shot Learners.
