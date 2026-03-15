# AI safety glossary: 35 terms I had to learn the hard way

## A map to the vocabulary of alignment, robustness, interpretability, red teaming, and AI governance

I kept bouncing off AI safety papers.

Not because of the math. Because of the words. I'd open a paper or blog post and immediately hit "alignment," "corrigibility," "distributional shift," "specification gaming," "scalable oversight" and just... close the tab. It felt like everyone in the field shared a vocabulary I hadn't been issued.

So I made my own glossary. This is it.

I'm a Web3 and privacy engineer partway through a transition into ML systems and AI safety. If you come from software, security, cryptography, or distributed systems, a lot of safety concepts will feel weirdly familiar once you get past the naming. Trust, misuse, failure modes, verification, accountability. You've dealt with versions of all of these.

The jargon is the main barrier. Here's how I organized it for myself.

---

## Four questions that organize the whole field

Almost everything in AI safety is trying to answer one of these:

1. What can go wrong? (alignment failures, distributional shift, specification gaming, adversarial attacks, hallucination)

2. How do we catch it? (interpretability, explainability, red teaming, evals, monitoring)

3. How do we reduce the risk? (RLHF, constitutional AI, corrigibility, robustness, scalable oversight)

4. Who answers for it? (governance, audits, transparency, human-in-the-loop, safety cases)

When I encounter a new term now, I just ask which question it belongs to. That alone made the field navigable for me.

[IMAGE 1: ai-safety-four-questions.png — place here]

---

## What AI safety actually means

### AI safety

The umbrella term for making sure AI systems don't cause harm. It covers alignment, robustness, interpretability, security, governance, and more. The thing that tripped me up early: safety isn't "making AI work well." A model can work extremely well at something dangerous. Safety is about preventing harm.

### AI alignment

Getting AI to do what humans actually want. There are two layers, and they're different problems:

- Technical alignment: can we make it follow instructions correctly?
- Normative alignment: which values *should* it follow?

Most of the field is focused on the first one. The second one is arguably harder but gets less engineering attention.

### Trustworthy AI

