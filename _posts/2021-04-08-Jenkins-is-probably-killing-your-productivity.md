---
layout: post
title: Jenkins is probably killing your productivity
subtitle: >-
  Time to say goodbye.
date: '2021-04-08T00:12:31.531Z'
thumbnail-img: /assets/img/old_jenkins.jpg
categories: []
keywords: []
tags: [Jenkins, DevOps, CI/CD]
readtime: true
---

### Jenkins =! DevOps

Let's start by clarifying that Jenkins is not a DevOps synonym and DevOps does not mean Jenkins. The fact that you started using Jenkins a while ago (or you're now probably stuck with it) does not mean you are doing "DevOps". As many other fellow engineers have mentioned out there:

*DevOps is a philosophy, a set of practices. You're not DevOps, you're doing DevOps.*

However, the purpose of this post is not to talk about DevOps, per se, but how being stuck with Jenkins can eventually actually kill your teams' productivity and their DevOps spirit.

### The "pre & post" Jenkins period

I am pretty sure you have heard something similar in your organisation or dev team:

"**We**'re a DevOps team that uses Jenkins based CI/CD pipelines..."

"Using Jenkins **we** promoted self-service features for our teams..."

"**We** started using Jenkins to build our product fast..."


I have highlighted the **we** in the phrases above. I did, because most probably the "we" was not your dev team but your *Jenkins expert*, and here is the first problem. How many people in your team actually know how to use Jenkins and are able to write pipelines without having to ask "*the Jenkins expert*" for help?

This tool started **10+ years ago**, mostly for building/compiling Java applications. Since then, the whole CI/CD ecosystem has evolved dramatically and many other CI/CD tools were developed and are now mature for production usage. However, not many organisations or teams made or even planning a move to a different CI system. Why?

### Jenkins is now a legacy system

It might not be 30 years old but considering how fast the software ecosystem is moving, Jenkins is now an old tool... and here comes the usual story that often applies to our beloved legacy systems:

- If it works, don't you dare touch it.
- Everyone is complaining when it stops working. No one really knows how to fix it.
- It is just too difficult and complicated to move away from it.
- You just love it!


After all, you have probably invested **tons** of hours to configure Jenkins, write the pipelines, scale it, secure it, maintain it and of course choose the right plugins! Hundreds of fancy plugins that someone will be tempted to install... And once everything is up an running you will realise that your code and Jenkinsfiles are now plugin specific or you need a newer Java version installed and so on and so fourth... All these mean just one thing.

**You're wasting a lot of valuable time and energy.**

You and your team spend more time building expertise around Jenkins. Hours and hours are spent to configure and maintain the tool instead of writing code to e.g. automate tests, collaborate with other teams or just think how to improve things. It is notorious difficult to scale Jenkins, fully automate its deployment and configuration, do a failover, deal with its JVM memory issues, or even write a simple pipeline.

You need to be able to find your way around with Groovy but still, you will have to spend a lot of time to test your code and make it work as you wish. Oh, and probably every team will want its own Jenkins instance to have more freedom to develop and test their code, so more maintenance work on the way! I believe [this](https://www.reddit.com/r/devops/comments/jgodgb/jenkins_help_to_understand) reddit post is self-explanatory:

<p align="center">
  <img src="/assets/img/jenkins_complexity.png" alt="https://www.reddit.com/r/devops/comments/jgodgb/jenkins_help_to_understand">
</p>


### If not Jenkins then what?

 You CI system should not have any unnecessary complexities and it should be easy to work with.

* Your pipelines have to be tested exactly as you're testing your code.
* You need to have separate stages for dev/test/prod.
* Your devs have to be able to experiment with the code they are writing.
* Restoring your configuration after a failure should **not** require any effort.
* You should not have to open 10 new tabs and wait hours and hours to load build results.
* You probably do not need so many plugins...
* CI tools should be fun to work with (bring the fun back)!
* A UI usually implies manual work but CI pipelines should be **fully** automated.

In short, Jenkins has served its purpose and it was a really great tool some years ago... but nowadays it is just too cumbersome. There are way better tools to help you hit the ground running with your CI system.

### Framework vs Plugins based approach

Solutions like Gitlab CI or Github Actions allow you to start writing pipelines in minutes and in languages that are extremely easy to work with (yml, shell or python scripts etc). Adopting these tools will help you have **a framework based approach** for developing your CI system instead of a plugins based solution. They integrate far better with technologies like Docker and it is way more simple to interact with their APIs in order to develop automation around them.

A CI system should help you build your code fast and reliable so that you are able to release it without surprises. That's where the DevOps philosophy comes into play and Jenkins does not seem able to keep up anymore.

p.s.1

If you're still not convinced then get a stopwatch and compare the time it takes to write a simple "hello world" pipeline using Jenkins and Github actions...

p.s.2

I did not even mention that Jenkins is often used as a CD tool as well... Will try to write a new article just about CD tools.