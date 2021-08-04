## Jenkins is probably killing your productivity

### Jenkins =! DevOps

Let's start by clarifying some things. Jenkins is not a DevOps synonym and DevOps does not mean Jenkins. The fact that you started using Jenkins (or you're probably stuck with it) does not make you are doing "DevOps". As many other fellow engineers have mentioned out there: 

*DevOps is something more like a philosophy and a set of practices. You're not DevOps but you're doing DevOps.


 However, the purpose of this post is not to talk about DevOps but how being stuck with Jenkins can actually kill your teams productivity and eventually **doing** DevOps.

### The "before and after" period

I often hear people talking about Jenkins and mentioning the things below:

"Before Jenkins **we** used to do things like manually, now everything is clear..."

"Using Jenkins **we** promoted self-service for our teams..."


etc.

BUT 

I am pretty sure you have heard something similar in your organisation or team. 
The **we** in the phrases above though was probably not your dev team rather than  your *Jenkins expert*, and this is where the problems start. How many people in your team actually know how to use Jenkins and are able to write pipelines without having to ask "the Jenkins expert" for help?

This tool started **10+ years ago**, mostly for building Java applications. Since then, the whole CI/CD ecosystem has evolved dramatically and many other CI/CD tools were developed. However, not many organisations or teams made or plan to move to a different CI system. 

Why? 

### Jenkins is now a legacy system

... and here comes the known story about legacy stuff. 

- No one wants to touch something that usually works.
- Everyone is complaining when it stops working and no one really knows how to fix them.
- It is just too difficult and complicated to move away from it.
- No one is **actually** using them.
- I still love you my old system!


Afterall, you have probably invested **tons** of hours to configure Jenkins, write the pipelines, scale it, secure it, maintain it and of course choose the right plugins! Hundreds of fancy plugins that someone has to install... And once everything is up an running you will realise that your code and Jenkinsfiles are now plugin specific or you need Java installed and so on... All this means just one thing.

**You're wasting a lot of valuable time and effort.**

Your team spends more time building expertise around Jenkins to configure and maintaine it instead of writing code to e.g. automate tests, collaborate with other teams or just think. It is notorious difficult to scale Jenkins, do a failover, deal with its JVM memory issues, or even write a simple pipeline. You need to be able to find your way around with Groovy and still spend a lot of time to test your code. Oh, and probably every team wants to have its own Jenkins instance, so more maintenance work on the way!

### What's next then?

 You CI system should be accesible and easy to work with.

* Your pipelines have to be tested exactly as you're testing your code. 
* You need to have seperate stages for dev/test/prod.
* Your devs have to be able to experiment with the code they are writing.
* Restoring your configuration after a failure should **not** require any effort.
* You should not open 10 new tabs and wait that long log to load to get back the build results.
* You probably do not need so many plugins...
* CI tools should be fun to work with, bring the fun back!
* A UI usually implies manual work and CI pipelines should be fully automated.

In short, Jenkins has served its purpose well and it was a really great tool some years ago but nowadays it is just too cumbersome. There are way better tools out there to help you hit the fround running with your CI system. 

### Framework vs Plugins based approach

Solutions like Gitlab CI or Github Actions allow you to start writing pipelines in minutes and in languages that are extremely easy to work with (yml files, scripts etc). Adopting these tools will help you have **a framework based approach** for building your CI automation instead of a plugins based solution. They integrate far better with technologies like Docker and it is way more simple to interact with their APIs in order to develop automation.  

A CI system should help you build your code fast and reliable so that you are able to release it without surprises. That's where the DevOps philosophy comes into play and Jenkins does not seem able to keep up anymore. 