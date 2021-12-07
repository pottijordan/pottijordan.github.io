---
title: The Great Mental Models and Information Security
author: jp
type: post
date: 2021-12-06T20:37:56+00:00
url: /2021/12/06/the-great-mental-models-and-information-security/
categories:
  - ramblings
---

As a regular reader of the Farnam Street project, I’ve come to appreciate mental models and how they can help us make decisions. In regards to the information security industry, since we are still likely in the “pre-Galilean” period, it suffices to say that applying mental models to the way we think about the problems we face could greatly benefit us. 

According to Farnam Street, “There is no system that can prepare us for all risks”, “But being able to draw on a repertoire of mental models can help us minimize risk by understanding the forces that are at play”.

It’d be hard for anyone to argue that applying mental models to Information Security is a bad idea. Applying mental models to any industry, or set of problems can only result in greater understanding. That being said, I would go a step further in that by *not* applying mental models to the Information Security industry, we may be causing more harm than good. 

Galileo’s Ship is an analogy that explains that when we are part of a system, we have a hard time seeing new solutions and understanding problems. Picture this, you are a fish and you are watching a ship moving at a constant velocity, a scientist drops a ball while standing on the deck of the ship. To the scientist, the ball simply drops straight down. To the fish, the ball moves horizontally at the speed of the ship as it falls. 

As an Information Security practitioner, we may not have a good understanding of the problems at hand. By leveraging mental models, we can think clearer and with fewer biases about the problems we face. 


**The Models:**

1. The Map is Not the Territory
2. Circle of Competence
3. First Principles Thinking
4. Thought Experiment
5. Second-Order Thinking
6. Probabilistic Thinking
7. Inversion
8. Occams Razor
9. Hanlon's Razor

