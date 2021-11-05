---
title: Using Zero Days for Red Teams
author: jp
type: post
date: 2021-11-05T20:37:56+00:00
url: /2021/11/05/using-zero-days-for-red-teams/
categories:
  - red team
---

**What do you think when you hear the term zero day?**

Most of us think of high dollar zero days that organizations such as Zerodium peddle. These types of zero days represent a minuscule amount of the zero days that one: currently exist, and two: are being released every day.

FireEye classifies a zero day as “an unknown exploit in the wild that exposes a vulnerability in software or hardware”. The vast majority of software is not Microsoft Windows, OSx, Android, Chrome or the litany of other popular software. 

Pick any organization with at least several hundred employees. Chances are, they use obscure software, or internally developed software. And chances are, that software at some point, somewhere, sits on the perimeter. 

A topic of contention in the Red Team community is whether or not a Red Team would “burn an O-day”,  or “drop tens/hundreds of thousands for an O-day” during an operation. If the understanding is that when we say zero days, we are speaking about those types of vulnerabilities that Zerodium is interested in, then we have a very valid argument. In fact, I would say you can count on a couple hands the amount of organizations that need that in their threat model. And even those organizations don’t necessarily need to burn a valuable zero day to simulate this type of attack.

Now if we consider zero days in a dulled lens, that is, an unknown vulnerability for any piece of code in our environment, it seems like something we should really consider. 

Does your blue team know how to investigate a compromise when the shell magically appeared on a server? 

When software has a known vulnerability, we typically have published artifacts and IOC’s. When that piece of software has no known vulnerabilities, but was somehow compromised, does the blue team have processes to handle that? 

As a Red Team, it is your responsibility to determine if using zero days can be a valuable training opportunity for your stakeholders. And in many cases, they don’t need a ton of R&D time. Spend some time on an internally developed application, or the software running some obscure physical security hardware, for example. 

Training your blue team to be ready to face the unknown unknowns is what you are here for. 

Spar with them, make them better. 

https://www.fireeye.com/current-threats/what-is-a-zero-day-exploit.html

https://debricked.com/blog/2019/06/17/vulnerabilities-in-dependencies/


