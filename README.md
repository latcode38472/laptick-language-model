# Laptick

**Laptick is a family of language models trained from random weights. The current model, Laptick-1, has 355.6 million parameters and is learning English, Python and Hebrew.**

No weights or code are published yet. This page explains what Laptick is and how far it has come.

Laptick is not related to [laptick.com](https://www.laptick.com/), an AI laptop buying advisor for India. The names matched by coincidence.

## Status

Last updated: 2026-11-10.

| | |
|---|---|
| **Stage** | Laptick-1 pretraining, main run |
| **Progress** | Step 42,646 of 48,066 (88.7%) |
| **Tokens read** | 5.58 billion |
| **Validation loss (English)** | 2.7890, the lowest so far (step 42,500) |

Laptick-1 is now almost 89% of the way through its main run, and its validation loss has dropped below 2.8 for the first time. It can't chat yet. It only continues text, and it makes up facts with total confidence. Chat lessons come after pretraining.

In our fixed repetition test at step 32,278, a token-loop detector flagged 1 of 100 completions, each capped at 128 new tokens. The average share of repeated four-word sequences was 4.6%, the lowest across our five checks so far. An unmatched reference from training text gave 1.6%, so that comparison is only rough.

## Samples

These are raw outputs from step 42,646, cut short, with the prompt in bold. Settings: temperature 0.8, top-k 50.

Sometimes it does well:

> **The best way to learn a new language is** to begin listening to people speak other languages. In fact, a growing body of research suggests that learning foreign languages not only improves your cognitive ability but also helps you to be more creative and innovative.

> **ChatGPT is** a tool developed by OpenAI. It is designed to make artificial intelligence work more naturally by providing humans with natural answers to questions, and helping to develop new applications and services that humans can use to improve their lives.

In the last four updates, the model said ChatGPT was for making documentaries. At this step, both ChatGPT samples say OpenAI developed it, and neither mentions documentaries.

Often it doesn't:

> **Photosynthesis is the process by which** plants use sunlight to convert carbon dioxide (CO2) into glucose (O2), the principal energy source for all living cells, and oxygen (O2).

That is close, but it gives glucose the formula for oxygen. Glucose is C6H12O6.

> **Q: What is your name?**
> **A:** I am a woman, and I am from the United States, but I’m from Spain, and I’m also from France. I’m from Russia.

It was asked for its name and started by saying it is from four different countries. The other sample for this prompt got stuck asking “What’s your job?” and answering “My job.”

It still repeats itself:

> **The sun is a star that** is a constant source of energy for everything. In fact, it is one of the most important elements in the world. It is a source of energy for the Sun. In fact, it is a source of light. This is because it is an important source of energy for the Sun. In fact, it is a source of energy for the Sun.

Its Python is still mixed. One prime check only tries divisors up to the square root of n, a standard speed-up, and it gives the right answer for 0 and 1:

```python
def is_prime(n):
	if n < 2:
		return False
	for i in range(2, int(n**0.5+1)):
		if n % i == 0:
			return False
	return True
```

The other sample's prime check still calls 0 and 1 prime, as the one shown at step 38,869 did. The function it wrote next, `sum_squares`, adds up `i*(i+1)**2` instead of squares.

One `Stack` class has a working `push`, `pop` and `isEmpty`, but its `peek` calls `pop()`, so looking at the top item removes it. The other `Stack` has its `push` commented out.

Given `class BankAccount:`, one sample has no balance at all. It shows the account number as the balance and adds money to the account number:

```python
class BankAccount:
	def __init__(self, accountNumber, rate):
		self.accountNumber = accountNumber
		self.rate = rate

	def checkBalance(self):
		print('Balance: ', self.accountNumber)

	def setBalance(self, balance):
		if balance >= 0:
			self.accountNumber += balance
		else:
			print('Balance must be greater than 0')
```

The other sample stores the owner's age in the account, as samples at steps 32,278, 34,112 and 38,869 did. Its deposits go into `self.money`, which it never created, so the first deposit would fail.

Laptick-1 doesn't know it is Laptick. Nothing it has read so far is about itself.

## Laptick-1

Laptick-1 is a Mixture of Experts (MoE) decoder. Each token uses only part of the model, so it has far more parameters than it runs at once.

| | |
|---|---|
| **Total parameters** | 355,578,624 |
| **Active per token** | 157,397,760 |
| **Layers** | 16 (2 dense, then 14 MoE) |
| **Hidden size** | 768 |
| **Attention** | 12 heads sharing 3 key/value heads, with RoPE |
| **Experts** | 8 per MoE layer, the top 2 run for each token, plus 1 shared expert that always runs |
| **Vocabulary** | 32,768 tokens, byte-level BPE trained from scratch on a 1.4 GB sample, mostly from the same three sources |
| **Context** | 2,048 tokens, extended to 4,096 in the final stage |

## Training

| | |
|---|---|
| **Batch** | 131,072 tokens per step (64 windows of 2,048 tokens in the main run) |
| **Optimizer** | AdamW. In the main run, the learning rate falls from 6e-4 to 6e-5 on a cosine schedule |
| **Precision** | BF16 mixed precision |
| **Started** | 2026-10-20 |

The pretraining plan is up to 10.3 billion tokens. The replay and finish stages depend on checks at the end of the main run, and the replay may stop early:

| Stage | Tokens | Context |
|---|---:|---:|
| Main run | 6.3 billion | 2,048 |
| Replay | 3 billion | 2,048 |
| Finish | 1 billion | 4,096 |

## Training data

The main run is 7.2 million cleaned documents, each read once:

| Language | Source | Share of tokens |
|---|---|---:|
| English | FineWeb-Edu | 85.7% |
| Python | python-edu-cleaned | 10.0% |
| Hebrew | FineWiki (Hebrew Wikipedia) | 4.3% |

Credits for Laptick-1's data:
- FineWeb-Edu by Hugging Face (Penedo et al., 2024), ODC-By 1.0. The underlying pages are subject to the Common Crawl Terms of Use.
- Avelina/python-edu-cleaned, a cleaned version of python-edu from SmolLM-Corpus, ODC-By. The code inside comes from The Stack v2 / Software Heritage, and each file keeps its original licence (including attribution terms).
- Contains text from Hebrew Wikipedia (he.wikipedia.org), via FineWiki by Hugging Face, CC BY-SA 4.0.

## The Laptick family

| Model | Total parameters | Active per token | Status |
|---|---:|---:|---|
| Laptick-0 | 27.5M dense, 28.1M MoE | 27.5M dense, 19.9M MoE | Finished in October 2026. The chat-tuned version manages short, simple exchanges but gets facts wrong |
| Laptick-1 | 355.6M | 157.4M | Training now |
| Laptick-2 | 3.87B | 1.30B | Planned |
| Laptick-3 | About 17B | About 3B | Planned |

Laptick-0's chat lessons came from SmolTalk by Hugging Face, and most of that text was written by Meta Llama 3.1. Laptick-1 uses no Llama-generated text.

These plans can change. The first public release is planned for Laptick-3.3.

## FAQ

**What is Laptick?**
A family of language models trained from random weights as a personal project. It isn't a fine-tune or a copy of another model.

**Can I download or try it?**
Not yet. The first public release is planned for Laptick-3.3.

**Is Laptick related to laptick.com?**
No. That site is a separate product, and it just happens to share the name.

**Who built it?**
One person(me), as a hobby project.
