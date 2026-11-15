# Laptick

**Laptick is a family of language models trained from random weights. The current model, Laptick-1, has 355.6 million parameters and is learning English, Python and Hebrew.**

No weights or code are published yet. This page explains what Laptick is and how far it has come.

Laptick is not related to [laptick.com](https://www.laptick.com/), an AI laptop buying advisor for India. The names matched by coincidence.

## Status

Last updated: 2026-11-15.

| | |
|---|---|
| **Stage** | Laptick-1 pretraining, main run finished |
| **Progress** | Step 48,066 of 48,066 (100%) |
| **Tokens read** | 6.29 billion |
| **Validation loss (English)** | 2.7709, the lowest so far (step 48,000) |

Laptick-1 has finished its main run. Its validation loss was still falling at the end: every one of its 96 checks set a new low. Next come the checks that decide on the replay and finish stages (see Training). It can't chat yet. It only continues text, and it makes up facts with total confidence. Chat lessons come after pretraining.

In our fixed repetition test at step 48,066, a token-loop detector flagged 1 of 100 completions, each capped at 128 new tokens. The average share of repeated four-word sequences was 3.8%, the lowest across our six checks. An unmatched reference from training text gave 1.6%, so that comparison is only rough. The drop from 4.6% at step 32,278 is small enough to be chance, and on the English prompts alone repetition rose slightly, also within chance.

## Samples

These are raw outputs from step 48,066, the last step of the main run. They are cut short, with the prompt in bold. Settings: temperature 0.8, top-k 50.

Sometimes it does well:

> **The best way to learn a new language is** to begin listening to people speak other languages. In fact, a growing body of research suggests that learning foreign languages not only improves your vocabulary and grammar, it also supports your health and wellbeing.

That is the sample's whole first paragraph. Up to “not only improves your” it is word for word the sample shown at step 42,646. Its next sentence went wrong: it said that after the first year of college “your brain still has little information about your native language”. The other sample opened with “to do it right. In this video, we'll show you a few”, as at steps 44,770 and 46,648.

It mixes real facts with made-up ones:

> **ChatGPT is** a tool developed by Google that allows users to create and share documentaries, blogs, videos, or interactive user stories, which are used for various purposes, such as advertising, marketing, social media integration, and text mining.\
> What is ChatGPT?\
> ChatGPT is an AI-powered chatbot that has already been introduced in numerous tools. It is a machine learning system that can learn and understand human language and natural language from textual and natural language input.

Its answer under “What is ChatGPT?” is close to the truth: ChatGPT is an AI chatbot. Its first sentence is not: OpenAI made ChatGPT, not Google, and it is not a tool for sharing documentaries. For three updates in a row both ChatGPT samples named OpenAI. This time neither does, and this one opens with the same words as the samples shown at steps 35,988 and 38,869: “a tool developed by Google that allows users to create and share documentaries”. The other sample called it “a deep learning program” and a chatbot, which is right, and “an open-source project”, which it is not.

> **The sun is a star that** is moving through space at an astonishing rate. It is a huge, bright ball of light that shines in all directions. It is the largest star in the Milky Way and is even bigger than our sun.\
> The sun is made from hydrogen and helium. It has a core of carbon, hydrogen, and helium. It has a core that is made up of hydrogen, helium, and sometimes oxygen. The sun is a sphere, with the sun in one side and the earth in the other.

The Sun does move through space, it does shine in all directions, and it is made mostly of hydrogen and helium. But it is far from the largest star in the Milky Way, and the Sun cannot be bigger than itself. The sample also describes the core twice, with different ingredients each time, and its last sentence puts the Sun inside the Sun. The other sample said that “the sun’s temperature is the highest on earth”.

Sometimes it loses the thread:

> **Q: What is your name?**\
> **A:** What’s your house?\
> Q: And what’s your mother’s name?\
> A: What’s hers?\
> Q: How old are you?\
> A: How old are you?\
> Q: I’m 20.\
> Q: And what do you call my brother?\
> A: What’s his name?

Its first answer is the same as at steps 28,640, 44,770 and 46,648. Every answer here is another question, so the questioner ends up giving its own age. The other sample opened with “I am a woman, and I am from”, as samples at steps 42,646 and 44,770 did, and gave no name either.

It still repeats itself:

> **Photosynthesis is the process by which** plants convert sunlight energy into sugars and other materials to fuel all their activities. Most plants are photosynthetic. They are not, however, like all other organisms, in that photosynthesis is a chemical reaction. It is a process that takes place in plants and certain plants are capable of doing so. While some plants are able to carry out photosynthesis and some cannot, most are able to photosynthesize.

The first sentence is a fair summary. Three of the next four sentences make one point in different words: plants do photosynthesis, but not all of them. The other sample went in a circle: it explained photosynthesis as “a process known as photosynthesis” and then named both of its steps “Photosynthesis”.

Its Python is still mixed. Given `class Stack:`, one sample wrote a stack that mostly works:

```python
class Stack:
	def __init__(self):
		self.queue = []
	def push(self, value):
		self.queue.append(value)
		
	def pop(self):
		return self.queue.pop()
		
	def peek(self):
		return self.queue[len(self.queue)-1]
		
	def full(self):
		return self.queue[len(self.queue)-1]
```

It named its list `queue`, as samples at steps 34,112, 35,988, 44,770 and 46,648 did. The last three of those built queues. This one is a stack: `push` and `pop` both work at the end of the list, and `pop` returns the item. Its `full` is a copy of `peek`, so it returns the top item instead of saying whether the stack is full. The other `Stack` is also right as far as it got. The `if` in its `push` does the same thing either way, as at step 46,648, and it reached its length limit before defining `is_empty`, which its `push` and `pop` both call.

Given `def is_prime(n):`, one sample wrote a prime check that is right for every positive whole number, and then started a list of primes with 2, 4, 6, 8 and 10:

```python
def is_prime(n):
	if n == 1:
		return False
	else:
		for i in range(2,n):
			if n % i == 0:
				return False
		return True

def get_primes(n):
	primes = []
	primes.append(2)
	primes.append(4)
	primes.append(6)
	primes.append(8)
	primes.append(10)
```

Like one sample at step 46,648, it still calls 0 prime, and it tries every number below n instead of stopping at the square root. The other sample now rejects 0 and 1, which samples at five earlier steps called prime. After that it only checks whether n is odd, so it calls 9 prime and 2 not prime. As at step 38,869, its `sum_primes` collects the primes but never adds them up. It returns the first half of the list.

Given `class BankAccount:`, one sample keeps a balance but gets the check in `withdraw` backwards:

```python
class BankAccount:
	def __init__(self, accountNumber, balance):
		self.accountNumber = accountNumber
		self.balance = balance

	def checkBalance(self):
		if len(str(self.accountNumber)) == 10 and self.accountNumber != "":
			print("You have already entered a valid account number")

	def withdraw(self, amount):
		if self.balance >= amount:
			print("Insufficient balance!")
```

It prints “Insufficient balance!” when there is enough money. As at steps 38,869 and 46,648, `checkBalance` tests for a ten-character account number, and like the first of those it never looks at the balance. The other sample stores the owner's age again, as samples at six earlier steps did, starting at step 32,278. It takes a balance too but never stores it, so its `deposit` would fail at `self.balance += amount`.

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
