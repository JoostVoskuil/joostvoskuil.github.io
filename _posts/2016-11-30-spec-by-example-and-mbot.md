---
layout: post
title: Serious Gaming – Learn Specification by Example with the mBot
date: 2024-11-30 23:36:10
description: This is a little story about my journey as a tester
#tags: Testing, SpecificationByExample,Sbe,BDD
categories: Testing, BDD, SbE
typograms: false
---

{% include figure.liquid loading="eager" path="assets/img/mbot.jpg" class="img-fluid rounded z-depth-0" %}

> This blogpost was writen for Bartosz and originally posted in Dutch on [the Bartosz website](https://www.bartosz.nl)

One of our colleagues discovered the mBot some time ago to teach his son how to program. It turns out that this mBot is not only interesting for children. This funny little robot is excellent for playfully mastering 'Specification by Example.'

## mBot

The mBot is a small robot that is highly customizable and equipped with various sensors and motors to drive the wheels. What makes the mBot unique is the combination of hardware and software. The hardware is based on the open-source hardware platform Arduino and can be expanded. With the Scratch application, you can visually program by dragging code blocks and creating a routine around the robot's behavior. The customizability of the robot and the price, around 80 euros, make it an attractive package. The mBot project was originally intended for education to teach children programming. The manufacturer's slogan is therefore 'one robot per kid.'

## Specification by Example

We see that Specification by Example (SbE) or Behavior Driven Development (BDD) is increasingly adopted by agile teams. Traditionally, a lot of information is transferred during the specification, building, and testing of software. From business, to analysts, to developers, to testers, who then report results back to the business. This process at least does not contribute to clear specifications, code, and tests. Besides, have you ever caught yourself using examples to explain something, providing concrete handles and clarity to abstract concepts?

That’s why we shape our specifications in examples. These examples can be seen as executable specifications that validate and verify requirements and give new meaning to test automation. By describing these examples in Gherkin syntax: 'Given, When, Then,' we can describe system behavior in a way that is clear to all involved.

## The Combination and Statement

Soon, the idea arose within Bartosz to combine Specification by Example and playing with the mBot. A serious learning moment with lots of fun. The behavior of the mBot is central; based on the sensors, a status can be determined, and based on that status, the robot should exhibit certain behavior. Additionally, the robot is just a lot of fun to play with, it is tangible, and it reacts immediately.

We also wanted to make a statement, namely the misconception that Specification by Example or Behavior Driven Development has something to do with testing or test automation. The shared vision, the discussions, the uniform language, and its tight scenarios naturally contribute to the quality of the application. However, we should not view the examples as tests but as specifications that drive the development of the application and thus become executable specifications. Nevertheless, the application can still contain errors or, with the speed of current software development, the just implemented feature can immediately provide new insights. We see facilitating this as the new role of the tester; learning, discovering, and exploring possibilities.

## Workshop 'Specification by Example with Robots'

For various clients and during the TestNet Summer Workshops, we organized the workshop 'Specification by Example with Robots.' In this workshop, you learn to model the behavior of a robot using Specification by Example. With these examples in hand, you program the robot with the goal of completing a course without errors in the shortest possible time!

The workshop begins with a theoretical introduction to Specification by Example and more insight into what the mBot is and can do. Then the practical part begins. We set up a course with a zigzag line, a right-angled turn to the right, a right-angled turn to the left, with walls and a garage where the mBot must come to a stop. The mBot has sensors that should follow the line, but you cannot blindly rely on them. The behavior to be exhibited at the wall and garage are different patterns. The ultimate goal is to program the robot to navigate the course flawlessly in the shortest time! But we only start implementing the code once we have a shared vision of what needs to be implemented. In groups of 4 to 5 people, the examples are worked out. The groups usually quickly conclude that mentioning sensor data in the scenarios is not the intention. It's about business value and describing the desired behavior of the robot. Intensive collaboration ensues to use a common language within the team, which is the intention of Specification by Example or Behavior Driven Development. These increase synergy within the team and involvement with the business. Thus, the right product can be built in one go.

After thinking through the scenarios, we proceed with implementing the Scratch code based on the examples, to then combine everything into one whole. This is where the big 'fun factor' of this workshop begins. We've seen quite a few robots perform the craziest capers.

We look back with pleasure at the Specification by Example with Robots workshops organized so far. Clients experience the workshop as very educational and have a fun afternoon together.

## The Next Steps

Of course, at Bartosz, we are not sitting still. After the success at TestNet, we decided to further expand the workshop. The next version of the workshop will be more realistic with what we, as test consultants, do on our assignments: Executable specifications are created from the examples via Cucumber and Java. Consciously or unconsciously, participants will learn concepts such as Dependency Injection and Mocking and the fact that you can leave out the mBot while programming the behavior. Not just specifications but true outside-in development and a proper setup of the test automation pyramid.
