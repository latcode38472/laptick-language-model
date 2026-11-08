# Laptick

**Laptick is a family of language models trained from random weights. The current model, Laptick-1, has 355.6 million parameters and is learning English, Python and Hebrew.**

No weights or code are published yet. This page explains what Laptick is and how far it has come.

Laptick is not related to [laptick.com](https://www.laptick.com/), an AI laptop buying advisor for India. The names matched by coincidence.

## Status

Last updated: 2026-11-08.

| | |
|---|---|
| **Stage** | Laptick-1 pretraining, main run |
| **Progress** | Step 38,869 of 48,066 (80.9%) |
| **Tokens read** | 5.09 billion |
| **Validation loss (English)** | 2.8122, the lowest so far (step 38,500) |

Laptick-1 has now read more than 5 billion tokens, over 80% of its main run, and its validation loss keeps reaching new lows. It can't chat yet. It only continues text, and it makes up facts with total confidence. Chat lessons come after pretraining.

In our fixed repetition test at step 32,278, a token-loop detector flagged 1 of 100 completions, each capped at 128 new tokens. The average share of repeated four-word sequences was 4.6%, the lowest across our five checks so far. An unmatched reference from training text gave 1.6%, so that comparison is only rough.

## Samples

These are raw outputs from step 38,869, cut short, with the prompt in bold. Settings: temperature 0.8, top-k 50.

Sometimes it does well:

> **The best way to learn a new language is** to begin listening to people speak. This is the easiest way to learn a language. It is also one of the most effective ways to learn a language as you study. In fact, it is a natural part of learning a language.

Often it doesn't:

> **ChatGPT is** a tool developed by Google that allows users to create and share documentaries.

This is the fourth update in a row where the model says ChatGPT is for making documentaries. The other ChatGPT sample at this step said OpenAI made it.

> **Photosynthesis is the process by which** plants use sunlight to convert carbon dioxide (CO2) into organic compounds; this is the process humans use to survive, not just breathe.
> The science of photosynthesis is pretty simple. The carbon dioxide that we breathe is made up of two carbon atoms and one oxygen atom.

It wrote CO2 itself, then got the atoms the wrong way round: carbon dioxide has one carbon atom and two oxygen atoms.

> **Q: What is your name?**
> **A:** I am a student in the Department of Physics and Astronomy.
> Q: Can you tell me something about the physics of light?
> A: Light can be defined as light that has a wavelength in the range of 980 nanometers to 1800 nanometers.

Visible light is about 380 to 750 nanometers.

It still repeats itself:

> **The sun is a star that** is hot all year round. And no wonder; it’s a perfect time to celebrate!
> Here are some fun ways to celebrate the sun, and some fun ways to celebrate the sun.

Its Python is still mixed. One prime check now only tries divisors up to the square root of n, a standard speed-up:

```python
def is_prime(n):
	for z in range(2,int(n**0.5)+1):
		if n % z == 0:
			return False
	return True
```

It is right for every whole number from 2 up, but it calls 0 and 1 prime. The function it wrote next, `sum_primes`, collects the primes but never adds them up. The other sample's prime check tries every divisor and is right for every positive whole number, but its next function, `get_prime_factors`, returns `[2, n, 1]` for every number.

Given `class BankAccount:`, one sample checks your balance by counting the digits of your account number:

```python
class BankAccount:
	def __init__(self, accountNumber, balance):
		self.accountNumber = accountNumber
		self.balance = balance

	def checkBalance(self):
		if len(str(self.accountNumber)) == 10:
			print('your balance is less than or equal to 10')
			return False
		else:
			return True
```

The other sample stores the owner's age in the account, as samples at steps 32,278 and 34,112 did. Those put deposits into the age, but this one puts them into the balance.

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
