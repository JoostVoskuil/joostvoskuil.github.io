---
layout: post
title: How AI is changing how beginners learn in our field
date: 2026-06-14 01:00:00
description: How AI is changing how beginners learn in our field
categories: Learning
typograms: false
---

GitHub Copilot and similar AI assistants have changed software development. Code that used to take days can now be produced in hours. That is impressive. But speed is not the only thing changing. You no longer have to understand something to build it, and that has major consequences for how beginners in our field learn. Can we still train them into strong engineers? What is changing in the learning process? And how do you guide that well as an experienced engineer? That is what this article is about.

## The engineering compass

In the GitHub Copilot workshops I run, I see a clear pattern. Experienced developers increasingly use GenAI to learn a new language or technology. They let AI generate working sample code quickly, but they recognize patterns and architectural principles they have known for years. They have a professional compass. Russinovich and Hanselman describe this in *Redefining the Software Engineering Profession for AI*: experienced engineers feel an immediate productivity boost because their context and judgment are already well developed.

Beginners lack that compass. With AI, they too can quickly build something that works, but without the mental framework to judge whether that solution will still hold tomorrow: is the architecture sound? is it secure? will it scale? can it be maintained? An experienced developer asks those questions automatically. The difference between junior and senior is no longer about building something that works, but about judging whether what works is also *right*.

On top of that, fast, visible progress feels rewarding: you change something, it works, you want to keep going. For a beginner, the reward is no longer a line of code, but a complete feature. That reward arrives before real understanding does. In *Thinking, Fast and Slow*, Daniel Kahneman describes exactly this trap: System 1 says, “it works, we’re done,” while System 2 asks, “do I understand why it works, when it fails, and what I would do differently?” Vibe coding—prompting until something works without truly understanding it—is exactly the System 1 path. It feels productive and looks productive, but it does not build craftsmanship.

## Investing in beginners is becoming a better bet, but not automatically

Kent Beck frames this as an investment question. Every beginner you hire is a bet: onboarding, coaching, and review cost more than the output initially returns. Beck calls the period until the break-even point the *Valley of Regret*. Traditionally, that valley lasts about 24 months on average. With annual attrition of 20%, roughly 36% of beginners leave the organization before reaching that point. The investment evaporates.

AI can improve that equation, but only if you use it intentionally. Beck shows that a well-guided team of beginners using AI tools can compress that valley to around 9 months, with early attrition around 15%. The mechanism is simple: a beginner who used to spend three hours finding a suitable API now has options on the table in twenty minutes. But that freed-up time is not a bonus for the next feature. It is time you should invest in weighing those options and building independent judgment. Beck calls this: *learning fast becomes a habit*.

## How learning used to work, and how AI disrupts it

In the past, when you started as a beginner in a team, the build process itself carried understanding. You had to grasp a concept before you could get it working, because a copy-paste from Stack Overflow rarely fit directly. Your mentor demonstrated, then you worked together, and only afterward did you continue on your own. Collins, Brown, and Newman call this *cognitive apprenticeship*: learning through modeling and coaching, with guidance gradually fading as the learner becomes able to articulate their own reasoning.

AI breaks that chain if you are not careful. The beginner gets working code immediately, without an expert making the thinking visible and without being forced to explain that thinking themselves. Guidance therefore shifts from helping build to helping make sound decisions, with deliberate room for reflection.

Research confirms this tension. Chen and Cheung (2025) analyzed 57 studies with 5,389 students: GenAI has a large positive effect on performance and higher-order thinking, but the effect on metacognition (monitoring and adjusting your own learning process) is not statistically significant. That is exactly the skill a beginner needs to avoid falling into the System 1 trap.

Kazemitabaar et al. (2023) zoomed in further. In a controlled study with 69 young people (ages 10–17), half learned Python with access to AI and half without. The AI group completed 1.15 times more tasks, scored 1.8 times higher on correctness, and made significantly fewer errors. Crucially, on code-modification tasks completed *without* AI, both groups performed similarly. AI did not harm manual skill.

