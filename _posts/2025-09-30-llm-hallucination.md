---
layout: post
title: "When AI Imagines Things: Understanding LLM Hallucinations!"
tags: AI, coding
---

<div style="text-align:center;">
<img align="center" src="https://aitificial.blog/wp-content/uploads/2024/03/ai-hallucinations.jpg" height="70%" width="70%"/>
</div>
<br/>

Large Language Models (LLMs) like GPT‑4, GPT‑5, and tools built on them such as GitHub Copilot have changed the way developers write code. They can autocomplete functions, suggest refactors, generate test cases, and even scaffold entire modules. Despite their impressive capabilities, they are not perfect, and one of the most frequent issues developers encounter is **hallucination** — when the model confidently produces outputs that are technically or factually incorrect. Hallucinations can range from minor syntactic errors to completely fabricated classes, methods, or API calls that don’t exist.

Hallucinations arise from the way LLMs are trained. During pretraining, models are exposed to enormous datasets of text and code, learning patterns and sequences of tokens. Fine-tuning and instruction-based training improve their ability to follow human instructions and generate contextually appropriate code, but the models still rely on predicting the next token rather than verifying correctness. This means that when faced with ambiguous or incomplete prompts, or when dealing with less familiar APIs, the model may produce outputs that look plausible but are not valid in reality.

Another key factor is context management. Tools like GitHub Copilot operate in **multi-turn interactions**, where developers provide a prompt and the model responds. Early turns tend to produce accurate and relevant suggestions. However, as sessions grow longer, context drift occurs, and the model may “forget” earlier instructions or the precise constraints of the codebase. This often leads to hallucinations, such as generating helper classes or methods that sound reasonable but do not exist, or suggesting changes that bypass existing logic.

Even straightforward modifications in production code can trigger hallucinations. For instance, while updating a REST controller that originally handled four parameters — `productName`, `assetClass`, `productClass`, and `investmentHorizon` — to add a fifth parameter, `ricCode`, Copilot began suggesting helper methods and service modifications that did not exist. These suggestions were syntactically valid but functionally incorrect, requiring careful manual review to filter out hallucinated code and ensure proper integration with the service layer and caching logic. This small but practical example illustrates how multi-parameter changes and context drift can cause LLMs to generate unreliable code.

Hallucinations are also influenced by evaluation methods. Many models are trained and scored based on producing confident answers rather than admitting uncertainty. If a model does not know an answer or the correct API, it often guesses rather than defers, resulting in outputs that may appear correct but are false. This behavior, combined with the probabilistic nature of token prediction, explains why even highly capable models sometimes fabricate code, functions, or classes.

Mitigating hallucinations requires a combination of careful prompting, incremental changes, and verification. Developers should provide explicit and detailed instructions, break down tasks into manageable steps, and review every suggested change against existing code, documentation, and test cases. In longer coding sessions, it can be useful to reset the prompt or re-establish constraints to prevent context drift. Additionally, grounding models with retrieval-augmented sources or documentation can help ensure that outputs are based on verified information rather than predictions alone.

Ultimately, hallucinations are a natural consequence of how LLMs operate. They are the result of statistical token prediction, limited context windows, and evaluation incentives that reward confident answers. While they cannot be fully eliminated, awareness of their causes and implementing disciplined workflows can reduce errors. Tools like GitHub Copilot remain transformative for software development, provided developers treat suggestions as **assistive guidance** rather than authoritative code and verify every output before integration. Understanding these limitations allows teams to leverage LLMs safely, accelerating development while maintaining code quality and correctness.

