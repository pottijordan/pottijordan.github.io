---
title: How I Gained Access to Nearly Half A Million College Transcripts
author: jp
type: post
date: 2017-06-01T16:57:29+00:00
url: /2017/06/01/how-i-gained-access-to-nearly-half-a-million-college-transcripts/
categories:
  - Uncategorized
tags:
  - application security

---
Last year I spent some time digging into bug bounty programs. Since I was quite new to the scene, I spent most of my time just figuring out how a lot of the tools worked, as well as figuring out a good process that worked for me. I spent loads of time in the Web Application Hackers Handbook and tried to figure out what looked normal, and what didn’t look normal.

My first bug however wasn&#8217;t from a bug bounty. It was on a massive university system. This system has collectively approximately 500,000 enrolled students at this time.

_Now I do not recommend going bug hunting on a system that isn&#8217;t part of a bounty program or one that you do not have explicit authorization to do so._ 

I came across this bug sort of by accident. While I was checking my academic record, or my transcript, I noticed something strange. The URL had two suspicious parameters that seemed to be sequential and pre-fixed with the current days date. One was jobQSeqNo and the other was job_id. Out of curiosity, I tried to open the link in an incognito window, which stripped my cookies from the request. I still could see my transcript..

That seemed strange since the URL did not provide any sense of obscurity. Using Burp Intruder, I cycled the numbers in the two identified parameters and started seeing hundreds and hundreds of transcripts. It turns out there was no authentication checked on the page as well as easily guessable URL parameters. I immediately contacted the security team for the system and they responded and resolved the vulnerability quite quickly. Luckily they did not sue me and my transcripts are safe now. Win, Win. They were even okay with disclosing, which was quite surprising, so kudos to them.

Some of the information gleaned from someone&#8217;s transcript includes:

  * Student ID Number
  * Student Name
  * Degree
  * Courses Taken
  * Grades for Each Course
  * Total GPA

This information is protected by FERPA, in fact, at the bottom of each report, there was a FERPA statement.

<img src="/images/2017/06/3.png" />

Overall, there aren&#8217;t many technical details to include. It really consisted of some curiosity, Burp Intruder, and a list of 1000 sequential numbers.

<img src="/images/2017/06/1.png" />

Here are the Intruder results showing quite a few results:

<img src="/images/2017/06/2.png" />