But the study also shows where things go wrong. On average, final solutions were 63% identical to what AI had generated. In 49% of tasks, the AI-generated code was submitted without the beginner changing a single line. That pattern was strongest in more complex topics like loops (60%) and arrays (52%)—exactly the moments when you would want someone to think just a little more deeply. What makes this especially concerning: this was not evenly distributed. 10 of 33 learners in the AI group did this in fewer than 4 out of 45 tasks. But 14 of 33 did it in more than half. One part of the group learned actively with AI; another part learned the shortest path to an approved submission. And beginners with more prior knowledge benefited significantly more from AI than those with less. AI strengthens an existing compass; it does not build a new one.

## A deliberate framework: 4MAT

When code is fast and cheap to produce, it becomes more important that a beginner learns how to make choices and test assumptions. That requires a deliberate framework, instead of an implicit process where you improvise and hope learning happens by itself. Bernice McCarthy’s 4MAT learning cycle offers such a framework. Four phases, each with a key question:

**Experiencing (Why?)**: *why does this problem matter?* AI makes it easy to find relevance: one prompt and you can see how a concept works in practice. But beginners who never have to discover relevance themselves are slower to build independent judgment. Motivation comes easily; the compass does not.

**Conceptualising (What?)**: *which concepts are at play here, and which principles determine whether a solution is good enough?* AI is the new first layer of knowledge: explanations, examples, and debugging support are always available. This shifts the core skill from “knowing frameworks” to asking the right questions and evaluating output. The risk is an illusion of understanding: you see lots of working code and think you understand it, while in reality you only had it generated.

**Applying (How?)**: *build a small, focused experiment.* In the spirit of Farley: write down what you expect before prompting AI. That is a hypothesis in testable form. AI dramatically accelerates execution, from API to test to deployment. But that shifts the real bottleneck: not writing code, but validating and integrating it into a larger system. Architectural thinking, test discipline, and debugging become core skills.

**Refining (What if?)**: *what does this outcome imply for variants, scalability, and risk?* With AI, rapid prototyping is trivial: you can explore multiple architectures in hours and generate variants on demand. This lets beginners operate earlier as mid-level experimenters. But too many options without anchors leads to decision fatigue. Good developers explore fast and choose fast. Guidance shifts toward evaluation and trade-off thinking.

AI compresses the cycle, but shifts the pressure. The bottleneck used to be writing code (*How*). Now the bottlenecks are understanding systems (*What*), choosing direction (*Why*), and making decisions under uncertainty (*What if*). A senior can no longer just delegate tasks and review output afterward. You also need to delegate thinking: the beginner learns to formulate *Why* and *What* before AI accelerates the *How*. That is exactly what the Kazemitabaar study shows. Productivity and learning diverge if the learning environment does not actively prevent it.

## What this means for the mentor

The value of your guidance is no longer in explaining how to build something. AI can do that faster. The value is in developing professional judgment: why choose this route, which assumptions are embedded in it, and when is “good” not good enough?

Concretely:

- **Delegate thinking, not just work.** Have the beginner first make context, hypothesis, and quality criteria explicit. Then AI can help with implementation.
- **Review reasoning, not just code.** Which alternatives were explored? Why does this solution fit? What changed in the original assumption after the experiment?
- **Adapt to learning style.** A doer does not need encouragement to experiment, but does need encouragement to slow down: what do you expect will happen? A thinker sometimes needs a push to start the prototype, even if the model does not feel complete yet. A dreamer needs room to recognize patterns. A decider sometimes needs to slow down and compare two outcomes before creating a third.
- **Do not correct too quickly.** Fixing a half-good AI solution feels efficient, but it removes the learning moment. Let the beginner explain what is solid, what is doubtful, and what they would do differently.

The best mentor is the one who helps beginners practice senior behavior sooner—not through more tasks, but through ownership of reasoning, choosing, and validating. My main tip: slow beginners down and ask deeper questions when it seems too easy. AI already does the fast work. Your role is to help place that fast work into a sustainable framework.

## What this requires from organizations and educational institutions

Mentors create the daily learning moments. But leaders decide whether mentors get the space and incentives to do so. Without that space, the effect evaporates.

