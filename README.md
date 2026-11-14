# Laptick

**Laptick is a family of language models trained from random weights. The current model, Laptick-1, has 355.6 million parameters and is learning English, Python and Hebrew.**

No weights or code are published yet. This page explains what Laptick is and how far it has come.

Laptick is not related to [laptick.com](https://www.laptick.com/), an AI laptop buying advisor for India. The names matched by coincidence.

## Status

Last updated: 2026-11-14.

| | |
|---|---|
| **Stage** | Laptick-1 pretraining, main run |
| **Progress** | Step 46,648 of 48,066 (97.0%) |
| **Tokens read** | 6.11 billion |
| **Validation loss (English)** | 2.7744, the lowest so far (step 46,500) |

Laptick-1 is now 97% of the way through its main run, with about 1,400 steps to go. Its validation loss is still falling: every check so far has set a new low. It can't chat yet. It only continues text, and it makes up facts with total confidence. Chat lessons come after pretraining.

In our fixed repetition test at step 32,278, a token-loop detector flagged 1 of 100 completions, each capped at 128 new tokens. The average share of repeated four-word sequences was 4.6%, the lowest across our five checks so far. An unmatched reference from training text gave 1.6%, so that comparison is only rough.

## Samples

These are raw outputs from step 46,648, cut short, with the prompt in bold. Settings: temperature 0.8, top-k 50.

Sometimes it does well:

> **The best way to learn a new language is** to do it right. In this video, we'll show you a few different ways to do it. This video gives you a great example of how to improve your speed in learning a foreign language.\
> By the end of the video, you'll be able to:\
> • Learn French faster\
> • Improve your pronunciation\
> • Become more comfortable pronouncing French words\
> • Learn the language in a more natural way\
> • Improve your communication skills in French

It began the same way at step 44,770. The French lesson is new.

It mixes real facts with made-up ones:

> **ChatGPT is** a deep text analysis engine developed by OpenAI for text-intensive work. In this post, we will introduce you to the ChatGPT.\
> To understand how ChatGPT works, lets first understand what ChatGPT is. ChatGPT is an open-source text-mining platform developed by OpenAI.

OpenAI is right, and both ChatGPT samples have now said so for three updates in a row. The rest is made up: ChatGPT is a chatbot, not a text-mining platform, and it is not open source. Its opening words, “a deep text analysis engine developed by OpenAI”, are the same as in a sample at step 34,112. The other sample called it a tool “for the self-learning of artificial intelligence”.

> **Photosynthesis is the process by which** plants convert light energy into energy for the life of the plant.\
> The process of photosynthesis is very simple: the plants absorb water and light.\
> To do this, plants use a part of the carbon dioxide in the air as a source of energy.\
> A molecule of glucose is made by the body through photosynthesis.

Light, water, carbon dioxide and glucose all belong here, but the energy comes from the light. The carbon dioxide supplies the carbon. The other sample put photosynthesis in the chloroplasts, which is right, but counted carbon dioxide among “organic compounds”.

Sometimes it loses the thread:

> **Q: What is your name?**\
> **A:** What’s your house?\
> Q: And what’s your work?\
> A: Your name.\
> Q: What’s your job?\
> A: Your job.\
> Q: What’s your job title?\
> A: Your job title.\
> Q: What’s your occupation?\
> A: Your occupation.\
> Q: What’s your profession?\
> A: Your profession.

Its first answer is the same as at steps 28,640 and 44,770. From “What’s your job?” on, each answer just hands the question back. The other sample ignored the question and wrote what reads like part of a news article.

It still repeats itself:

> **The sun is a star that** is part of the Solar System. The Sun is a star that is part of the planets Solar System. This means that it is part of the Solar System. The Sun is a giant star. Sun comes into our Solar System from the outermost giant planet.

Its first three sentences all say the same thing, and the last two are wrong: astronomers class the Sun as a dwarf star, not a giant, and it did not come from a planet. The other sample for the language-learning prompt also looped: after five sentences it went back to its opening and started again.

Its Python is still mixed. Given `def is_prime(n):`, one sample wrote two functions and then stopped by itself:

```python
def is_prime(n):
	for i in range(2,int(n**0.5)+1):
		if n%i==0:
			return False
	return True

def sum_squares(n):
	summa=0
	for i in range(1,n+1):
		summa+=i**2
	return summa
```

`sum_squares` is now correct. At step 44,770 it left out 1 squared, and at step 42,646 it added up `i*(i+1)**2`. `is_prime` still calls 0 and 1 prime, as samples at steps 34,112, 38,869, 42,646 and 44,770 did. The other sample rejects 1 but still calls 0 prime, and it tries every number below n instead of stopping at the square root.

Given `class BankAccount:`, one sample stores the owner's age and no balance. Samples at five earlier steps, starting at step 32,278, also stored the age. As at step 34,112, a deposit makes the owner older and a withdrawal makes them younger. These lines are the same, character for character, as the ones shown then:

```python
class BankAccount:
	def __init__(self, owner, age):
		self.owner = owner
		self.age = age

	def deposit(self, amount):
		self.age += amount
		return self

	def withdraw(self, amount):
		self.age -= amount
		return self
```

Two methods later it names a method `yield`, which is a reserved word in Python, so the sample as a whole would not run. The other sample does keep a balance, but for a ten-digit account number with a balance above zero, its `checkBalance` prints “You have enough funds, you can't go ahead and pay off your balance.” and returns `False`.

Given `class Stack:`, one sample named its list `queue`, as samples at steps 34,112, 35,988 and 44,770 did, and like the last two of those it built a queue. This time `push` adds at the back and `pop` removes from the front, so the oldest item goes first. A stack should give back the newest. Its `pop` also throws the item away instead of returning it. The other `Stack` is right as far as it got: `push` and `pop` both use the end of the list, though the `if` in `push` does the same thing either way. It reached its length limit just as it started `is_empty`, which both methods call.

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
