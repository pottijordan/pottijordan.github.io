---
title: HEY, Can I Put Spaces Here? A story about that time I almost saved 900 bucks.. 
author: jp
type: post
date: 2020-07-31T20:37:56+00:00
url: /2020/07/31/hey-can-i-put-spaces-here/
categories:
  - bug bounty
  - application security

---


 <img src="/images/2020/07/hey.jpg">


Several weeks ago, I received an invite code to HEY.com. Naturally, I tried to get jp@HEY.com. Since its considered a “premium” email address, its spendy. For $999, not really worth it probably.  

Turns out, email addresses 4 or more characters are the standard $99. So.. lets try ‘jp  @hey.com’ (two spaces). And boom! HEY stripped the last two spaces after the premium character count check, and I had jp@hey.com for $99!  

 <img src="/images/2020/07/99.png">

So. Of course if I bought this for $99, I would have to tell them about it, or someone else would, and I would be stuck with an email address that won’t work past a year (unless I wanted to choke up $999).  

So I told them.. 

And they fixed it. 

And they didn’t let me keep jp@hey.com for $99.  

But at least they had a fix in production in an hour or so! 

<img src="/images/2020/07/999.png">