[**The Map is Not the Territory**](https://fs.blog/map-and-territory/)

A map shows us how to get from point A, to point B. It shows contour lines, roads, and many other static data points. And in physical navigation, this works well. Roads typically don’t change once built. New cliffs or rivers don’t appear overnight. However, it's still not perfect. A map doesn’t show the weather that you will be traveling through, it doesn’t tell us the best shoes to wear for the terrain, or what tires work well in the mountain passes. It doesn’t show how much deadfall you’ll have to climb over if you’re hiking, or if a bear is on the trail. It’s simply static data that acts as an assistant. 

Information Security has many such “maps”. Frameworks, career roadmaps, standards, and maturity models all provide map-like assistance to getting from point A to point B. However, each step in any given model could have its own map, and there likely is. Those are the array of footnotes for each step in implementing a framework.  And those consultants or employees, those are the guides we hire to take us from point A to point B, map or no. 

This leads us to our next Mental Model. In this case, we need to hire a guide even though we have a map. It's like climbing Mount Everest. You can Google the best route, and even obtain highly detailed maps which contain elevations, hazards, camp locations, and a myriad of other details. However, you likely need to hire a guide. Someone who knows the terrain, someone who’s considered a “local”, someone whose Circle of Competence, includes climbing Mount Everest. 

[**Circle of Competence**](https://fs.blog/circle-of-competence/)

Your Circle of Competence is pretty simple. It's what you’re good at, what's familiar to you, what you can talk all day about. Ideally, it's something you enjoy. That being said, no matter how good you are at something, nothing replaces experience. Building a Circle of Competence takes time and practice.

One thing that stuck with me was something one of my first bosses in information security told me after asking for a promotion I thought was well deserved. He said, “Yes, you do operate technically at a senior level, but nothing can replace cold hard experience, and that’s something you don’t have”. I have a feeling that many people in today's culture will have a problem with that, but there is truth to it. There may be exceptions to that, but I wasn’t one of them. 

Information Security cannot be your Circle of Competence. 

It’d be like saying airplanes are your Circle of Competence. What kind? Flying them? Fixing them? Jets? Prop planes?  Aerodynamics? Engineering? Warfare? 

There is too much to learn, the domain is simply too wide.

So how do you find your Circle Of Competence? That’s sort of like asking “What do I want to be when I grow up?”. You find it by trial and error. Hopefully, educated trial and error. For example, if you don’t like heights, maybe don’t try and be a windmill repairman. 

Moving ahead to where you found something you love, double down on it. If you love web application security, or reverse engineering, or JavaScript, make that your own. After several years of focus, you will have an established Circle Of Competence. 

But be careful, this isn’t an end goal. A Circle of Competence isn’t something you just attain. You must continually improve on it. [The Goldilocks Rule](https://jamesclear.com/goldilocks-rule) states that humans experience peak motivation when working on tasks that are right on the edge of their current abilities. So continue pushing the boundaries of your circle. 

[**First Principles Thinking**](https://fs.blog/first-principles/)

Have you ever had a boss that wouldn’t buy into your ideas unless they saw it done before? There is a reason why social proof is such an effective sales strategy. [Social proof is one of the most powerful negotiating techniques](https://www.negotiationtraining.com.au/articles/encourage-social-proof/), however, you’ll [never innovate if it’s the only method by which you make decisions.](https://fs.blog/mental-model-social-proof/)  

Reasoning with First Principles is a much better alternative. By unpacking the fundamental rules of whatever it is you’re considering, you open up countless novel paths. 

Red Teaming, Vulnerability management, Security Operations, GRC, Security Architecture. What do they all have in common? First Principle Thinking allows us to step back, and consider the core reason why we are all here. Considering the “Five-Whys” might help you get to the First Principles sooner. 

Why do we have a Red Team? To identify attack paths.
Why do we need to identify attack paths? So that we can close them before an attacker exploits them.
Why do we need to close the attack path before attackers exploit them? To prevent a breach.
Why do we need to prevent a breach? To protect our IP/PII/PCI/etc.
Why do we need to protect that data? To keep our customer's trust and stay out of financial and legal trouble. 

So according to the Five Whys, you could say a First Principle of InfoSec is revenue protection. 

Another First Principle of InfoSec is that we are here to reduce the probability of breaches at a cost that the business is willing to pay. It's pretty simple. While we all play different roles at that, we can’t lose sight of the First Principles. 

What are your own First Principles?

Simon Sinek coined this as Find your Why. Victor Frankl calls it Logo Therapy. It's also important to note that determining the First Principle of why you do something, is not always in line with why someone pays you to do something. You should be aware of both. 

[**Thought Experiment**](https://fs.blog/thought-experiment/)

Nothing is original. However, just because something has been tried before, and failed, does not mean that it will fail if you try it. Cyber Security is not a “solved” industry. We need Thought Experiments, which inevitably lead to innovation. 

John Kindervag, the pioneer of Zero Trust, was likely playing around with a Thought Experiment when he came up with the idea. 

It could have gone something like this: “What would happen, if instead of trusting a user or device based on a static secret, what if we continually re-assessed our trust relationship? And used many different components, not just a username and password.”

Before we can do anything with that thought experiment, we need to try and prove it wrong. Why is it possible? Why is it impossible?

John Kindervag likely decided that yes, this concept is possible. The technology exists to do this, and hence, Zero Trust was born.

[**Second-Order Thinking**](https://fs.blog/second-order-thinking)

How many people do you know that owe payments on most of what they own? While it's possible they’ve never considered the difference between an asset and a liability, it's also likely that they’ve never thought beyond the monthly payment. Second-Order Thinking forces us to consider what comes after the initial result of a decision. 

So consider what happens after you sign on the dotted line. Now you have a monthly payment, you need somewhere to store the thing, you need to maintain and winterize the thing, and instead of the desired result of having a fun toy, you only have less time and less money. 

How many of us have written a report, or an email, or a Slack message with a message of fist meet face. We’ve had enough, the security of this company will not suffer because this department refuses to comply. I bet many of us have been there, and I bet it even works most times the first time. 

Second-Order thinking would have likely led us to consider but then what? 

What will that team do the next time they face a security decision? You’ve just incentivized them to minimize their relationship with security. The next time they have to make a security decision (or not), they may no longer plan on including security if they can help it. 

[**Probabilistic Thinking**](https://fs.blog/probabilistic-thinking/)

Risk is a big industry. It's a subset of InfoSec as a whole, and yet, it *is* InfoSec. And no one really understands it. It might be because those that don’t understand it don’t think about it, and those that do think about, are thinking about it too hard. 

InfoSec is full of fat tails. It's impossible to guess when and how we will get breached, let alone, which vulnerabilities the threat will use. For example, if we consider the risk of a breach, how likely is it that a breach introduces an existential risk? Or how likely is it that a breach causes moderate financial impact?

Shane Parish with Farnam Street says it much better than I could:


> The important thing is not to sit down and imagine every possible scenario in the tail (by definition, it is impossible) but to deal with fat-tailed domains in the correct way: by positioning ourselves to survive or even benefit from the wildly unpredictable future, by being the only ones thinking correctly and planning for a world we don’t fully understand.

I’ve written about this before but I think it's worth thinking about this succinctly.

While we can’t predict accurately how likely a vulnerability will lead to a breach, we can continually plan and prepare for the likelihood that a breach will occur. As Shane Parish wrote, we need to be “positioning ourselves to survive”.

On this subject, I’d love to see some research around metaprobability in regards to various risk scoring frameworks. My hypothesis is that is not very accurate. 

[**Inversion**](https://fs.blog/inversion/) 

This is similar to [Premortem Analysis](https://hbr.org/2007/09/performing-a-project-premortem). 

Inversion lets us think hypothetically about exactly what we don’t want, and then we consider how we got there. Let’s say you want to run a world-class security organization. Inversion doesn’t work really well at a high level, but if you break down the problem into a small set of problems, you can start to see the value of Inversion. 

Start with your goal, lets say you want everyone in your organization to feel like they own security for their own domains. We know it’s not an ideal situation when the organization relies solely on “the security team” when it comes to security ownership. Using Inversion, you can consider how you could get to a place you don’t want to be, and quickly. 

To begin, you would make sure all security decisions are routed through your org. You would position yourself as the gatekeeper of all things security-related. You would set security policies that would be enforced regardless of business requirements. If various teams can’t fulfill some requirements, you would handle an exception process for them. When you do end up in meetings with other teams, you would be patronizing when security topics are discussed. 

Now that we’ve identified the things you can do to get to an undesirable state, we now know what to avoid, or at least be careful with. 

[**Occams Razor**](https://fs.blog/occams-razor/)

Occams Razor argues that the simplest answer is usually correct. How will an attacker breach your organization? It’s unlikely that it will be a zero-day, and it’ll likely be a spear phish, or an unpatched server, or some other obvious weakness. This leads us to the belief that doing the ordinary security work well, will go much further than chasing the latest hotness.

Worrying about zero-days is a bit like running down the middle of a busy highway and being worried about getting struck by lightning. 

People crave novelty. Those latest zero-days and new TTP’s get the most attention. 

Many of those organizations worried about the latest threats aren’t doing the boring stuff yet. No whitelisting, no host firewalls, lacking asset management, and the list goes on and on. 

Jim Alkove, the Chief Trust Officer at SalesForce calls this “doing the common uncommonly well”.

[**Hanlon's Razor**](https://fs.blog/mental-model-hanlons-razor/) 

You may have heard the saying “Stupidity is the same as evil if you judge by the results.’ Hanlon's Razor is taking the results of something gone wrong, and defaulting to blame it on stupidity. 

That organization that got breached probably wasn’t purposefully malicious. Those engineers that push terrible code, aren’t evil. No one is (probably) out to get their customers. I think everyone can agree that we don’t want breaches to happen.

HanlHanlon'sor helps us understand that more times than not, mistakes happen through innocent intentions. 

As an information security professional, it's your job to prevent those mistakes from happening, through education and mechanisms designed to reduce risk. 