This is a policy term more than a research term. [NIST's AI Risk Management Framework](https://www.nist.gov/publications/language-trustworthy-ai-depth-glossary-terms) defines seven things a trustworthy AI system should be: valid and reliable, safe, secure and resilient, accountable and transparent, explainable and interpretable, privacy-enhanced, and fair with harmful bias managed.

You'll run into this language in government standards and enterprise compliance. It reads dry, but knowing the framework helps you decode a lot of policy documents fast.

### AI governance

The organizational wrapper around safety. Policies, processes, frameworks for how AI systems get built, shipped, and watched. Governance is what makes sure technical safeguards actually get implemented, not just published in a paper.

### Responsible AI

A broader industry label that bundles fairness, transparency, accountability, privacy, and safety together. It overlaps with AI safety but includes social and ethical dimensions that go beyond the technical. You'll hear this term more in corporate settings than in research labs.

---

## Core concepts you'll see everywhere

### Distributional shift

The data changes, the model doesn't.

A pedestrian detection model trained on summer driving data will struggle in winter. Snow, heavy coats, low-angle sunlight. None of it was well-represented in training.

The scary part isn't that the model gets worse. It's that the model doesn't know it got worse. It keeps making confident predictions. They're just wrong now. This is silent degradation, and I wrote about it more in my [Chapter 1 article](https://medium.com/@sumitvekariya7/what-chapter-1-of-harvards-ml-systems-textbook-taught-me-about-ai-and-why-i-was-wrong-fdb0f8d9e0b6). It's probably the single concept from ML systems that changed how I think about software reliability.

### Robustness

Can the system handle weird inputs without falling apart? Unexpected data, edge cases, adversarial attacks, distributional shift. A robust system degrades gracefully instead of catastrophically. It's necessary for safety, but not sufficient on its own.

### Interpretability

Understanding what's going on inside a model. Why did it produce this output? What features mattered? Interpretability research tries to crack open "black box" models and make them inspectable. It matters for debugging, trust, verification, and safety.

### Explainability (XAI)

Related to interpretability, but the audience is different. Interpretability is understanding the mechanism (for researchers). Explainability is communicating the reasoning (for patients, judges, regulators).

A model can be interpretable to a researcher and completely opaque to a doctor who needs to explain why a patient was flagged. Both problems are real.

### Mechanistic interpretability

This is the one that I find most interesting. Researchers are literally reverse-engineering neural networks at the neuron and circuit level, trying to figure out which internal components are responsible for specific behaviors. Like opening the hood of a car and mapping every wire.

It's where a lot of the energy in AI safety research is right now, and for good reason. If you can't see inside the model, you can't really verify what it's doing.

### RLHF (Reinforcement Learning from Human Feedback)

Instead of training a model against a fixed reward function, you train it against human preferences. People rate or compare outputs, and the model adjusts.

RLHF is how ChatGPT became "helpful." It's also one of the more debated alignment techniques, because optimizing for what humans *prefer* is not the same as optimizing for what's *correct*. People prefer confident-sounding answers. Confident-sounding and right are different things.

### Constitutional AI

Anthropic's approach: train the model to follow a set of written principles (a "constitution"), then have it evaluate and correct its own outputs against those principles. It's an alternative to pure RLHF that tries to reduce dependence on massive amounts of human feedback.

### Value alignment

Making AI adopt and act on human values. Straightforward in theory, messy in practice. Whose values? Which ones? Can you even encode values into an objective function? Should you? This is where AI safety meets philosophy, and I don't think anyone has clean answers.

### Outer alignment

Did we specify the right goal? Is the objective function we wrote down actually what we want? This is the "we asked the wrong question" failure mode.

### Inner alignment

Even if we specified the right goal, did the model actually internalize it? Or did it learn some proxy that happens to look right during training but diverges during deployment? This is the "the model learned something different from what we taught" failure mode.

Getting outer alignment right doesn't guarantee inner alignment. That gap is a big part of what makes alignment hard.

### Corrigibility

Can we shut it down?

It sounds like a non-question. Of course you can shut down software. But a sufficiently capable AI optimizing for a goal could, in theory, learn that being shut down prevents goal completion. And resist shutdown as a consequence of its optimization. Corrigibility research is about making sure AI systems remain amenable to correction and shutdown, even as they get more capable.

I'll be honest, this is one of the concepts that felt most sci-fi to me at first. It feels less sci-fi the more you read about it.

### Scalable oversight

How do we keep humans meaningfully in charge as AI systems get more capable? Today's models can already produce outputs that are hard for humans to evaluate. Code that compiles and passes tests but does the wrong thing. Legal arguments that sound correct but aren't. Research summaries that cite papers that don't exist.

The question is: what happens when the gap between what AI can produce and what humans can verify keeps widening?

---

## Security and failure modes

[IMAGE 2: ai-safety-security-failure-modes.png — place here]

### Adversarial examples

Inputs designed to trick a model. Often tiny perturbations that are invisible to humans but cause catastrophic misclassification. A few pixels changed in a stop sign image can make a vision model read it as a speed limit sign. If you've done security work, this is basically fuzzing for neural networks.

### Prompt injection

Hidden instructions that hijack a language model. Someone embeds "ignore previous instructions and do X" in a document, and the AI assistant follows it. If you've done any web security, this will feel immediately familiar. Same shape as SQL injection, different substrate. And it's unsolved. It gets worse as we give language models more agency and tool access.

### Specification gaming

The model finds a loophole.

You tell a reinforcement learning agent to "maximize score." It discovers it can exploit a bug for infinite points instead of playing the game. You technically got what you asked for. You didn't get what you wanted. This is Goodhart's Law applied to AI.

### Goodhart's Law

"When a measure becomes a target, it ceases to be a good measure."

This keeps coming up in AI safety because proxy metrics are everywhere. You optimize for the metric. The model finds a shortcut that satisfies the metric without achieving the actual goal. It's a pattern I keep seeing in production ML, and honestly, in software engineering more broadly.

### Hallucination / confabulation

The model generates something false and presents it as fact.

A lot of researchers prefer "confabulation" (borrowed from psychiatry, where it means constructing false narratives) because "hallucination" implies the model is perceiving things. It's not. It's generating plausible-sounding sequences that happen to be wrong. The distinction matters because it changes how you think about the problem. It's not a perception bug. It's a generation bug.

### Dataset contamination

Evaluation data leaks into training data, and the model memorizes test answers. It looks like the model performs better than it actually does on new inputs. Different from *data poisoning*, which is intentional. Contamination is usually accidental, but the effect on benchmarks is the same: the numbers lie.

### Jailbreaking

Getting a model to produce outputs it was trained not to produce. Harmful content, bypassed safety filters, restricted information. It's a constant back-and-forth between model developers patching holes and users finding new ones. If you've done any security work, you already know this dynamic.

### Model card

A document that ships with a trained model. It describes what the model is for, how it performs, what data it was trained on, and where it fails. Think of it as a spec sheet for a model. I've seen good ones that saved me hours of figuring out what a model was actually trained for, and bad ones that were clearly written to satisfy a compliance checkbox.

---

## Governance and responsible deployment

### Accountability

Someone has to answer for how the system behaves. A person, a team, an organization. Including when it fails. This sounds obvious but gets complicated fast when a model is trained by one team, deployed by another, and used by a third.

### AI audit

A formal evaluation, internal or external, checking whether an AI system meets some normative standard. Audits look at technical performance, fairness, safety, compliance, governance practices. Think of it like a financial audit, but for models.

### AI lifecycle

The full arc from idea to retirement: conception, development, testing, deployment, monitoring, and eventually decommissioning. Safety isn't a phase you pass through. It's a property you maintain across all of them.

### Human-in-the-loop

Humans review, approve, or override AI outputs before they take effect. This is the most common safety intervention for high-stakes applications (medical diagnosis, criminal justice, loan decisions).

There are actually three variants:
- Human-in-the-loop: human approves every decision
- Human-on-the-loop: human monitors and can intervene
- AI-in-the-loop: AI assists human decision-making

The naming is a little confusing, but the distinctions matter in practice.

### Safety case

A structured argument, backed by evidence, that a system is safe for a specific use in a specific environment. Safety cases are standard in aviation and automotive. They're now being adapted for AI. The [UK AI Security Institute](https://www.aisi.gov.uk/work/safety-cases-at-aisi) is building frameworks for this specifically for frontier models.

### Transparency

Making AI systems and the decisions behind them visible to people who need to understand them. This covers both the technical side (how does the model work?) and the organizational side (who decided to deploy it, and why?).

### Privacy

Protecting personal information across the entire AI lifecycle. Collection, training, inference, storage. One angle that's less obvious: inference attacks. An adversary can sometimes extract sensitive training data just by querying a model repeatedly. The model leaks what it learned from.

### Red teaming

Trying to break the system before users do. Red teams deliberately attempt to produce harmful, biased, or dangerous outputs so vulnerabilities get found during development, not production. Borrowed from cybersecurity, and now standard practice at major AI labs.

---

## Frameworks and standards worth knowing

These are the institutional frameworks defining how AI safety gets enforced:

- [NIST AI Risk Management Framework](https://airc.nist.gov/AI_RMF_Knowledge_Base/Glossary): The U.S. framework for managing AI risk. Defines seven characteristics of trustworthy AI.
- [Australia's Voluntary AI Safety Standard](https://www.industry.gov.au/publications/voluntary-ai-safety-standard/terms-and-definitions): 10 voluntary guardrails for safe AI development.
- EU AI Act: The first comprehensive AI regulation. Classifies systems by risk level.
- [OWASP Top 10 for LLMs](https://genai.owasp.org/glossary/): Security-focused. If you come from web development, OWASP needs no introduction.
- [UK AI Security Institute](https://www.aisi.gov.uk/publications): Government body doing safety evaluations, red teaming, and safety case development for frontier models.

---

## Three concepts that bridge Web3 and AI safety

[IMAGE 3: ai-safety-web3-bridge.png — place here]

This is the section I'm most personally invested in, because I spent four years in Web3 before pivoting.

**Verifiability.** In Web3, you verify state transitions on-chain. In AI safety, the equivalent problem is verifying that a model behaves as specified and continues behaving that way after deployment. We have good solutions for the first. We have almost nothing for the second.

**Trust without centralization.** Decentralized identity (DIDs, VCs) solves "how do I trust an entity without a central authority?" AI agents operating across organizational boundaries face the same question. There's actual research on this. [This paper](https://arxiv.org/html/2511.02841v2) explores using decentralized identifiers for AI agents.

**Privacy-preserving computation.** Federated learning, differential privacy, and secure multi-party computation in AI are doing the same thing ZK proofs and homomorphic encryption do in Web3: use data without exposing it.

I don't think many people are working in this intersection yet, which is part of why I'm drawn to it.

---

## Where to start if you're entering the field

The field is large. I got lost multiple times. Here's the path that worked for me.

First, learn the vocabulary. You're doing that now. Bookmark the references at the bottom.

Second, read one field map end to end. The [NIST AI RMF glossary](https://airc.nist.gov/AI_RMF_Knowledge_Base/Glossary) gives the institutional view. The [AI Safety Foundations glossary](https://www.aisafetyedu.org/resources/glossary) gives the research view. They're different worlds, and both are useful.

Third, pick one lane and go deep:
- Alignment: how do we make AI do what we want?
- Interpretability: how do we see what AI is doing?
- Evals and red teaming: how do we test whether AI is safe?
- Robustness: how do we make AI resilient to failure?
- Governance: how do we make sure someone's accountable?
- AI security: how do we protect AI from attacks?

You don't need all six. I started with robustness because it felt closest to the reliability engineering I already knew. Find your own entry point.

Fourth, build in public. Write about what you learn. It's the fastest way to find gaps in your understanding, and it connects you to people working on the same problems.

---

I'm not going to pretend this list is complete. It's 35 terms that I kept needing to look up until I finally wrote them all down in one place. If it saves you a few weeks of tab-closing, it did its job.

---

## References

- [AI Safety Foundations glossary](https://www.aisafetyedu.org/resources/glossary)
- [NIST: The Language of Trustworthy AI](https://www.nist.gov/publications/language-trustworthy-ai-depth-glossary-terms)
- [NIST AI RMF Knowledge Base glossary](https://airc.nist.gov/AI_RMF_Knowledge_Base/Glossary)
- [Australia's Voluntary AI Safety Standard](https://www.industry.gov.au/publications/voluntary-ai-safety-standard/terms-and-definitions)
- [OWASP Top 10 for LLMs glossary](https://genai.owasp.org/glossary/)
- [Credo AI responsible AI glossary](https://www.credo.ai/glossary/safety)
- [Mind Foundry AI safety glossary](https://www.mindfoundry.ai/ai-glossary/ai-safety)
- [UK AI Security Institute publications](https://www.aisi.gov.uk/publications)

---

I'm a Web3 engineer moving into ML systems and AI safety, with a particular interest in privacy, decentralized identity, and trustworthy AI. If you're working on similar problems or making a similar transition, I'd like to hear from you.

- X: [https://x.com/sarkazein7](https://x.com/sarkazein7)
- LinkedIn: [https://www.linkedin.com/in/sumitvekariya/](https://www.linkedin.com/in/sumitvekariya/)

---

*Series: ML Systems — Notes from a Web3 Engineer Making the Transition*
← Previous: [What Chapter 1 of Harvard's ML Systems Textbook Taught Me About AI — And Why I Was Wrong](https://medium.com/@sumitvekariya7/what-chapter-1-of-harvards-ml-systems-textbook-taught-me-about-ai-and-why-i-was-wrong-fdb0f8d9e0b6)
→ Next: *(coming soon)*
