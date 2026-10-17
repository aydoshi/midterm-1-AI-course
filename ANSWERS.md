# Midterm 1: answers

GitHub username: aydoshi
Session code: KETTLE-62
Fingerprint: `c5fc535d`

> Fill in the three lines above from `my_data/DATA.md`. Do not rename headings or table columns: a script reads the numbers.
> Write only in the empty cells and lines. Percentages as 0.78 or 78 %, money as plain numbers.

## AI-use declaration

I used Claude and ChatGPT (basic models) as AI assistants while working on this midterm.

I primarily used Claude for navigation and technical guidance, including instructions for working with GitHub and understanding theoretical material related to artificial intelligence. It helped me understand concepts and find my way through the course workflow.

I primarily used ChatGPT for step-by-step guidance on completing the midterm. I asked it to clarify what each question required, what type of answer was expected, and what steps to take next. I also used it to work through numerical calculations and understand the relevant formulas, including retrieval metrics, Wilson confidence intervals, annual model costs, document extraction accuracy, token ratios, sampling probabilities, and training-token ratios. ChatGPT helped me interpret the results and organize them into concise explanations and recommendations.

I used these tools to support my understanding and progress through the assignment.

## Part 1: Retrieval and cost decision

### 1a. Metrics (all 60 questions)

| config | recall@1 | recall@3 | recall@5 | MRR@10 |
|---|---|---|---|---|
| A | 0.667 | 0.800 | 0.883 | 0.743 |
| B | 0.250 | 0.467 | 0.717 | 0.391 |

### 1b. Recall and 95 % Wilson interval of each candidate (recall at its own k)

| candidate | recall@k | lower | upper |
|---|---|---|---|
| A, k=3 | 0.800 | 0.682 | 0.882 |
| A, k=5 | 0.883 | 0.778 | 0.942 |
| B, k=3 | 0.467 | 0.346 | 0.591 |
| B, k=5 | 0.717 | 0.592 | 0.815 |

### 1c. Annual model cost in USD

| candidate | annual cost |
|---|---|
| A, k=3 | 23471 |
| A, k=5 | 31651 |
| B, k=3 | 14459 |
| B, k=5 | 16633 |

### 1d. Decision (at most 150 words)

Chosen candidate: A, k=3

I choose A with k=3. Its annual cost of $23471, which is less $27600. A with k=5 annual cost of $31651 exceeds it. A with k=3 recall is 0.80 (0.68–0.88), clearly above B with k=3 (0.47, 0.35–0.59): the intervals do not overlap. Against B with k=5 (0.72, 0.59–0.82) the intervals overlap, so no difference is shown. B with k=5 is cheaper $16633, but I prefer A with k=3 because the budget allows it and its recall estimate is higher. Recall of A with k=3 by language: EN 0.90 (18/20), RU 0.85 (17/20), KK 0.65 (13/20). Kazakh-speaking customers are served worst, and with only 20 questions that estimate is wide (0.43–0.82).

### 1e. Next measurement (at most 60 words)

I would add at least 100 more Kazakh test questions and re-measure recall for A, k=3 and B, k=5. This would narrow the intervals enough to show whether A, k=3 truly beats B, k=5.

## Part 2: A document pipeline you can defend

### 2a. Numbers

| pipeline | overall accuracy | weakest cell (language, condition) | accuracy there | lower | upper | annual cost |
|---|---|---|---|---|---|---|
| H | 0.889 | KK, clean | 0.800 | 0.627 | 0.905 | 11899 |
| L | 0.850 | KK, degraded | 0.800 | 0.627 | 0.905 | 14400 |

Personal-data pages per year sent abroad under H: 438000

### 2b. Memo (at most 300 words)

I recommend Pipeline L as the default for processing invoices containing personal data.

Pipeline H has higher overall accuracy (88.89% vs 85.00% for L), but the 95% Wilson intervals overlap (83.5–92.7% vs 79.1–89.5%), so the difference is not shown. Its least accurate cell, clean invoices in Kazakh, is 80.00% accurate; Pipeline L's weakest cell, degraded Kazakh-language invoices, is also 80.00%. Both have the same 95% Wilson interval of 62.7%–90.5%, a high degree of uncertainty since there are only 30 test invoices per cell.

Pipeline H costs $11899 a year and L $14400, so L costs an extra $2501 per year, about 21% more. In return, documents are processed on a server in Kazakhstan instead of a hosted service abroad. This matters because about 438000 pages a year carry a person's name and ИИН.

Three questions about the scans. Where does a scan go? Under H, to a data centre abroad; under L, to our own server in Kazakhstan. Who stores it? Under H, the vendor, whose storage and subcontractors we must still find out; under L, the bank. For how long? Under H, unknown until the contract says; under L, the period we set.

Verifiable safeguards for L: a written retention period, checked quarterly by random deletion tests; an access list and audit log, reviewed quarterly; human review of Kazakh and low-confidence scans, with monthly accuracy per language and condition.

H could serve documents without personal data if the privacy and compliance teams approve external processing.

Remaining risk: about one in five Kazakh invoices may still need correction, and a server we run ourselves must be secured by us.

## Part 3: Explain your own numbers

### Q1

Number: 2.40
Why (at most 40 words): Output tokens cost five times more than input tokens. Kazakh needs 2.62 more input tokens but only 1.94 more output tokens, so the heavily weighted output grows less. The bill ratio 2.40 falls below the token ratio 2.56.

### Q2

Number: 395
Why (at most 40 words): Yes. The sentence ends before token 415, inside the 512-token window, so it is embedded and can match. Only the last 14 tokens are cut, silently. Truncation raises no error.

### Q3

Number: 59.3
Why (at most 40 words): Temperature rescales all scores before softmax, making the whole distribution sharper. Top-p keeps the original probabilities. And drops the unlikely tail once cumulative prob reaches 0.9, then renormalises the remaining tokens.

### Q4

Number: 13
Why (at most 40 words): Public leaderboard prefers X. It rewards every correct answer and never penalises a wrong one, so a model learns to always guess instead of saying 'I don't know', which produces hallucinations.

### Q5

Number: 7.85
Why (at most 40 words): Over-trained. It sees about 7.85 times more tokens per parameter than compute-optimal. Chinchilla said models such as GPT-3 were too large for their data, hence under-trained, and that parameters and tokens should grow together.
