# Laptick

**Laptick is a family of language models trained from random weights. The current model, Laptick-1, has 355.6 million parameters and is learning English, Python and Hebrew.**

No weights or code are published yet. This page explains what Laptick is and how far it has come.

Laptick is not related to [laptick.com](https://www.laptick.com/), an AI laptop buying advisor for India. The names matched by coincidence.

## Status

Last updated: 2026-11-12.

| | |
|---|---|
| **Stage** | Laptick-1 pretraining, main run |
| **Progress** | Step 44,770 of 48,066 (93.1%) |
| **Tokens read** | 5.86 billion |
| **Validation loss (English)** | 2.7805, the lowest so far (step 44,500) |

Laptick-1 is now over 93% of the way through its main run, and its validation loss is still falling: every one of the last ten checks set a new low. It can't chat yet. It only continues text, and it makes up facts with total confidence. Chat lessons come after pretraining.

In our fixed repetition test at step 32,278, a token-loop detector flagged 1 of 100 completions, each capped at 128 new tokens. The average share of repeated four-word sequences was 4.6%, the lowest across our five checks so far. An unmatched reference from training text gave 1.6%, so that comparison is only rough.

## Samples

These are raw outputs from step 44,770, cut short, with the prompt in bold. Settings: temperature 0.8, top-k 50.

Sometimes it does well:

> **The best way to learn a new language is** to do it right. In this video, we'll show you a few tips to get you started. This video gives you a great overview of the language and will take you through the process of learning how to speak and write the language.

It mixes real facts with made-up ones:

> **ChatGPT is** a Chatbot created by OpenAI. It was developed in 2016 in response to a question from AI. ChatGPT is a great language model that can improve the effectiveness of humans by talking to humans.

OpenAI is right, and both ChatGPT samples have now said so for two updates in a row. The year is made up: ChatGPT came out in 2022. The other sample said 2013.

> **Photosynthesis is the process by which** plants convert light energy into chemical energy and this conversion process is controlled by an enzyme called chlorophyll.

The first half is right, but chlorophyll is a pigment that absorbs light, not an enzyme. The other sample said that plants and animals both do it.

Sometimes it loses the thread:

> **Q: What is your name?**\
> **A:** What’s your house?\
> Q: Where is your dad?\
> A: My parents are both in the US.\
> Q: What is your mother’s name?\
> A: My mom works at a big company.\
> Q: What’s your name?\
> Q: What’s your name?\
> A: It was “R.V.”\
> Q: What does your name mean?\
> A: It is a nickname for your name.

It answered the question with another question and then kept interviewing itself. The other sample opened with “I am a woman, and I am from the United States”, the same words as the sample shown at step 42,646, and gave no name at all.

It still repeats itself:

> **The sun is a star that** is a hot star. In the center of the Sun, it is called the "corona". The corona, which is the corona, is the outer outer covering of the Sun. The corona is made of the Sun's visible spectrum and colors. The solar corona is the corona that is seen by most telescopes.

That is “corona” six times in five sentences. It also puts the corona in the center of the Sun and then calls it the outer covering. The second claim is right: the corona is the Sun's outer atmosphere.

Its Python is still mixed. Given `class BankAccount:`, one sample wrote a class that works, though nothing stops the balance from going below zero:

```python
class BankAccount:
	def __init__(self, account, name, balance):
		self.account = account
		self.name = name
		self.balance = balance

	def deposit(self, amount):
		self.balance += amount

	def withdraw(self, amount):
		self.balance -= amount

	def showbalance(self):
		print(f'The account balance is {self.balance}')
```

The other sample stores the owner's age in the account, as samples at steps 32,278, 34,112, 38,869 and 42,646 did. As at the first two of those steps, a deposit makes the owner older:

```python
class BankAccount:
	def __init__(self, name, age):
		self.name = name
		self.age = age

	def deposit(self, amount):
		self.age += amount
```

Its next method, `get_account_info`, prints `self.balance`, which it never created, so calling that method would fail.

Given `class Stack:`, one sample named its list `queue` and built a queue, as one did at step 35,988: `push` adds at the front and `pop` takes from the back, so the oldest item comes out first. A stack should give back the newest. The other `Stack` has its `push` commented out, as at step 42,646.

Given `def is_prime(n):`, both samples only try divisors up to the square root of n. The one that returns an answer still calls 0 and 1 prime, as samples at steps 38,869 and 42,646 did. The function it wrote next, `sum_squares`, now adds real squares, but it starts at 2 and so leaves out 1 squared. At step 42,646 it added up `i*(i+1)**2`.

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
