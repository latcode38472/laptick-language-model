# Laptick

**Laptick is a family of language models trained from random weights. The current model, Laptick-1, has 355.6 million parameters and is learning English, Python and Hebrew.**

No weights or code are published yet. This page explains what Laptick is and how far it has come.

Laptick is not related to [laptick.com](https://www.laptick.com/), an AI laptop buying advisor for India. The names matched by coincidence.

## Status

Last updated: 2026-11-03.

| | |
|---|---|
| **Stage** | Laptick-1 pretraining, main run |
| **Progress** | Step 28,640 of 48,066 (59.6%) |
| **Tokens read** | 3.75 billion |
| **Validation loss (English)** | 2.8982, the lowest so far (step 28,500) |

Laptick-1 is almost 60% through its main run, and its validation loss has dropped below 2.9 for the first time. It can't chat yet. It only continues text, and it makes up facts with total confidence. Chat lessons come after pretraining.

In the latest repetition check (step 24,350), none of 100 samples got stuck in a loop, down from 5 at step 16,982. It still repeats four-word phrases more than real text does, though that comparison is only rough.

## Samples

These are raw outputs from step 28,640, cut short, with the prompt in bold. Settings: temperature 0.8, top-k 50.

Sometimes it does well:

> **The best way to learn a new language is** to do it. This is true whether you’re learning a language you’ve never spoken before, or you want to get a better grasp on how the language works.

Often it doesn't:

> **The sun is a star that** is the center of our universe. The sun was once a huge, bright ball of fire. [...] The sun does not emit any heat.

> **ChatGPT is** a tool developed by Google that allows users to create and share document-based content [...] It is intended for use with the Microsoft Office suite of operating systems and a variety of other applications, and is available as an open-source software package and freeware.

> **Q: What is your name?**
> **A:** What’s your house?
> A: And why do I call it the house of his teeth?

It still gets stuck in loops sometimes. Given the same question with another seed, it listed made-up children and then wrote:

> [...] I am not sure why. I am thinking of something else. I am not sure why. I am thinking of something else.

Its Python has improved. At step 24,350, `class Stack:` led to code that would crash. This time it wrote a stack that would work:

```python
class Stack:
	def __init__(self):
		self.stack = []

	def isEmpty(self):
		return self.stack == []

	def push(self,item):
		self.stack.append(item)

	def pop(self):
		return self.stack.pop()

	def display(self):
		print(self.stack)
```

Its logic still slips, though. Given `def is_prime(n):`, it wrote a check that says 3 isn't prime and 4 is:

```python
def is_prime(n):
	if n == 1 or n == 0:
		return False
	if n == 2:
		return True
	if n % 3 == 0 or n % 5 == 0:
		return False
	return True
```

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
