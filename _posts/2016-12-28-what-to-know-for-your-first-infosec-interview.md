---
title: What To Know For Your First InfoSec Interview
author: jp
type: post
date: 2016-12-28T18:58:20+00:00
url: /2016/12/28/what-to-know-for-your-first-infosec-interview/
categories:
  - Uncategorized

---
There are a whole bunch of sites out there that have common interview questions for careers in information security. I took those and compiled a list of concepts that those questions are based off of. While most of this stuff you should already know if you have an interview, it&#8217;s nice to have a refresher sheet. This most likely won&#8217;t be anything earth shattering but it will be a collection of concepts that a budding information security professional should understand. This is also a dynamic post so things will likely be added over the next couple months.

This is by no means a complete list but it should be a guide on some of the common concepts we should understand. I created this more for myself as a refresher since I will be graduating soon but I hope you can find it useful as well.

_Disclaimer: I am a student as well, this was done as a way to gain a better understanding of these concepts. I am not a seasoned professional so don&#8217;t accept everything I post on here as absolute fact, this goes for everything on my site. In fact, it goes for everything. Always question what you&#8217;re told, especially on the internet. _

##### **XSS &#8211; Cross Site Scripting**

XSS is a client side attack. What this means is that an attacker is able to place malicious code on a website and when a user visits that web page, their web browser runs the code locally. Typically, this is JavaScript but it can be any client side language.

So what can an attacker do with a XSS vulnerability? There are a number of things that can be done. The most common is stealing cookies or other session information.

**For example**, you log into your favorite shopping site. The site issues your web browser a cookie which is your unique identifier. If an attacker is able to place malicious code on any site you browse to, he can grab your cookie, which allows him to access your favorite shopping site as you.

There are two main types of XSS attacks; **Stored** and **Reflected**.

**Stored** is basic, it means that the malicious code is stored on the web server. It can be stored in a comment field, in a database or wherever else you can place your code on the website. **Reflected** is a bit more convoluted. This requires a victim to access a malicious resource such as a website which then sends a message to the vulnerable web server, which then reflects its response to the victim.

One thing to keep in mind is that if the user is running a web browser that has code execution vulnerabilities, you may be able to execute code on the victims device via an XSS attack.

So how do we fix these types of vulnerabilities? While it completely depends on the circumstances there some common ways to prevent many XSS vulnerabilities. **Escaping any character that can affect your web app.** Depending on the language you are using, there are typically plugins or functions for this. **Validate all user data**, if it is expecting a phone number, make sure your web app only accepts a phone number. **Escape all data outputted to the user. **The other proven method of preventing the damage of a XSS attack is **secure cookies** and **content-security-policy headers**. These force SSL/TLS on the cookies and enforce the HTTP response behavior.

Two notable XSS attacks were TweetDeck and Samy. Both of these were not particularly dangerous but they did represent the vast effect that these types of vulnerabilities are capable of.

