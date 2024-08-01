---
layout: post
title: What We Can Learn from SpaceX Regarding DevOps
date: 2024-03-01 23:36:10
description: This post explains the awesome things that are happening right now at Starbase and what we as professionals can learn from SpaceX
#tags: DevOps, Space, Learning
categories: DevOps
typograms: false
---

> This blogpost was writen for Delta-N and originally posted in Dutch on [the Delta-N website](https://www.delta-n.nl)

A real revolution in space technology is happening right now. SpaceX, the company of Elon Musk, accounts for 80 percent of the total mass brought into space. This number will rapidly increase to 90 percent. Many people are critical of Elon Musk. For this blog, we need to look beyond his ego and focus on how the engineers on the ground are working. In my opinion, SpaceX's success is due to their approach. Within DevOps, we can learn a lot from them. In this blog, I want to discuss the difference between NASA and SpaceX (both developing a new rocket) and what makes SpaceX so unique.

## The Space Launch System (SLS) Rocket from NASA

NASA is currently developing the [Space Launch System (SLS) rocket](https://www.nasa.gov/exploration/systems/sls/index.html). This rocket is expected to bring humans back to the moon within a few years. Artemis is the successor to the Apollo program from the 1960s and 70s. The SLS rocket is an evolution of the Space Shuttle that last flew in 2011 and uses revised and optimized components from the Space Shuttle. NASA receives its budget from the United States treasury under strict conditions. The money must be spent on subcontractors active in certain states to guarantee jobs, involving significant lobbying between senators. This rocket is for single use; after launch, the components burn up in the atmosphere or fall into the ocean. Making and launching four rockets costs about $41 billion. Because it involves public money, no mistakes are allowed, as this would result in negative publicity and reputational damage. The approach is waterfall, where the entire system is designed, built, inspected, tested, tested again, and tested once more. Due to the subcontractors, a lot of time is spent aligning the designs. This all feels safe, and it is necessary because people will soon be flying in the Orion capsule of the SLS rocket to the moon. NASA chooses 100% certainty: 'failure is not an option.' From the first launch, it must go perfectly.

{% include figure.liquid loading="eager" path="assets/img/sls.jpg" class="img-fluid rounded z-depth-0" %}

## The Starship Rocket from SpaceX

SpaceX is currently developing Starship. [Starship](https://www.spacex.com/vehicles/starship/) is the largest, heaviest, and most powerful rocket ever made. There is a significant difference with NASA’s SLS rocket: Starship will be fully and quickly reusable, like an airplane. Launch, land, refuel, and relaunch. This significantly reduces costs for bringing mass into space.

{% include figure.liquid loading="eager" path="assets/img/starship.jpg" class="img-fluid rounded z-depth-1" %}

At the time of writing, the second test rocket is ready for launch. This will undoubtedly be accompanied by a large explosion, or as SpaceX calls it, a 'Rapid Unscheduled Disassembly.' Our media will negatively highlight this as a rocket explosion, but for SpaceX, it is not a failure. For SpaceX, it is about collecting data and learning—learning a lot and learning quickly. Their feedback loop is short, and each component from their factory is an improvement on the previous version. Sometimes they take shortcuts. Where NASA’s slogan is 'failure is not an option,' SpaceX goes with 'Just send it.' Learning happens in practice. Practice is more valuable than theory. Stability and reliability are achieved by doing something frequently and thus eliminating all errors. This is similar to DevOps: deploying and releasing software. The more you do it, the better and faster you become.

SpaceX has gained experience with this approach by landing their Falcon 9 rocket. While the world said it was madness to land a returning rocket on Earth and relaunch it, SpaceX proved it could be done. The latest version of this rocket has landed successfully 225 times (out of 236 attempts). This rocket is the most reliable rocket ever made, and even the landing has a higher reliability than all other rockets in terms of launches. An impression of the first landing attempts is summarized in a hilarious YouTube video [‘How not to land an orbital class rocket booster.’](https://www.youtube.com/watch?v=bvim4rsNHkQ)

## SpaceX’s Innovation Algorithm

SpaceX uses an innovation algorithm consisting of five principles. Every engineer at SpaceX works according to these five principles, which can also be applied in the DevOps world. The principles are:

- Make the requirements less dumb
- Delete the part or process step
- Optimize
- Accelerate
- Automate

### Make the requirements less dumb

You must assume that the requirements you are told are always wrong. The question is, how wrong are they? You must question the requirements because continuous learning and new insights emerge. Primarily, it is about seeking the right question.

Where people collaborate on a complex whole, constraints around the requirements arise. In English, we call them 'constraints.' As an engineer, you must adhere to the constraints when realizing a part within a complex whole. Otherwise, it won’t align with a component someone else has created. Many organizations believe this is efficient and effective. It gives you less freedom in realizing a certain component both in process and actual result. This results in what we call 'local optimization.' You build the best you can but within these constraints. This does not mean the whole system works optimally.

At SpaceX, simply accepting a constraint as a constraint is out of the question. A constraint must always be challengeable because constraints increase the complexity of the whole. Experience shows that with constraints, often no one really knows why the constraint exists or was imposed by someone who has since left. You want to strive for 'global optimization' and want people to look beyond departments and individuals to jointly realize the best solution.

During software development, engineers must always be able to challenge the requirements. In practice, this often happens with product owners. Something is conceived, but a product owner is also just a human with biases/beliefs. Already in 2009, Microsoft proved with this [paper](http://ai.stanford.edu/~ronnyk/ExPThinkWeek2009Public.pdf) that 2/3 of all software developed has no or negative value. Impact mapping, feature mapping, and behavior-driven development can help in software development to ask the right questions, challenge the requirements, and uncover the right requirements. Data telemetry and observability can further support this by quickly learning from practice.

### Delete the part or process step

If the component or process step is not there at all, it saves time, money, and especially complexity.

SpaceX wants to fully reuse Starship, so the rocket must be able to land. Creating a landing gear is complex and adds extra weight. Therefore, SpaceX has completely removed the landing gear from the rocket. The rocket is caught in mid-air by two large grab arms. Imagine a 70-meter-tall and 9-meter-diameter building being caught from the air. The complexity is moved to the launch & landing tower, allowing the rocket to carry more cargo because the landing gear is no longer needed.

Engineers often make the mistake of optimizing or automating something that shouldn't exist at all. As an example, I want to mention Kubernetes. Kubernetes is a fantastic product with many great possibilities but can quickly become complex. If you don’t have scalability or availability problems for an application, why make it more complex? Kubernetes is extremely suitable for improving these quality aspects. If scalability and availability are not an issue for your application, don’t implement it and keep it simple. We engineers need a holistic view of the whole to oversee this. Good engineers are good at system thinking.

### Optimize

The optimize step is essentially repeating the steps of "make the requirements less dumb" and "delete the part of the process step" several times. How often? That can't be said. It is important to understand that our IT world is a complex whole. No one sees the whole picture, and complex systems are [always on the verge of failure.](https://www.adaptivecapacitylabs.com/HowComplexSystemsFail.pdf) The simpler the whole, the fewer components are used, and the higher the reliability. This creates a conflict for many engineers because it's often thought that the more tooling, the better. To make matters worse, complexity sells better, as [Edsger Dijkstra](https://www.cs.utexas.edu/users/EWD/transcriptions/EWD08xx/EWD896.html) once said.

Engineers often make the mistake of optimizing or automating something that shouldn't exist in the first place. As an example, I would like to mention Kubernetes. Kubernetes is a fantastic product with many great features but can quickly become complex. If you don't have scalability or availability issues for an application, why make the whole thing more complex? Kubernetes is ideally suited to improve precisely these quality aspects. If scalability and availability are not a problem for your application, don't implement it and keep it simple. We engineers need a holistic view of the whole to oversee this. Good engineers are good at [system thinking.](https://en.wikipedia.org/wiki/Systems_thinking)

### Accelerate

After iterating through the whole and optimizing it several times, then go faster. By going faster, you remove errors from the process or product. You discover weaknesses or identify which elements need attention. By accelerating, you make new discoveries.

This aligns with the saying "if it hurts, do it more often." You can always go faster, but be careful not to go too fast, as this could create unacceptable risks, especially when there is potential for reputational, financial, or even life-and-death damage.

I've experienced my team being under pressure to deliver several features by a certain date. To deliver faster, we decided to spend less time testing. We accepted the risk that our customers might encounter errors. The result? It was quite manageable; yes, there were some complaints, yes, we saw strange situations in the logs, but ultimately we resolved them quickly. We realized that we were structurally over-testing and that our test coverage could be reduced to deliver faster. This led to a revision of the measures we took as a team regarding risk mitigations.

### Automate

Only after performing the first four steps do you start automating. Automating helps you go even faster and standardize. You streamline the process further. You only automate last because that's when you can efficiently and effectively automate the whole.

## The Relationship with DevOps Way of Working and the Pitfall of 'Automate Everything'

I often encounter teams that follow the "automate everything" principle. I think it's not a good idea because starting with automation can increase inefficiency. Bill Gates said this years ago:

> "The first rule of any technology used in a business is that automation applied to an efficient operation will magnify the efficiency.
> The second is that automation applied to an inefficient operation will magnify the inefficiency." - Bill Gates

In our DevOps world, we engineers should more often reflect on the principles of SpaceX and be less quick to jump on the automation and tooling hype. It makes sense because most job openings don't ask for optimization. They ask for tooling and thus automation knowledge.

In my opinion, a good engineer has a holistic view of the whole, asks critical questions, and always looks at whether complexity can be reduced to create a more reliable and cheaper whole. Let's look more at how SpaceX develops rockets. If they can do it with rockets, we should be able to do it with our software too.

In which rocket would you like to take a trip around the moon? A rocket that has been thoroughly tested on paper and on the ground and has flown only a handful of times, or a rocket that initially exploded often and from which all weaknesses were removed through a fast learning process?
