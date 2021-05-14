---
title: Determining Risk Less Badly
author: jp
type: post
date: 2021-05-14T20:37:56+00:00
url: /2021/05/14/determining-risk-less-badly/
categories:
  - red team
---

After an offensive security assessment, rank all the findings based on your gut. Now spend an hour or so per finding ranking these on the industry frameworks. 

Did you learn anything? Chances are, most of the risk scores are largely the same as what you scored based on your gut feeling. 

Vulnerability scoring using common framework's doesn’t give us anything besides being able to back up our gut feelings about risk. Either way we get to the same conclusion, and arguably, both are inaccurate.. 

## So how should we rank risk?

Most organizations perform annual risk assessments. These are how the business identifies risk, and determines how to mitigate or reduce that risk. There is a good chance that Legal, Product, HR, Security and other leaders are part of that conversation.

Some common factors of these risk assessments include financial loss, employee dissatisfaction, customer loss, regulatory or compliance impact, and of course, likelihood. When we talk about risk to senior leadership, this is the language they speak. So going from a conversation with other leaders discussing all the threats that face the business, its no wonder our “critical” Windows  Server vulnerability we reported to IT leadership gets disregarded. 


When we start to rank our findings, we start to ask these types of questions depending on the scoring model we are using:


***How much money does it cost the company if the SRE team doesn’t turn on command line logging for our nix infrastructure?***

***How many customers do we lose if IT doesn’t use a tiered approach with domain admin workstations?***

***How is employee morale impacted if those developers fail to remove credentials from that source code repository?***


None of those really make sense. Some of us start to chain findings together, or to look at the impact of the operation itself and try to factor those into the scores and overall, it begins to match our gut feelings around how bad these things are. (Which isn’t what we want)


When we begin sharing our findings and explaining the attack path, we get asked a set of common questions:


***“If that weakness wasn’t there, wouldn’t the rest be null?”***

***“If we fix the first thing on this path, would this other one still be high, or would it now be a medium?”***

***“What can we do now, that reduces the risk, but won’t take much work?”***


All of the questions stem from a poor presentation of what the offensive security did/does, and how bad the issues really are. 

## A new approach:

This approach saves time, allows us to communicate with leaders better, and prioritizes disrupting the attackers ROI. We first rank the risk of the operation as a whole, and then prioritize each finding or recommendation based on the return on investment.

*Maintain focus on pragmatism by ensuring that defensive measures are likely to meaningfully disrupt the attacker value proposition of attacking you, increasing cost and friction on the attacker's ability to successfully attack you. Evaluating how defensive measures would impact the adversary's cost of attack provides both a healthy reminder to focus on the attackers perspective as well as a structured mechanism to compare the effectiveness of different mitigation options.* - Microsoft Security Best Practices

![Increase attack cost with minimal defense cost](https://docs.microsoft.com/en-us/security/compass/media/privileged-access-strategy/balance-defender--and-attacker-cost.png)


1. Use the enterprise risk model that is used be leadership to rank the risk of the **outcome** of the operation.
    1. For example, ransomware getting deployed might be a critical risk, medium monetary risk, loss of customer confidence, employee morale, some regulatory issues maybe, etc.
    2. Supply chain attack leading to malicious code on customers endpoints. Might only be a high risk if the operation proved that its likely only possible on a small subset of products or in edge cases. 
    3. Customer data breached could even be a medium risk if the operation focused on PII. As we know, these types of breaches don’t always result in a huge financial loss and often times are buried in the news. 
2. Rank each recommendation based on priority. Priority should be based on a return on investment. A large return on investment would mean that the solution is relatively quick or low cost, while the cost to an attacker is vastly larger. 


    **Some examples of high ROI solution:**\
        1. Removing a credential from a public GitHub repository and rolling the credential\
        2. Shutting down an exposed script console on a production Jenkins server\
        3. Patching a WordPress vulnerability on a public server with an available exploit
    
    **Examples of also important but lower ROI solutions:**\
        1. Implementing MFA on email internally\
        2. Remove credentials from internal source code repositories and leverage an enterprise password storage solution\
        3. Restrict workstations from communication with one another

At this point, you know the actual business risk of the operation, you can explain to leadership why its critical. 

***This operation demonstrated the ability for a threat actor to infect all eighteen million subscribers to the Colorful Widgets Unlimited software. This would likely result in moderate financial impact, a critical security impact to Colorful Software Inc and its customers, as well as a high likelihood of major regulatory and compliance impact.***

Once you capture leaderships attention regarding the total risk, the rest of the findings are prioritized and the individual risk isn’t so important. The high priority findings get fixed quickly to stop the bleeding, while the lower ROI recommendations are picked off as longer projects.

