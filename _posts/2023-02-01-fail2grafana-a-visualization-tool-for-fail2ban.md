---
layout: post
title: Some thoughts on software and data security 
subtitle: >-
  Running a honeypot server for a month 
date: '2023-1-06T15:20:31.531Z'
thumbnail-img: /assets/img/rusty-lock.jpg
categories: []
keywords: []
tags: [fail2ban, devops, vps, security, ci/cd, docker, containers, ssh, grafana, devsecops]
readtime: true
---

# Running a Honeypot Server for a Month

I recently tried to experiment a bit with server security. What other better way of doing this other than spinning up a VPS server and convert it into a honeypot! After a month running the server, a lot of things changed in my mind around security and how we should deploy, build and configure infrastructure (and processes around that). 

### Show me the results amigo

Around 5,000 individuals or machines (we are in the AI era, after all) displayed an "interest" in this publicly-exposed server

<p align="center">
  <img src="/assets/img/honeypot1.png">
</p>

<p align="center">
  <img src="/assets/img/honeypot2.png">
</p>

For reference, the dashboards were created using `fail2ban` and a tool called `failed2grafana` for which I will have a repo sometime soon (yeah, WIP).

### So what?

If a random person's server on the internet receives this much traffic, you can imagine what happens with companies that host valuable data. My guess is that financial and healthcare sector companies are at the top of the list.

Over the last decade, the tech sector has experienced a major boom, with SaaS and PaaS solutions now ubiquitous. Unfortunately, it seems that little attention has been paid to the security aspect of these developments and the potential cost of security issues to businesses. Even GDPR, a widely-known regulation, only became enforceable in late 2018. As more sophisticated tools become widely available, cyber attacks are expected to become more advanced and aggressive over time. A quick internet search will reveal interesting reports and statistics on cyber security. (I'll leave that to you!)

Speaking of data, unless you live on Mars (greetings to Elon from Earth), chances are that your data is already scattered across numerous places and data centers. From your local food delivery service to credit card companies, all of this information is expected to be securely stored on remote servers. If you were to host your data (of any kind) somewhere, would you choose a provider that is regularly hacked and has loose security measures in place?

In the software world, we spend quite some time to improve processes and teams in order to move fast. Engineers, scrum masters, product managers, customers - all of them want the next feature - fast! The problem with that?

Security is often ignored or neglected when teams try to move fast or when an aggressive deadline is approaching. In addition, with the rise of cloud platforms, new engineers often do not get any exposure to security and infrastructure best practices since they never had to run a server or service on their own. 

Living in managed world and this level of abstraction obviously has its pros and cons but secure and securing software starts from the very early phases of the SDLC. Not knowing the risks of design decisions can impose significant risks to an organization and potentially damage its reputation. I believe moving forward and in the next few years, a security-first-culture and well-thought-out processes and measures that shape security are needed more than ever. 

Now, some companies already rushed to call this DevSecOps but we never managed to define what DevOps means so I'd rather keep security built-into every aspect of our work!

Preparing for a security breach is more important than simply hoping to avoid one. Inevitably, every organization will face attempted hacks and data breaches. Therefore, it is crucial to have robust security measures in place to minimise the impact of such incidents.  

When a security incident does occur, the organization must be prepared to respond quickly and effectively to contain the damage. By taking a proactive approach to security, companies can build a robust and resilient security posture that can weather any attack with confidence and minimal impact. Good luck!