# AI tokens and cost research (Issue #1)

Nov 25, 2026 · @Amir

## The short answer

Maison ADITI does not need to pay much. If someone already has a Claude Max or Team plan, the included API credit may cover the whole placement; if not, a small prepaid top-up of $20 to $50 is enough to start.

- **Recommended platform:** the Claude API (Anthropic). It has a very cheap small model (Haiku 5.5), it does not train on API data, and it fits how we plan to build Agent Zero.
- **Possible free route:** if the owner or Amir has a Claude **Max** plan, it includes **$100 of API credit every month**. A Team plan includes $20 to $100 per seat. Pro does not include credit. New Console accounts also get a small amount of free test credit.
- **Expected cost:** about **$30 to $80 per month** for the whole system during the placement, based on the estimate below. That fits inside the Max plan's $100 credit.
- **Cost safety:** set a hard monthly spend limit in the Claude Console (for example $50). When it is reached, the API simply stops; it can never run up a surprise bill.
- **Important correction:** GitHub Models, which was a popular free option, was **fully retired on 30 July 2026**. It is no longer available.

## First, what is a token?

A token is a small piece of text, roughly three quarters of an English word. AI providers charge per million tokens (written MTok), with two prices:

- **Input tokens:** everything we send to the model (the instructions, the Brand DNA, the task).
- **Output tokens:** everything the model writes back, including its hidden reasoning ("thinking"). Output is usually five times the price of input.

So the main cost drivers are how long the answers are, and how much context we resend every time. The Brand DNA is sent with nearly every request, which is why prompt caching (below) matters so much for us.

## Free models versus paid models

The agent itself is just code we write, and that part is free. What is free or paid is the **model** behind it: the "brain" the agent calls. The same Agent Zero code can run on a free model or a paid one. What changes:

