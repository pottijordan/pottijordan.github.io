---
title: ServiceNow - HelpTheHelpDesk And The Hackers
author: jp
type: post
date: 2021-02-21T20:37:56+00:00
url: /2021/02/21/ServiceNow-HelpTheHelpDeskAndTheHackers/
categories:
  - bug bounty
---

**tldr; ServiceNow had a feature that exposed credentials to hundreds (if not thousands) of their customers ServiceNow instances. These credentials varied from limited permissions, to full administrative access to the ServiceNow instance. The vulnerability was patched on October 8th, 2020.**

ServiceNow has a feature, that when configured, allows ServiceNow customers to collect information from their employees or customers endpoints. This is in the form of a WMI script, that collects system information, and ships it off to ServiceNow. 

This sounds great, except for how ServiceNow decided to authenticate those requests to submit system information. Curiously, the credentials for the request were stored in a public JavaScript file on all ServiceNow instances utilizing the HelpTheHelpDesk feature. 

The JavaScript file can be accessed at https://\<customername\>.servicenow.com/HelpTheHelpDesk.jsdbx. The credentials are at the top of the script for anyone’s viewing pleasure. How this hadn’t been found before is interesting. 

Fortunately, the password was encrypted... Unfortunately, the password was actually base64 encoded, and not encrypted as the JS file tried to convince us by prepending the base64 encoded password with ‘encrypt:'.

Since determining if a host is exposing credentials was done with a simple GET request, it was a trivial process to determine the breadth of the issue. Using some open source reconnaissance, a list of ServiceNow subdomains was collected and each one was issued a request for the HelpTheHelpDesk script. If the httpUsername and httpPassword values were filled, the request was logged. 

This led to quickly finding over 600 enterprises, government agencies and universities exposing credentials for their ServiceNow instances. 


![](https://paper-attachments.dropbox.com/s_950FE6053E05F52791508FF8799E8027016BE6B9BAB57C646249306A732842EE_1613888606369_image.png)


**So what do these credentials get us?**

The documentation describes a process that provides only the required unprivileged role to the SOAP HelpTheHelpDesk user. 

![](https://paper-attachments.dropbox.com/s_950FE6053E05F52791508FF8799E8027016BE6B9BAB57C646249306A732842EE_1613889501817_image.png)


Unfortunately again, many customers just used their ServiceNow administrator credentials..

Usernames such as sn_admin, admin, and servicenow-admin were plenty, and in more than one case, credentials provided full admin access to ServiceNow instances that were used by global companies with bug bounty programs. 

Administrative access to a ServiceNow instance provides a smattering of varying access. You get access to customer support tickets, employee data, internal documentation, internal IT tickets and internal HR tickets. Other ServiceNow features can even provide command execution on servers and workstations enrolled in various ServiceNow integrations. Given the amount of information and access ServiceNow has in many environments, this can lead directly to entire environment compromise. 

Due to the vast exposure, this vulnerability (feature?) was reported to ServiceNow and a patch was issued shortly after, closing up the massive hole in a matter of months. 

**Timeline:**

Discovered: August 15th, 2020\
Reported to ServiceNow: August 20th, 2020\
Response from ServiceNow: August 21st, 2020\
Patch Released: October 8th, 2020\
Public Disclosure: February 22nd, 2021



