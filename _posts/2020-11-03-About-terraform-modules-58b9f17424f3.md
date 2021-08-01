---
layout: post
title: About terraform modules
subtitle: >-
  If you’re reading this then I guess you’re in the middle of refactoring your
  terraform code...
date: '2020-11-03T09:26:41.000Z'
thumbnail-img: /assets/img/terraform.png
categories: []
keywords: []
tags: [Terraform, DevOps]
---

If you’re reading this then I guess you’re in the middle of refactoring your terraform code, trying to make it reusable, easier to maintain and actually be proud enough to share it with others! Writing reusable code in the form of modules can be a challenging task, so I will try to explain some basics below.

Engineers working with terraform sometimes are not able to write reusable code and understand the whole plumbing mechanism. Personally, I sometimes blame automation and the clouds since they are hiding what is under the hood (and well… they make things too easy sometimes)! For example, “How do you turn on the lights”. Most people will answer flip the switch, some might have an idea of how the electricity comes in tubes and the switch enables the connection, but chances are only a select few will even go and talk about ac or dc. I believe that something similar is happening with terraform.

Before terraform, infrastructure automation was something you would be doing with your own code, and this is challenging! It is challenging because most of the time you would be using languages like python, bash etc. in an imperative way. That means your code had logic and steps you had to follow to reach your goal. But code is difficult to understand, and you need to build your skills around it as well.

**Declarative Programming**

However, with terraform, things are now more ‘straightforward’ in a way! They are, because someone (Mitchell Hashimoto — Hashicorp) decided to abstract the ‘custom logic’ I mentioned earlier and provide you the tools to build infrastructure without having to worry about any underlying complexities (code, state, apis etc.). Infrastructure deployment became something like playing with Legos, and this is a changing mindset. Something we need to consider and one of the main differences between present vs past is that terraform configuration language is a declarative language! Quoting from the official documentation:

**_The Terraform language is declarative, describing an intended goal rather than the steps to reach that goal._**

**_What does this mean?_**

**Declarative programming** is a programming paradigm-a style of building the structure and elements of computer programs-that expresses the logic of a computation without describing its control flow. _(source: Wikipedia)_

Why should you care about this? Because you should start writing code using building blocks — modules and you should stop writing code as it was e.g. python, bash etc. Actually, in terraform everything is supposed to be modules, and everything is connected! Let’s take it a step further:

**_A Terraform configuration consists of a root module, where evaluation begins, along with a tree of child modules created when one module calls another._** **Why Modules**

When you started working with terraform you might find it easy to start with its common files like and define your infrastructure there. Especially in cases when your infrastructure was simple at the beginning you could easily fall into that “will fix it later” trap. Usually we (infrastructure, operation and reliability engineers) work against the clock, especially during outages, and usually this means that you will not be able to spend much time refactoring code in the future. Quality and best practices should be built in your code at the moment of writing. Let me try and explain why this is important:

In the long run (or in the short run as well!), your infrastructure will grow, and things will become complicated. When this time comes you will have to scale your infrastructure. Scalability is one of the most common and challenging problems engineers face on a daily basis. Now, if your codebase is intricate for no reason, or you cannot reuse your code, you will not be able to maintain it and actually use it easily. That means you and your team will have to spend a lot of time to first understand the code, then change it, test it and then deploy it to prod. (yes these things should happen automatically through a CI/CD pipeline, but you will still have to wait!). i.e. This is an overhead for everyone, starting from the engineers and finishing with your end users/customers.

**Defining the Module**

Anyway, I’ve used the **module** buzzword a lot of times already. Let’s try and define it? One definition could be the following:

**_Module is a set of code files put together into a folder which you can move across projects and reuse._**

Might be that simple. However, a better definition might be:

A terraform module is what a library would be in any other programming language. A library contains functions and functions have inputs, outputs and “tightly coupled” code. Using a library means you can use its functions in your programme without any modifications. You can also use the same code in other programmes without any changes and the code would always return the same results.

![](/assets/img/0__l01WlzMiZx9wUTez.png)

Well, the same goes for terraform modules. In a module you’re describing the building blocks of your infrastructure e.g. an ec2 instance (function — f). To configure the instance, you need to have some values (inputs — x) which will e.g. determine whether the instance will have a public-ip attached at boot time. When this function returns you will have an up and running instance (output — f(x)) configured with some specific values (disk size, flavor etc). It is that simple and that complicated as well! In an attempt to make it crystal clear let’s have a look at a simple building block and how we can eventually create a module out of it.

_(We will use some basic AWS terminology and I assume you have at least some basic terraform experience.)_

**Example**

The following file structure would create a simple module to create AWS ec-2 instances (remember the definition — **_module is a set of code files put together into a folder_**):

![](/assets/img/0__osOpLszPvFvYz__EK.png)

The main.tf here would contain our module (function) definitions.

**Main.tf**

![](/assets/img/0____aRETfbBhcL3qX6w.png)

To use a function though, we need to define inputs and outputs. The variables.tf will act as our inputs/parameters file:

**Variables.tf**

![](/assets/img/0__cEoVQf90qN5lfAp3.png)

and the output.tf will describe what this function returns!

![](/assets/img/0__o70tztzncWJHVcy0.png)

We now have a simple module with the name **compute**, which we can use to create ec2 instances. The tricky part we need to understand here is that the outputs of a module/function can (and will) be the inputs of another module/function. Let’s remember what we said at the beginning of this article:

**_A Terraform configuration consists of a root module, where evaluation begins, along with a tree of child modules created when one module calls another._**

Our infrastructure is actually a chain of modules where outputs of one module become inputs/variables for another. What we did at the moment was to generate a **child** module. The child module now needs a parent. This parent is going to be the **root** module since the evaluation process is happening at the root module. Let’s assume the root module is already there with the name main.yml and it already contains e.g. the description about our AWS provider and the subnets details. The last step to start using our module would be to actually define it in our code, instantiate it (similar to what we would be doing if we were to use a function in any other language):

The main.tf is going to re-use the code of our compute module.

**main.tf**

![](/assets/img/0__YZEqBRkrVbOrNXVh.png)

We just need to define our code in the main.yml

**module definition in the main.yml**

![](/assets/img/0__EcdLozDRaXCSwah__.png)

### Thanks

Thanks for reading and hopefully you now have a better understanding on terraform modules!

_p.s. Special thanks to Goncalo Barroso for reviewing the article and make it an easier read for everyone!_

_Originally published at_ [_https://automationlogic.com_](https://automationlogic.com/about-terraform-modules-an-explainer-by-george-tarnaras/) _on November 3, 2020._