|  | Free models | Paid models |
| --- | --- | --- |
| Quality | Fine for simple tasks; slip more often on subtle voice and long instructions | Much better at holding the restrained Maison voice and judging what is on-brand |
| Limits | Capped (for example 50 to 1,000 requests a day); can change or disappear without warning, as GitHub Models did | High limits and reliable |
| Privacy | Some free tiers may use prompts for training or let staff read them | The Claude API does not train on our data |
| Business use | Some forbid commercial use, or serving UK users (Gemini's free tier) | Made for real products |
| Tools | Often basic | Extras agents rely on: web search, prompt caching, spend limits |

**In short:** free models are for learning and testing; paid models are for the real Maison ADITI system. The paid cost is small (around $50 a month), so it is worth it for the real work.

## How we "train" the agents

For this project we do **not** retrain the model itself. That is called fine-tuning: it needs hundreds of approved examples, more time and more money, and it is not needed to get a good brand voice in four weeks. Instead, we train the agents through what we give them:

1. **Brand knowledge pack:** the Brand DNA, the five archetypes, voice rules and words to avoid. Agent Zero sends it with every task, and prompt caching keeps that cheap.
2. **Example library:** pieces the owner approves become examples the agents copy the style from. This is the most powerful lever.
3. **Feedback loop:** when the owner edits or rejects something, the correction is saved, so the agents improve over time without retraining.
4. **Brand-fidelity tests:** a fixed set of tasks re-run after every change, to prove the agents are getting better (the sprint 3 evaluation).

This works with any model, so if we later switch an agent to a cheaper model, the training carries over.

**Models for the prototype:** Haiku 5.5 while building and debugging (hundreds of test runs cost cents), Opus 5.5 for Agent Zero and Sonnet 5.5 for content and research when producing real brand work, and a free local model (Qwen via Ollama) for offline practice with made-up data.

## Claude model prices (checked 25 Nov 2026)

| Model | Good for | Input / MTok | Output / MTok | Cached input / MTok |
| --- | --- | --- | --- | --- |
| Opus 5.5 | Hard judgment, orchestration, brand guarding | $4.00 | $20.00 | $0.20 |
| Sonnet 5.5 | Writing in the Maison voice, research | $2.00 | $10.00 | $0.10 |
| Haiku 5.5 | Simple, high-volume jobs (tagging, SEO checks, CRM) | $0.10 | $0.50 | $0.01 |

Other prices that matter for us:

- **Web search** (for the research and competitor agents): $10 per 1,000 searches, plus the tokens of the results.
- **Web fetch** (reading a page): no extra charge, only tokens.
- **Batch API:** 50% off everything, for jobs that can wait a few hours (for example the weekly digital growth report).
- **Prices are in USD.** Haiku 5.5 costs more for very long prompts (over 100,000 tokens), which we should not need.

## Which model for which agent

This is my recommendation. We should confirm it with the brand-fidelity evaluation in sprint 3 rather than guess.

| Agent | Model | Why |
| --- | --- | --- |
| Agent Zero (manager and brand guardian) | Opus 5.5 | It makes the important calls: routing, checking that work stays on-brand. It writes short messages, so the higher price adds little. |
| Content agent | Sonnet 5.5 | Most of the writing in the Maison voice. Try Opus only if the evaluation shows Sonnet misses the restrained, invitational tone. |
| Research and competitor agents | Sonnet 5.5 with web search | Needs good reading and summarising. Cap searches per task. |
| SEO agent | Haiku 5.5 | Keyword and metadata work is routine. |
| CRM agent | Haiku 5.5 | Short, templated customer messages; Agent Zero reviews anything sensitive. |
| Brand-fidelity checker (evaluation) | Haiku 5.5 first, Sonnet 5.5 if it is not accurate enough | It runs many times, so cheap matters. |

A learning point: this "big model manages, smaller models do the work" pattern is the standard way to keep multi-agent systems affordable. Our current default is Opus 5.5 everywhere, which is simplest to start with. Moving the worker agents to Sonnet and Haiku is the first change to make once everything works.

## Cost estimate

Assumptions (to be replaced with real numbers from the Console's usage page once we run the system):

- The Brand DNA plus agent instructions is about 10,000 tokens and is cached.
- A typical task adds about 2,000 new input tokens and produces about 2,500 output tokens (including thinking).
- Usage: about 30 tasks per working day, 22 days a month, so around 660 tasks a month.

| Item | Cost per task | Tasks per month | Monthly |
| --- | --- | --- | --- |
| Agent Zero handles every task (Opus 5.5) | about $0.02 | 660 | about $13 |
| Content tasks (Sonnet 5.5) | about $0.03 | 300 | about $9 |
| Research tasks with about 5 web searches (Sonnet 5.5) | about $0.12 | 100 | about $12 |
| SEO and CRM tasks (Haiku 5.5) | under $0.002 | 260 | under $1 |
| **Total** |  |  | **about $35** |

Development and testing usually doubles this, so budget **$50 to $80 a month** during the placement. Running everything on Opus 5.5 instead would roughly double the writing costs, to around $60 to $100. Either way, this is well within a $100 monthly credit.

## Free and low-cost options compared

| Option | What you get | Catch | Fit for Maison ADITI |
| --- | --- | --- | --- |
| Claude Max plan API credit | $100 per month of Claude API credit (Team: $20 to $100 per seat) | Needs a Max or Team subscription; credit does not roll over | **Best option if someone already has Max or Team** |
| Claude new-account credit | Small free credit to test the API | One-off | Good for the first week |
| Claude for Startups | Partner discounts; up to $100K API credit through partner VCs | **Applications are closed right now**; the big credit needs a partner VC | Join the newsletter and apply when it reopens |
| GitHub Models | Was free access to many models | **Retired 30 July 2026** | Not available |
| Google Gemini free tier | Free Flash and Flash-Lite models, with daily limits | Limits change without notice. Outside the UK and EEA, free prompts are used for training. Google's terms say apps serving UK users must use paid services. | Fine for Amir's own experiments, not for the product |
| OpenRouter free models | Open models such as Llama, Qwen and gpt-oss; about 50 requests a day, 1,000 with a $10 top-up | Low limits, no guarantees, quality varies | Good for practice, not for brand work |
| Groq or Cerebras free tiers | Fast open models, around 1,000 requests a day | No guarantees; smaller models are weaker at nuanced voice | Good for practice |
| Ollama (run models on your own laptop) | Completely free, and data never leaves the computer | Needs a decent laptop; much weaker than Claude for brand voice | Great for learning how agents work offline |

**Confidentiality rule:** the Brand DNA is confidential. It should only be sent to a paid service whose terms say it will not train on our data (the Claude API is one). Never paste it into a free tier that may use prompts for training or human review.

## Google and Chinese model options

These are worth knowing about, especially for learning and testing. Prices change often, so check each provider's own pricing page before deciding.

| Option | Free offer | Price after that (per million tokens, input / output) | Main concern for us |
| --- | --- | --- | --- |
| Google Gemini API (AI Studio) | Free tier for Flash and Flash-Lite models, with daily limits shown in AI Studio | Paid tier per model | Google's terms say apps serving UK users must use the paid service, so free is for testing only |
| Google for Startups Cloud Program, Start tier | Up to $2,000 of Google Cloud credit for one year, usable on Gemini | Normal Google Cloud prices | Aimed at tech startups founded in the last 24 months that plan to raise investment; needs a company website and matching business email. Maison ADITI may not qualify, but it costs nothing to apply. |
| DeepSeek (China) | Small sign-up credit; the amount changes | V4.1 Flash about $0.30 / $1.20; V4 Pro about $1.32 / $3.96; half price off-peak | Data is processed in China. Not suitable for the confidential Brand DNA or for UK customer data in the CRM agent. |
| Alibaba Qwen (Model Studio, Singapore region) | About 1 million free tokens per model for 90 days, new accounts only, eligible models listed in the console | Varies by model | Use the International (Singapore) region. Still a non-UK provider, so check terms before sending client data. |
| Open-weight Qwen, DeepSeek, Gemma or Llama models | Free to run yourself with Ollama, or free with limits on OpenRouter | Free (your own computer) | Weaker at subtle brand voice than the top paid models, but data stays private when run locally |

**How I would use them:** the Chinese and Google models are much cheaper per token, but price is not the only factor. For Maison ADITI the deciding factors are (1) keeping the confidential Brand DNA and customer data with a provider whose terms we trust, and (2) whether the model can hold the restrained, invitational voice. So:

- Use the free options (Gemini free tier, Qwen free quota, Ollama) for **learning and testing the agent plumbing**, with made-up or public data only.
- Keep **real brand and customer work** on a paid provider with clear no-training terms.
- Build the engine so the model provider is a setting, not hard-coded. Then in the sprint 3 brand-fidelity evaluation we can test a cheap model (for example Gemini Flash or Qwen) against Claude on the same tasks, and switch any agent that scores well enough.

Sources: [Google for Startups benefits](https://cloud.google.com/startup/benefits?hl=en), [Google for Startups FAQ](https://cloud.google.com/startup/faq), [Alibaba Model Studio free quota](https://www.alibabacloud.com/help/en/model-studio/new-free-quota.md), [DeepSeek pricing overview (Puter, Oct 2026)](https://developer.puter.com/tutorials/deepseek-api-pricing/).

## How to keep costs from exploding

In order of impact:

1. **Set a monthly spend limit** in the Claude Console (Settings, Spend limits). New accounts also start on the Start tier, which has a hard cap of $500 a month, so a runaway loop cannot cost more than that.
2. **Prompt caching.** Mark the Brand DNA and agent instructions as cacheable. After the first request, re-reading them costs 5% of the normal price on Opus and Sonnet (10% on Haiku). For us this is the single biggest saving.
3. **Use the right model per agent** (the table above).
4. **Effort setting.** Claude models have an "effort" setting from low to max that controls how much they think. Use low for simple agents, medium or high for Agent Zero.
5. **Cap output and loops.** Set a maximum answer length, a maximum number of web searches per task, and a maximum number of agent-to-agent steps, so an agent can never loop forever.
6. **Batch non-urgent work** (reports, bulk SEO rewrites) for 50% off.
7. **Log tokens per agent.** Every API response reports exactly how many tokens it used. We store that per agent so we can see which one costs the most, and show the owner a monthly cost report.

## Recommendation for the owner

1. Create a Claude Console account for Maison ADITI at platform.claude.com, owned by the business, not by Amir, so the API key and billing stay with them after the placement.
2. If the owner (or Amir) has a Max or Team plan, link it to claim the free monthly API credit. Otherwise, add **$20 to $50 of prepaid credit** to start.
3. Set a spend limit of **$50 a month** to begin with; raise it only if the usage page shows we need to.
4. Start with Opus 5.5 for Agent Zero, and move workers to Sonnet 5.5 and Haiku 5.5 once the evaluation shows quality holds.
5. Review real spend at the end of week 1 and adjust this estimate.

## Open questions for the owner's sister

- Does anyone at Maison ADITI already pay for a Claude Max or Team plan?
- Is there a monthly amount they are comfortable with (for example $50)?
- Who should own the API account and the payment card?

## Sources

- [Claude API pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- [Claude API rate and spend limits](https://platform.claude.com/docs/en/api/rate-limits)
- [API credits for Max, Ultra and Team plans](https://platform.claude.com/docs/en/about-claude/api-credits-for-subscribers)
- [Claude for Startups](https://claude.com/startups)
- [GitHub Models retirement notice](https://docs.github.com/en/github-models/use-github-models/prototyping-with-ai-models)
- [Gemini API terms](https://ai.google.dev/gemini-api/terms-archive/terms_11_20_25) and [Gemini free tier overview](https://neuraplus-ai.github.io/blog/gemini-api-free-tier.html)
- [Free LLM APIs compared (OpenRouter)](https://openrouter.ai/blog/tutorials/free-llm-apis-compared/)
