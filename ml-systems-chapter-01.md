# What Chapter 1 of Harvard's ML Systems Textbook Taught Me About AI — And Why I Was Wrong

*Notes from a Web3 engineer transitioning into ML Systems*

I've spent the last four years writing smart contracts.

Solidity, zero-knowledge proofs, DeFi protocols, decentralized identity systems — the kind of work where correctness is binary. Either the contract does what the specification says, or it doesn't. You can formally verify it. You can audit it. When something breaks, there's a stack trace.

I assumed machine learning was similar. Write a model, train it, deploy it, monitor the logs.

I was wrong in ways that Chapter 1 of [mlsysbook.ai](https://mlsysbook.ai) — Harvard's open ML Systems textbook by Prof. Vijay Janapa Reddi — made painfully clear within the first few pages.

This is my attempt to document what actually surprised me, and why it matters — not just for building ML systems, but for the alignment and safety questions I care about.

---

## Why I'm doing this publicly

I'm making a deliberate transition from Web3 engineering into ML Systems and AI Safety. Not abandoning my background — connecting it.

The questions I spent years asking in decentralized systems — how do you verify trust, preserve privacy, build accountable systems under adversarial conditions — are the same questions becoming urgent in AI.

Rather than pretending to already know ML, I'm studying from first principles and writing about what I learn. The textbook is free, open, and rigorous. If you're on a similar path, I'll be posting notes after each chapter.

This is Chapter 1.

---

## Assumption 1: Better algorithms make better AI

The first thing the textbook dismantles is a comfortable belief: that progress in AI comes primarily from smarter algorithms.

Richard Sutton — a reinforcement learning pioneer and 2024 ACM Turing Award recipient — spent decades watching the same pattern repeat. Researchers would build clever systems encoding human expertise: chess strategies, linguistic rules, feature detectors for vision. These would work impressively within their narrow domain. Then a simpler model, trained on more data with more compute, would outperform them.

Every time.

Sutton called this *The Bitter Lesson* in a 2019 essay.

> "The biggest lesson that can be read from 70 years of AI research is that general methods that leverage computation are ultimately the most effective, and by a large margin."

The examples are striking when you line them up:

- **Chess.** IBM's Deep Blue defeated Kasparov in 1997. It didn't encode grandmaster strategy. It evaluated 200 million positions per second.
- **Go.** AlphaGo didn't study centuries of human Go wisdom. It played itself billions of times through self-play.
- **Language.** GPT didn't learn from linguistics professors. It trained on the raw internet.
- **Vision.** CNNs that learn features directly from images outperformed decades of hand-crafted feature engineering.

The "bitter" part is that we keep forgetting this lesson. Every generation tries again to encode human expertise, gets short-term gains, and gets beaten by the next scale-up.

**The implication for systems engineering:** if computation is the deciding factor, infrastructure is the bottleneck — not algorithms.

The question in modern ML stopped being *"what algorithm?"* It became *"how do we coordinate 10,000 GPUs efficiently enough to actually scale this?"*

This is why ML Systems Engineering exists as a field. It's not a support function for algorithm researchers. It *is* the competitive advantage.

---

## Assumption 2: When an ML system breaks, you'll know

This one hit hardest — partly because of where I'm coming from.

In smart contract development, failures are (usually) explicit. Transactions revert. Events emit. There are on-chain traces. When something goes wrong in production, the evidence is often right there, immutable and auditable.

ML systems break differently. They break *silently*.

The textbook's example is precise: an autonomous vehicle's pedestrian detection accuracy drops from 95% to 85% over several months. Winter lighting. New clothing patterns. Weather the training data didn't represent. The system continues operating. No error is thrown. No alert fires. Infrastructure dashboards show healthy uptime.

> The system just quietly becomes less safe.

This is called **silent degradation** — and it's not a bug you can patch. It's a property of how ML systems relate to the world.

Here's why it happens: traditional software derives its behavior from code, which only changes when a developer changes it. ML systems derive their behavior from *data patterns learned during training*. Data reflects the real world. The real world changes.

- User preferences evolve
- Seasons shift sensor performance
- Road layouts get modified
- New edge cases emerge that were never in the training distribution

When the world drifts from what the model was trained on, the model's behavior drifts too — but the code hasn't changed, so none of the traditional monitoring systems notice.

A recommendation system might drop from 85% to 60% accuracy over six months. Users keep getting recommendations. Infrastructure reports healthy. Business value silently evaporates.

The monitoring practices this requires are completely different from traditional software ops. You're not watching for crashes or error rates. You're watching for shifts in input distributions, changes in prediction confidence, degradation in business metrics that only become visible when you know what to look for.

**ML operations is a discipline in itself. Not an afterthought.**

---

## The AI Triangle: a better diagnostic framework

One of the most useful things Chapter 1 introduces is a simple framework for thinking about every ML system.

Three interdependent components:

- **Data** — guides behavior
- **Algorithms** — learn patterns
- **Infrastructure** — enables training and inference

The textbook calls this the AI Triangle. The key insight isn't the components themselves. It's that they're *interdependent*. Constrain any one, and the others can't compensate.

The 2012 AlexNet breakthrough illustrates this clearly. Convolutional neural networks weren't new in 2012. The architecture had existed for decades. What changed was that all three components finally aligned at once:

- **Data**: ImageNet — 1.2 million labeled images
- **Algorithm**: CNN architecture with dropout and data augmentation
- **Infrastructure**: NVIDIA GTX 580 GPUs providing 192 GB/s memory bandwidth

> Change any single component and the breakthrough probably doesn't happen when it did. There's no "one thing" that caused AlexNet to work. It was the convergence.

This reframes how I now think about ML failures. When a model underperforms in production, the instinct is to tune the model. But:

- Poor performance is often a **data quality problem** — mislabeled data, distribution skew between training and serving, stale data that no longer reflects current patterns
- Data problems are often **infrastructure problems in disguise** — pipeline bugs that compute features differently between training and serving
- Infrastructure problems sometimes **stem from data problems** — your serving latency is fine, but the model is running on data preprocessed differently than at training time

The AI Triangle is useful not because it tells you what to fix, but because it tells you *where to look*. Most debugging time in ML is spent looking in the wrong place.

---

## The deployment spectrum: one framework, many constraints

Chapter 1 also maps out how differently the same principles apply depending on where a system runs.

At one end: cloud-based ML on massive data centers. Virtually unlimited compute. Millions of users served simultaneously. Training GPT-3 consumed approximately 1,287 MWh of energy and required coordinating 1,024 V100 GPUs across weeks. The engineering challenge is coordination at scale.

At the other end: TinyML — neural networks on microcontrollers costing under a dollar, with kilobytes of memory, on battery power. Smart home devices recognizing voice commands on less power than an LED bulb. The challenge is radical compression without losing the capability that matters.

Between these: mobile ML (4–12 GB RAM, strict power budgets), edge ML (compute close to data sources), enterprise ML (domain-specific, integrated with existing systems), hybrid architectures.

Each deployment context changes everything. What data you can collect. How you can train. What monitoring is feasible. How you update models in the field. A choice to deploy on embedded devices ripples through every stage of the lifecycle.

> ML isn't a model. It's a system with a lifecycle — and that lifecycle looks different depending on where it runs.

---

## The part I keep thinking about: alignment is a systems problem

I came to this textbook partly because I care about AI Safety and Alignment. Chapter 1 surfaced something I don't see discussed enough.

If the Bitter Lesson holds — if general methods that leverage computation *always* beat carefully designed, knowledge-encoded approaches — then it applies to alignment too.

Alignment approaches that rely primarily on careful algorithm design, on encoding human values precisely into model objectives, face the same pressure every "clever human knowledge" approach has faced throughout AI history. Eventually, a system trained at larger scale with more data produces better results — aligned or otherwise — even without the careful design.

This doesn't make alignment research futile. It changes what alignment research needs to engage with.

> The question can't just be "how do we design an aligned model in the lab?" It has to be "how do we build *systems* that remain aligned as they scale, degrade, and encounter the world?"

Silent degradation is an alignment problem as much as a performance problem. A model that behaves well during evaluation, then slowly drifts as its training distribution becomes stale — that's not just a performance failure. In safety-critical deployments, that's a safety failure.

Alignment can't be a property you bake in at training time and assume holds forever. It has to be a *systems property* — something you monitor, maintain, and actively preserve across the full lifecycle.

The infrastructure that trains and deploys models at scale is where alignment is actually implemented. Understanding that infrastructure isn't optional for alignment researchers. It's the work.

---

## What I'm carrying forward

Chapter 1 is framing, not depth. The real technical content is ahead. But the frame matters — it changes what you notice in every chapter that follows.

Three things I'm carrying into Chapter 2:

**1. Infrastructure is not plumbing.**  
In ML, infrastructure is where competitive advantage lives and where safety properties are actually implemented.

**2. Silent degradation is the default failure mode.**  
Any system I build needs monitoring designed around this — not traditional uptime metrics, but behavioral monitoring.

**3. The AI Triangle is a diagnostic tool.**  
Before blaming the model, ask: is this a data problem? An infrastructure problem? The model is usually the last thing to fix.

And one open question I'm sitting with: if alignment is a systems property and not just a model property, what does good alignment *infrastructure* actually look like?

I don't have an answer yet. But it feels like the right question to be asking.

---

*I'm working through [mlsysbook.ai](https://mlsysbook.ai) — the ML Systems textbook from Harvard, by Prof. Vijay Janapa Reddi. It's free, open, and genuinely excellent. If you're a software engineer trying to build ML foundations from first principles, I'd recommend starting there.*

*Chapter 2 next.*

---

**Series: ML Systems — Notes from a Web3 Engineer Making the Transition**  
← Previous: *(this is the first)*  
→ Next: Chapter 2 — ML Systems Architecture *(coming soon)*
