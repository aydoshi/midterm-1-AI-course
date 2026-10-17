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

I choose A with k=3. Its annual cost of $23471. A with k=5 annual cost of $31651 exceeds it. A with k=3 recall is 0.80 (0.68–0.88), clearly above B with k=3 (0.47, 0.35–0.59): the intervals do not overlap. Against B with k=5 (0.72, 0.59–0.82) the intervals overlap, so no difference is shown. B with k=5 is cheaper $16633, but I prefer A with k=3 because the budget allows it and its recall estimate is higher. Recall of A with k=3 by language: EN 0.90 (18/20), RU 0.85 (17/20), KK 0.65 (13/20). Kazakh-speaking customers are served worst, and with only 20 questions that estimate is wide (0.43–0.82).

### 1e. Next measurement (at most 60 words)

Should to add 100 more Kazakh test questions. And remeasure recall for A, k=3 and B, k=5. This would narrow the intervals enough to show whether A, k=3 truly beats B, k=5.

## Part 2: A document pipeline you can defend

### 2a. Numbers

| pipeline | overall accuracy | weakest cell (language, condition) | accuracy there | lower | upper | annual cost |
|---|---|---|---|---|---|---|
| H | 0.889 | KK, clean | 0.800 | 0.627 | 0.905 | 11899 |
| L | 0.850 | KK, degraded | 0.800 | 0.627 | 0.905 | 14400 |

Personal-data pages per year sent abroad under H: 5000 * 365 0.24 = 438000

### 2b. Memo (at most 300 words)

I recommend Pipeline L as the default for processing invoices containing personal data.

Pipeline H has better overall accuracy: 88.89% compared to 85.00% for Pipeline L. But its least accurate cell, clean invoices in Kazakh, is 80.00% accurate. Pipeline L also has weakest-cell accuracy of 80.00% for degraded Kazakh-language invoices. The two weakest cells have the same 95% Wilson interval of 62.69%–90.50%. This indicates a high degree of uncertainty since there are only 30 test invoices per cell.
Pipeline H costs $11,899 a year, and pipeline L $14,400. So, if you pick L, it costs an extra $2,501 per year, or about 21% more than H. This enables documents to be processed on a server in Kazakhstan, rather than sending them to a hosted service abroad. Why does it matter ? There are about 438 , 000 pages a year that have a person 's name and ИИH on it .

Where local processing is required to reduce cross-border data exposure, the bank should use Pipeline L, subject to appropriate security controls. Pipeline H would be suitable for documents containing no personal data, if the bank’s privacy and compliance teams approve the plans for external processing.


## Part 3: Explain your own numbers

### Q1
The same support request, once in English and once in Kazakh, through Model M ($2.5 per million input tokens, $12.5 per million output tokens):

| | input tokens | output tokens |
|---|---|---|
| EN | 1,314 | 126 |
| KK | 3,442 | 244 |

Compute the token ratio (total tokens KK ÷ EN) and the bill ratio (cost KK ÷ cost EN). **Number:** the bill ratio (2 decimals). **Why** (at most 40 words): why is the bill ratio not equal to the token ratio?

Number: 2.40
Why (at most 40 words): Output tokens cost five times more than input tokens. Kazakh needs 2.62 more input tokens but only 1.94 more output tokens, so the heavily weighted output grows less. The bill ratio 2.40 falls below the token ratio 2.56.

### Q2
A teammate sets the chunk size to **512 tokens, counted with cl100k**. The retrieval model (e5-small) reads at most 512 of *its own* tokens. Assume e5 tokens grow in proportion to cl100k tokens at the rate measured in Lecture 4.1 (a chunker told 256 cl100k tokens produced chunks of 263 / 128 / 76 e5 tokens for EN / RU / KK), and that every chunk is exactly the full size.

In an English chunk, the sentence that contains the answer starts 75 % of the way into the chunk and is shorter than 20 e5 tokens. **Number:** the position of the start of that sentence, in e5 tokens (nearest integer). **Why** (at most 40 words): can a question that matches only that sentence retrieve this chunk, and would you see an error?

Number: 395
Why (at most 40 words): Yes. The sentence ends before token 415, inside the 512-token window, so it is embedded and can match. Only the last 14 tokens are cut, silently. Truncation raises no error.

### Q3
A language model gives these next-token scores for the prompt "The branch is closed on":

| token | score |
|---|---|
| Monday | 4.9 |
| Friday | 3.9 |
| Sunday | 3.2 |
| holiday | 2.9 |
| Easter | 2.4 |

Use temperature T = 1, then apply top-p = 0.9. **Number:** the probability of the top token after top-p and renormalisation, in percent (1 decimal). **Why** (at most 40 words): how does top-p differ from lowering the temperature?

Number: 59.3
Why (at most 40 words): Temperature rescales all scores before softmax, making the whole distribution sharper. Top-p keeps the original probabilities. And drops the unlikely tail once cumulative prob reaches 0.9, then renormalises the remaining tokens.

### Q4
Two models answer the same 100 factual questions (1 point per correct answer on the public leaderboard):

| | correct | wrong | "I don't know" |
|---|---|---|---|
| Model X | 74 | 26 | 0 |
| Model Y | 69 | 8 | 23 |

Now score them again with a penalty: +1 per correct answer, −1 per wrong answer, 0 for "I don't know". **Number:** Y's penalised score minus X's (negative if X is ahead). **Why** (at most 40 words): which model does the public leaderboard prefer, and what does that teach a model to do?

Number: 13
Why (at most 40 words): Public leaderboard prefers X. It rewards every correct answer and never penalises a wrong one, so a model learns to always guess instead of saying 'I don't know', which produces hallucinations.

### Q5
A lab trains a model with **70 billion parameters on 10.99 trillion tokens**. Chinchilla (Lecture 1.1) trained 70 billion parameters on 1.4 trillion tokens, which the paper presents as the compute-optimal ratio. **Number:** this model's tokens per parameter divided by Chinchilla's tokens per parameter (2 decimals). **Why** (at most 40 words): is the model under- or over-trained by that criterion, and what did Chinchilla say about models such as GPT-3?

Number: 7.85
Why (at most 40 words): Over-trained. It sees about 7.85 times more tokens per parameter than compute-optimal. Chinchilla said models such as GPT-3 were too large for their data, hence under-trained, and that parameters and tokens should grow together.
