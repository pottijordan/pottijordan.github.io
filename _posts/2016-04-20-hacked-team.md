---
title: How the Hacking Team Got Hacked
author: jp
type: post
date: 2016-04-20T22:18:38+00:00
excerpt: For Fisher, Hacking Team was a perfect target.
url: /2016/04/20/hacked-team/
sharing_disabled:
  - 1
switch_like_status:
  - 'a:1:{i:0;i:0;}'
categories:
  - Uncategorized
tags:
  - hacking
  - hackingteam
  - wikileaks

---
The Hacking Team is a group of 50+ hackers who provide offensive tools for governments across 6 continents. In mid-2015, they were exposed to the world. Everything from their source code to their email database was published on their defaced Twitter page via a torrent.

Little was known about the attack. Until several days ago. Phineas Fisher posted a full write-up on the steps taken to gain access and ex filtrate the goods.

His write-up can be found <a href="https://ghostbin.com/paste/6kho7" target="_blank">here</a> on Ghost Bin.

The tactics used by Fisher are very complicated. It&#8217;s easy to say that this guy definitely is not a noob. You can check out the link about for the full write-up which is pretty interesting or if you don&#8217;t got time for that, I summarized for you.

For Fisher, Hacking Team was a perfect target. According to him, it was run by a &#8220;Fascist&#8221; and worked against &#8220;journalists, activists, political opposition, and other threats to their power&#8221;

Fisher followed the typical steps to gather information for a target. He used Google, Subdomain Enumeration, Whois and Port Scanning. These are your typical beginning steps. After this he began to collect information from social sites such as LinkedIn and Metadata from public files. Once again, these are all typical steps for reconnaissance.

Fisher avoided social engineering, after all, the Hacking Team are experts at it so they likely know how to spot spear phishing!

With all this information collected, Fisher found that they don&#8217;t have much exposed to the internet. What they did have was a website, a mail server, a couple routers, two VPN appliances and a spam filter.

His options were pretty slim so he began to search for a 0day in an embedded device. This is that impressive part..

After two weeks of reverse engineering, he had a remote root exploit. And after some more testing and assuring that it would not raise a red flag, he created a back door firmware to hide his 0day.

He did not include his exploit or the device used since he claims that it still is not patched.

Once he was in their network, his process was pretty straight forward. Slow network scans and a NetBios poisoner <a href="https://github.com/SpiderLabs/Responder" target="_blank">responder.py</a>

He soon finds their backups and attained access to their email database and eventually their development network. He used iscsi to access their backups and mounted them using some clever iptables rules.

He ended up pulling all their passwords as well which he lists in the report.

> HACKINGTEAM         c.pozzi         P4ssword

Using powershell, he downloaded all the mailboxes and then used his proxy and smb to download all the files.

All of the emails leaked can be found <a href="https://wikileaks.org/hackingteam/emails/" target="_blank">here</a>.

He included quite a few links to all the tools he used and some good times and use cases for those. I would recommend you check that out!

It appears that at the end of his summary he encourages similar behavior. Heres an excerpt from his report..

> &#8220;Hacking guides often end with a disclaimer: this information is for
  
> educational purposes only, be an ethical hacker, don&#8217;t attack systems you
  
> don&#8217;t have permission to, etc. I&#8217;ll say the same, but with a more rebellious
  
> conception of &#8220;ethical&#8221; hacking. Leaking documents, expropriating money from
  
> banks, and working to secure the computers of ordinary people is ethical
  
> hacking. However, most people who call themselves &#8220;ethical hackers&#8221; just work
  
> to secure those who pay their high consulting fees, who are often those most
  
> deserving to be hacked.&#8221;

&nbsp;

However, the Hacking Team seems to call Fisher out on <a href="http://www.hackingteam.it/news/2016/04/18/on-our-security-breach.html" target="_blank">some of the details..</a>

Moral of the story, no one is invincible, and there is always someone out there ready to play your game better than you.

<img src="https://i2.wp.com/images.techhive.com/images/article/2015/07/hackingteam_logo-100594944-primary.idge.jpg?w=688" alt="hackingteam hacked logo" data-recalc-dims="1" />

Credit: Steve Ragan / Twitter

&nbsp;

&nbsp;