To learn more, check out [OWASP&#8217;s guide on XSS][1].

* * *

##### CSRF &#8211; Cross Site Request Forgery

CSRF is another client side attack. This attack requires an attacker to have some knowledge of the web application as well as who their target is. CSRF is basically a way for an attacker to force you to execute an action by sending you a link.

**For example**, the victim gets an email at work while logged into an internal web app that controls access to a VPN portal. The victim clicks on the link which actually points towards that internal web app which adds a new user to the VPN portal. As you can see, this could be catastrophic to an organization if executed correctly.

There are two main types of CSRF attacks. One is used for the example above, it requires some social engineering in order to trick the user into clicking the link. The other type is **Stored**. Stored CSRF takes place when the CSRF link is stored on the web server itself. This can also be executed in part with a XSS vulnerability which would in turn would target a wide amount of users.

So how do we fix these types of vulnerabilities? Most web frameworks have built in methods to guard against CSRF vulnerabilities. Other ways include **checking the referrer header** to verify it matches the target origin, **checking the origin header** to verify it matches the target origin and **CSRF tokens**. CSRF tokens are unique to each session and are generated at random for each session. If the request does not pass the verification, the request fails.

An alternate way to deter CSRF attacks, as well as just being a good idea, is using **CAPTCHA**&#8216;s.

In 2008, uTorrent had a CSRF vulnerability that definitely warrants a read if you want to get a better understanding of CSRF attacks. [Check it out here.][2]

To learn more, check out [OWASP&#8217;s guide on CSRF][3].

* * *

##### Encryption Stuff

In **symmetric encryption**, both communicating parties will need a copy of the same key to decrypt and encrypt data. In an **asymmetric encryption (Public/Private key)**, both parties will need each others public key. An encrypted message which is encrypted with a public key, can only be encrypted with each users private key which is kept hidden.

**Encryption Algorithms** are simply algorithms that scramble data based on some complicated math by using a key. Some of the common ones are Triple DES, RSA, Blowfish, AES. I would assume that unless you will be working on projects directly related to an organizations PKI (Public Key Infrastructure), you won&#8217;t be expected to have a deep understanding of these beyond knowing which of the common ones are symmetric or asymmetric. Some symmetric algorithms are AES, DES and 3DES. The most common asymmetric algorithm is RSA.

<p style="text-align: left;">
  <strong>Key Exchange</strong> is the method in which an encrypted communication channel is established.  A common key exchange protocol is <strong>Diffie-Hellman</strong>. The problem with this initial key exchange is that before the two parties exchange keys, they cannot communicate securely. This introduces the problem of a <strong>Man-In-The-Middle</strong> attack. Luckily, there is a way to assist with that issue by using <strong>Digital Signatures</strong>. This is where your SSL certificates come into play. SSL certs are issued by a Certificate Authority such as VeriSign. Another common to verify a parties identity is the use of <strong>PGP</strong>, or pretty good privacy. Instead of trusting a third party to verify a certificate, each user is responsible for sharing their public key. This is a form of asymmetric cryptography.
</p>

**RSA** is similar to Diffie-Hellman with some variations. RSA is also an asymmetric protocol but it takes care of signing the digital certificate as where Diffie-Hellman couldn&#8217;t do that.

_In a nutshell, if asked the difference between the two, RSA is more of an encryption algorithm where Diffie-Hellman is more of a key-exchange protocol._

**Hashing** is not to be confused with encrypting. Hashing is irreversible where as encryption is reversible. MD5 and SHA-1 are fairly common hashing types but are no longer consider secure. SHA-256 is considered much stronger. A **salt** is used in conjunction with a hash in order to make it more difficult to defend against dictionary attacks or rainbow table attacks. What a salt does is append a random value to a password before it is hashed in order to prevent having the same hashes in a database if two users happen to use the same password.

**Encoding** is the simplest to reverse as its not designed for obscuring or hiding data. A common form of encoding is base64. What encoding does is converts data to a common form to be transferred over a medium in order to protect the integrity of the data.

[https://www.owasp.org/index.php/Guide\_to\_Cryptography][4]

<https://en.wikipedia.org/wiki/Salt_(cryptography)>

<https://en.wikipedia.org/wiki/Encryption>

* * *

##### CVSS and CVE

**CVSS** stands for Common Vulnerability Scoring System. This is a universal language to describe the severity of a vulnerability. Scores range from 0 to 10, where 10 is the most critical. There are multiple metrics that go into calculating the score and these scores change over time depending on the number of target systems, damage potential, exploitability among others.

**CVE **stands for Common Vulnerabilities and Exposures. This is a standard way to identify a vulnerability with a standard naming convention. This is where you see CVE-YEAR-SOME RANDOM NUMBERS. &#8220;CVE-2016-3578&#8221;. Mitre has a database of all CVE&#8217;s as well as NIST where you can see some details about each CVE as well as the associated CVSS score.

<https://cve.mitre.org>

<https://nvd.nist.gov/>

* * *

##### OWASP Top Ten

Get familiar with the Owasp Top Ten. They have a [pretty nice cheat sheet][5] you can study off of. You should be familiar with at least one example based off each vulnerability.

Examples:

  1. **Injection**&#8211; SQL injection
  2. **Broken Authentication** &#8211; Login form over HTTP.
  3. **XSS** &#8211; Stolen authentication token.
  4. **Insecure Direct Object References** &#8211; Normal users can add admin users.
  5. **Security Misconfigurations** &#8211; Anything you can think of basically. A common one is default credentials.
  6. **Sensitive Data Exposure** &#8211; A users password is displayed to him in his settings tab which means passwords are stored in plain text.
  7. **Missing Function Level Access Control** &#8211; The admin tab is not shown to a normal user but it can still be accessed by plugging in &#8220;/categories/admin&#8221; after the host name.
  8. **CSRF** &#8211; User unknowingly performs an action orchestrated by an attacker.
  9. **Using Components with Known Vulnerabilities** &#8211; Running a WordPress plugin that has known vulnerabilities.
 10. **Unvalidated Redirects and Forwards** &#8211; A user is redirected to a malicous website.

* * *

##### OSI Model

OSI stands for Open Systems Interconnection model.  This is a theoretical model that helps us design and understand how data communication works.

<img class="alignnone wp-image-175" src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2016/12/2016-12-28-11_20_20-OSI-model-Wikipedia.png?resize=627%2C184" alt="" width="627" height="184" srcset="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2016/12/2016-12-28-11_20_20-OSI-model-Wikipedia.png?resize=300%2C88 300w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2016/12/2016-12-28-11_20_20-OSI-model-Wikipedia.png?resize=768%2C226 768w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2016/12/2016-12-28-11_20_20-OSI-model-Wikipedia.png?resize=1024%2C302 1024w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2016/12/2016-12-28-11_20_20-OSI-model-Wikipedia.png?resize=1200%2C353 1200w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2016/12/2016-12-28-11_20_20-OSI-model-Wikipedia.png?w=1202 1202w" sizes="(max-width: 627px) 100vw, 627px" data-recalc-dims="1" />

Image Credit: <https://en.wikipedia.org/wiki/OSI_model>

Examples of each layers:

<img class="alignnone wp-image-176" src="https://i1.wp.com/jordanpotti.com/wp-content/uploads/2016/12/2016-12-28-12_26_40-Book1-Excel-300x117.png?resize=608%2C237" alt="" width="608" height="237" srcset="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2016/12/2016-12-28-12_26_40-Book1-Excel.png?resize=300%2C117 300w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2016/12/2016-12-28-12_26_40-Book1-Excel.png?resize=768%2C299 768w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2016/12/2016-12-28-12_26_40-Book1-Excel.png?w=903 903w" sizes="(max-width: 608px) 100vw, 608px" data-recalc-dims="1" />

Some key differences between a router, a switch and a hub are what they connect. A router connects networks and a switch connects hosts. A hub just connects hosts and instead of using logic to divert packets, it just sends to everyone. A router uses IP addresses for transmitting data, a switch uses MAC addresses and a hub doesn&#8217;t care since it is broadcasting all received data.

Since ICMP is a Layer 3 protocol, it doesn&#8217;t use ports which take place at Layer 4. That is why pinging does not require a port to be open. Using commands like tracert to determine the path of a packet also uses ICMP (Windows). This works by setting the TTL (Time To Live) to a low number and incrementing by one each time you get a response. The response will be an ICMP error message since the TTL was reached before reaching the packet&#8217;s destination. This way, your device is able to build a list of devices that a packet crosses all the way to the destination. Linux uses a similar method by using UDP instead of ICMP.

##### Some of the Interview Questions Asked To Me Personally

_Some of these questions were asked &#8220;pre-interview&#8221; at a job fair so some of them are pretty simple. _

What is XSS and how would you prevent a XSS attack?

What is CSRF and how would you prevent a CSRF attack?

TCP vs UDP?

What is a buffer overflow?

What is a block cipher?

Asymmetric vs symmetric encryption?

What is Diffie-Hellman?

What are some static/dynamic analysis tools?

Whats your favorite security tool?

##### ForgottenSec&#8217;s Github with some awesome content

[https://github.com/ForgottenSec/Transitioning\_Into\_InfoSec/blob/master/index.md][6]

##### Related Links from Rob Fuller&#8217;s (mubix) repo

<div class="gist-oembed" data-gist="5737a066c8845d25721ec4bf3139fd31.json" data-ts="8">
</div>

 [1]: https://www.owasp.org/index.php/Cross-site_Scripting_(XSS)
 [2]: http://xs-sniper.com/blog/2008/04/21/csrf-pwns-your-box/
 [3]: https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)
 [4]: https://www.owasp.org/index.php/Guide_to_Cryptography
 [5]: https://www.owasp.org/index.php/Top_10_2013-Top_10
 [6]: https://github.com/ForgottenSec/Transitioning_Into_InfoSec/blob/master/index.md