Anyone deploying AI without a deliberate development strategy buys short-term speed while building long-term debt in knowledge and quality. That is exactly the risk Jeen Beckers describes as “digital suicide”: more output on paper, but less learning capacity in your team. His point is sharp: fewer junior intakes, seniors mainly cleaning up AI debris, and rising maintenance burden. AI appears to accelerate you, but in practice you are just pushing quality debt forward.

On top of that, a toxic dependency loop emerges: management optimizes for throughput, teams receive fast solutions “thrown over the wall,” and then the same organization must make that mess sustainable. Panic often leads to a staffing IV-drip: more external hires for short-term capacity. But every temporary expert who leaves takes context with them. Without internal growth of people and knowledge, your digital autonomy is gradually hollowed out.

If you evaluate only output, you reward superficial speed. If you also evaluate justification, validation, and reflection, you reward professional maturity. For companies, that means: do not steer only on “features per sprint,” but also on how quickly beginners learn to reason, spot risks, and explain choices. As Kent Beck describes, the time you gain from faster implementation must be consciously reinvested in the learning loop. Not immediately into more output, but into understanding, trade-offs, and craftsmanship.

That is why Delta-N invests in educational partnerships, guest lectures, and mentoring graduating students on realistic challenges. In that practice, students experience that AI makes them faster—and that professional judgment determines whether that speed also creates sustainable value.

## Closing

Building is getting faster. The value of pure code production is declining. The value of conceptual reasoning is increasing. That is not a weakening of the profession, but a deepening.

A strong professional does not just show that something works, but also why it works, when it can fail, and how that leads to a better next decision. That is where durable craftsmanship begins.

---

## Sources

- Beck, K. (2024). *The Bet on Juniors Just Got Better*. Tidy First, Substack. [tidyfirst.substack.com](https://newsletter.kentbeck.com/p/the-bet-on-juniors-just-got-better?utm_source=publication-search)
- Beckers, J. (2024). *Zo plegen veel Nederlandse IT-organisaties digitale zelfmoord*. [LinkedIn](https://www.linkedin.com/pulse/zo-plegen-veel-nederlandse-it-organisaties-digitale-jeen-beckers-pph3e/?originalSubdomain=nl)
- Chen, S., & Cheung, A.C.K. (2025). *Effect of generative artificial intelligence on university students learning outcomes: A systematic review and meta-analysis*. Educational Research Review, 49, 100737. DOI: [10.1016/j.edurev.2025.100737](https://doi.org/10.1016/j.edurev.2025.100737)
- Collins, A., Brown, J.S., & Newman, S.E. (1989). *Cognitive Apprenticeship: Teaching the Crafts of Reading, Writing, and Mathematics*. In L.B. Resnick (Ed.), *Knowing, Learning, and Instruction: Essays in Honor of Robert Glaser* (pp. 453-494). Lawrence Erlbaum Associates.
- Farley, D. (2021). *Modern Software Engineering: Doing What Works to Build Better Software Faster*. Addison-Wesley Professional.
- Kahneman, D. (2011). *Thinking, Fast and Slow*. Farrar, Straus and Giroux.
- Kazemitabaar, M., Chow, J., Ma, C.K.T., Ericson, B.J., Weintrop, D., & Grossman, T. (2023). *Studying the Effect of AI Code Generators on Supporting Novice Learners in Introductory Programming*. CHI '23. DOI: [10.1145/3544548.3580919](https://doi.org/10.1145/3544548.3580919)
- Kolb, D.A. (1984). *Experiential Learning: Experience as the Source of Learning and Development*. Prentice Hall. See also: [imkopleidingen.nl/nieuws/kolb-leerstijlen](https://www.imkopleidingen.nl/nieuws/kolb-leerstijlen/)
- McCarthy, B. (1980/1987). *The 4MAT System: Teaching to Learning Styles with Right/Left Mode Techniques*. Excel Inc. See also: [4mat4learning.com.au](https://4mat4learning.com.au/what-is-4mat/)
- Russinovich, M., & Hanselman, S. (2024). *Redefining the Software Engineering Profession for AI*. Communications of the ACM. DOI: [10.1145/3779312](https://doi.org/10.1145/3779312)
