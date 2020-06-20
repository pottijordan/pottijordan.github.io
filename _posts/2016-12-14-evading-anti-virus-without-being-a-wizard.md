---
title: Evading Anti-Virus Without Being A Wizard
author: jp
type: post
date: 2016-12-14T18:32:38+00:00
url: /2016/12/14/evading-anti-virus-without-being-a-wizard/
categories:
  - Uncategorized

---
Recently, I became curious just how different AV evasion tools actually worked. This is a very interesting topic as AV  vendors seem to be playing catch-up. Granted, they are pretty good at this cat and mouse game but the problem is that they are the mouse.

Lots of posts I have seen on AV evasion are simple tutorials on how to run the tools but I don&#8217;t find that very helpful. Without understanding what you&#8217;re doing, you aren&#8217;t going to be able to propose a solution. Anyways, the techniques used to trick AV are pretty clever!

This is not meant to be a simple tutorial since there are plenty of those out there; what I wanted this to be is a way to understand how AV bypass tools work.

* * *

**Hyperion:** Hyperion is an older tool that is only on this list to demonstrate a pretty cool obfuscation technique. Hyperion use a technique that encrypts your malicious binary with a shortened AES key. The decrypter, or stub, which is not encrypted, gets packed into the binary as well. What that does is brute forces the AES key every time the binary is run. The problem with this is that AV vendors look for this brute forcing stub which forces Hyperion to rely on original obfuscation techniques such as Assembly Ghostwriting. Assembly Ghostwriting is in short placing junk inside your program in order to hide its true purpose.

Tutorial: <http://e-spohn.com/blog/2012/08/02/pe-crypters-hyperion/>

Documentation: <https://www.exploit-db.com/docs/18849.pdf>

* * *

**Veil-Evasion:** Veil is a framework that can use multiple obfuscation techniques. One of these is taking the same method Hyperion uses. It also can take a python shell and using py2exe, Pyinstaller, or pwnstaller, it can bypass AV. Since Pyinstaller is a fairly common tool, AV rarely flags binaries created with it. Pwninstaller is a variation of Pyinstaller. What it does is recompiles a part of Pyinstaller to force your binary to NOT opt-in for DEP protection which can increase the reliability of your binary.

Another method Veil uses is custom code, obfuscating code and using non-standard code for Windows binaries. Hopefully, you can already see a trend. There are multiple ways to obfuscate malicious code, no one way works for all AV vendors and no one way will work for very long before they catch up.

Here is a chart of how well AV vendors are faring against Veil payloads: <https://docs.google.com/spreadsheets/d/1GkNmPkaPrHevO0tHnc-W_xctBNWtnL3LaQIeejTNX6U/edit#gid=0>

Tutorial: <https://www.security-sleuth.com/sleuth-blog/2015/2/3/using-veil-with-metasploit>

Documentation: <http://www.slideshare.net/VeilFramework/the-veilframework>

* * *

**Shellter:** This tool is simply a PE injector. It allows you to inject a shell into a binary. Because you can choose any application to inject code into, as well as choose any injection point, you basically have an extremely polymorphic malicious binary tool. Shellter also has options to load several built in meterpreter shells. This is also the only Closed-Source project on this list so you might want to take that into consideration before using this tool.

Tutorial: <https://www.doctorchaos.com/shellter-project-bypassing-av-detection/>

Documentation: <https://www.shellterproject.com/introducing-shellter/>

* * *

**Overview:**

There are of course many other ways to bypass AV. This post was meant to gain an understanding of the point-n-click tools for AV bypass.

Hyperion fares the worst since its &#8220;stub&#8221; or decryption code is well known by AV vendors. This could be bypassed by writing your own stub. After obfuscating my meterpreter shell, the detection rate actually went up.

Veil fares pretty well! Its framework also has a bunch of other convenient tools as well which makes it the most robust tool on this list. You can check out this list of proven bypassed AV vendors above.

Shellter actually seemed to be the most in depth AV obfuscation tool on this list. With my first try, I got a meterpreter shell with a perfect score on Virus Total. Once again, with this tool being closed-source, it will be interesting to see how long it takes for AV to catch up!