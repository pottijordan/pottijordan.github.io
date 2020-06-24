YY---
title: Violating Your Personal Space with Webex
author: jp
type: post
date: 2018-10-03T22:41:22+00:00
url: /2018/10/03/violating-your-personal-space-with-webex/
categories:
  - Uncategorized

---
Some time ago [Karl Fosaaen with NetSpi came out with some pretty interesting research around Federated Services and Skype for Business][1]. One of the attack vectors was being able to access other companies address books and sending them direct Skype for Business messages, including all the features that Skype for Business has, such as seeing when other users are online. There is something similarly fishy/phishy with Cisco’s Webex..

Webex is used by thousands of companies worldwide. It’s a video conferencing platform that allows customers to use domains such as “companyname.webex.com” to host their public meeting rooms.

One very useful feature is personal meeting rooms. A personal meeting room is given to each employee so that they may host meetings or conference calls in their personal room over Webex. Employees can share their personal meeting room link with those they wish to communicate with.

<img src="/images/2018/10/a-screenshot-of-a-cell-phone-description-generate.png">

A common link is: “companyname.webex.com/meet/userid”. The user id is typically the users domain username or email address plus or minus a few characters. Once you know a company’s personal room naming scheme, you can now enter anyone’s personal room, as anyone you’d like, from anywhere you’d like! Here is an example below:

By navigating to a personal meeting room link, I am presented with this page.

<img src="/images/2018/10/word-image-3.png">

Once you enter any details you want, you are then directly placed into the personal room, The best part is that if that person is not currently in their room, we are presented with this:

<img src="/images/2018/10/word-image-4.png">

Once you click notify, an email sent to the owner of the personal room. This email is generated and sent from <messenger@webex.com>. The name I entered “Fake Name” could have been anyone’s name I chose, for example, my targets boss.

<img src="/images/2018/10/word-image-5.png">

## So what does this allow us to do?

##### A couple of useful things as a red teamer or adversary:

  1. Enumerate Valid Usernames
  2. Direct access to anyone in the company, think C levels.
  3. Convincing Vishing platform, just turn off Video and treat it like VOIP.

## How do I find out if my company is vulnerable to this?

  1. Check your personal room link URL by going into your Webex Settings.
  2. Try to access that link from a computer off your network, if you can get into the personal room, you are vulnerable.

## What can I do about it?

  1. Depending on how it is set up, you may be able to change your personal room’s URL. Make it something unguessable! 
      * e.g. if it is fname.lname, change it too lname.fname_<5 random characters>
  2. Encourage your IT department to change everyone’s room id to something unguessable or random.

A quick subdomain enumeration on *.webex.com discovered thousands of domains for companies that use Webex in this manner. With some quick Google Dorking, discovering what their naming scheme for personal rooms would be trivial.

Comments, suggestions, concerns&#8230; feel free to reach out to me on [Twitter][2] or by [email!][3]

 [1]: https://blog.netspi.com/attacking-federated-skype-powershell/
 [2]: https://twitter.com/ok_bye_now
 [3]: mailto:admin@jordanpotti.com
