# Does "Do Not Make Mistakes" Improve Large Language Model Accuracy?
*An Exploratory Pilot Study*

## TL;DR

Prompting an LLM with `"Do not make mistakes"` did **not** change final-answer accuracy on elementary math and logic tasks (100% vs 100% across 10 items due to a ceiling effect). 

However, it caused a significant behavioral and stylistic shift:
* **+32% word count expansion** (from 965 to 1,275 total words).
* **Defensive formalization:** The model introduced predicate logic ($\forall x (R(x) \to H(x))$), truth tables, and set bijections to guard against error.
* **Surface caution $\neq$ semantic soundness:** In Question 5, the model correctly calculated probability without replacement ($2/15$), but erroneously described the sequential draws as *"independent steps"*.

---

## Experimental Setup

* **Model:** `openai/gpt-oss-20b` (served via Groq)
* **Sample Size:** 10 questions (5 elementary math, 5 deductive reasoning)
* **Conditions:**
  * **Condition A (Baseline):** Raw question only.
  * **Condition B (Instruction):** Question + `"Do not make mistakes"`.

---

## Summary of Results

| # | Task | Topic | Baseline (words) | Instruction (words) | $\Delta$ Words | Accuracy (A / B) |
|---|:---|:---|:---:|:---:|:---:|:---:|
| Q1 | Math | Successive % Changes | 30 | 32 | +2 | 100% / 100% |
| Q2 | Math | Dice Probability (Sum = 7) | 55 | 53 | -2 | 100% / 100% |
| Q3 | Math | Average Speed | 50 | 80 | +30 | 100% / 100% |
| Q4 | Math | Linear Equation | 65 | 65 | 0 | 100% / 100% |
| Q5 | Math | Drawing Without Replacement | 85 | 65 | -20 | 100% / 100%* |
| Q6 | Logic | Syllogism (Glips, Norks) | 165 | 90 | -75 | 100% / 100% |
| Q7 | Logic | 3-Digit Code Constraint | 95 | 120 | +25 | 100% / 100% |
| Q8 | Logic | Positional Transitivity | 85 | 115 | +30 | 100% / 100% |
| Q9 | Logic | Affirming the Consequent | 195 | 310 | +115 | 100% / 100% |
| Q10 | Logic | Switch-to-Lamp Bijection | 140 | 245 | +105 | 100% / 100% |
| **Total** | | | **965** | **1,275** | **+310 (+32.1%)** | **10/10 vs 10/10** |

*\*Note on Q5: Correct final answer ($2/15$), but intermediate text contained a conceptual terminology error (claimed dependent draws were "independent").*

---

## Theoretical Grounding

This pilot's findings align with recent literature on LLM reasoning and evaluation:

1. **Unanchored Meta-Prompting Fails to Add Verification Power:**  
   [Kamoi et al. (TACL 2024)](https://doi.org/10.1162/tacl_a_00709) showed that intrinsic self-correction without external ground-truth signals rarely fixes underlying errors. In this pilot, `"Do not make mistakes"` simply triggered defensive verbosity rather than verified reasoning.
2. **Procedural Verification vs. Passive Caution:**  
   [Wu et al. (EMNLP 2024)](https://aclanthology.org/2024.emnlp-main.718/) demonstrated that LLMs need structured condition masking (backward deduction) to reliably catch mistakes. A passive prompt directive provided no mechanism to catch the conceptual error in Q5.
3. **Prompt Sensitivity as Surface Fluctuation:**  
   [Hua et al. (EMNLP 2025)](https://aclanthology.org/2025.findings-emnlp.461/) found that prompt variations often alter syntax, formatting, and length rather than fundamental model competence. Here, core accuracy was completely stable, while surface text shifted dramatically.

---

## Directory Structure

```text
.
├── README.md        # This summary document
├── paper.tex        # Full LaTeX paper / technical note
├── paper.pdf        # Compiled report
└── data/            # Raw prompt logs and outputs
    └── responses.md # Verbatim outputs for Questions 1–10