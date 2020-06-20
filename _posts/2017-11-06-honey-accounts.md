---
title: Honey Accounts
author: jp
type: post
date: 2017-11-06T20:37:56+00:00
url: /2017/11/06/honey-accounts/
categories:
  - Uncategorized
tags:
  - active directory
  - canary
  - defense
  - honey pot

---
I recently saw a tweet mentioning the use of an AD account with the password in the description attribute and logon hours set to none. I can’t find that tweet anymore so I apologize for the lack of attribution.

The idea is that when someone does breach your network perimeter, some of the first steps in performing recon is collecting information from Active Directory. In this recon, they stumble on a DA account called ‘helpdeskDA’. They even discover a password in the description! Well this looks like an easy win and a critical finding. In order to figure out how to leverage this new found user, the attacker attempts to RDP or use psexec to move to a higher value target. In doing so, AD checks the credentials and returns to the attacker that his newfound account is not allowed to login during this time. Meanwhile, this login attempt has triggered an alert and is being investigated.

I personally love security tools that aren’t just blinky lights and can run natively on devices we already possess. I took a stab at setting this up in my test environment

## Setting up the AD Account:

  1. Create your new account in AD Users and Groups and add to the Domain Administrators group.
  2. Set the user description to something believable or just set the password in there and let the attacker make their own assumptions.

<img class="wp-image-297" src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/word-image.png?w=688" srcset="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/word-image.png?w=418 418w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/word-image.png?resize=231%2C300 231w" sizes="(max-width: 418px) 100vw, 418px" data-recalc-dims="1" />

  1. Go to the Account tab and select Logon Hours…
  2. Set Logon Denied to 24&#215;7

<img class="wp-image-298" src="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys.png?w=688" alt="Q:\Restricted\SECURITY\Application Security Analyst\HoneyAccounts\AD\login.png" srcset="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys.png?w=1103 1103w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys.png?resize=300%2C152 300w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys.png?resize=768%2C390 768w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys.png?resize=1024%2C520 1024w" sizes="(max-width: 688px) 100vw, 688px" data-recalc-dims="1" />

## Group Policy:

There are a couple Group Policy options that need to be enabled in order for this to work. Depending on your organization, these are already configured.

  1. Edit the Default Domain Policy.

<img class="wp-image-299" src="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2017/11/word-image-1.png?w=688" srcset="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2017/11/word-image-1.png?w=491 491w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2017/11/word-image-1.png?resize=300%2C241 300w" sizes="(max-width: 491px) 100vw, 491px" data-recalc-dims="1" />

  1. Navigate to: Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Advanced Audit Policy Configuration -> Audit Policies -> Account Logon -> Audit Kerberos Authentication Services and set to audit Failure events.

<img class="wp-image-300" src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-1.png?w=688" alt="Q:\Restricted\SECURITY\Application Security Analyst\HoneyAccounts\GP\gp_config1.png" srcset="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-1.png?w=1099 1099w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-1.png?resize=300%2C204 300w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-1.png?resize=768%2C523 768w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-1.png?resize=1024%2C697 1024w" sizes="(max-width: 688px) 100vw, 688px" data-recalc-dims="1" />

Don’t forget to update group policy on the client by running: gpupdate /force

## Event Viewer:

Here is an example of what the event now looks like in Event Viewer.

<img class="wp-image-301" src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-2.png?w=688" alt="Q:\Restricted\SECURITY\Application Security Analyst\HoneyAccounts\EventViewer\eventLog.png" srcset="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-2.png?w=894 894w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-2.png?resize=300%2C195 300w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-2.png?resize=768%2C498 768w" sizes="(max-width: 688px) 100vw, 688px" data-recalc-dims="1" />

And here is the XML view with more details, this is important for writing our event trigger in Task Scheduler. Note the event ID. A normal failed login is event ID 4625. However, since this won’t be a traditional failed login due to the login hours, it falls under 4768 which indicates that the Kerberos authentication ticket was requested and failed due to various reasons, in this case the logon hours.

<img class="wp-image-302" src="https://i1.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-3.png?w=688" alt="Q:\Restricted\SECURITY\Application Security Analyst\HoneyAccounts\EventViewer\eventDetails.png" srcset="https://i1.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-3.png?w=941 941w, https://i1.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-3.png?resize=300%2C154 300w, https://i1.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-3.png?resize=768%2C394 768w" sizes="(max-width: 688px) 100vw, 688px" data-recalc-dims="1" />

## Task Scheduler:

Configuring the task scheduler took a bit of time. This is due to the way the event was logged. Since it is not the traditional 4625 failed login event, the event trigger based on User did not work. I had to write a custom XML trigger rule to catch the username in this context.

  1. Open Task Scheduler and create a new task. Name it whatever you like and make sure to select ‘Run whether user is logged on or not’.

<img class="wp-image-303" src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-4.png?w=688" alt="Q:\Restricted\SECURITY\Application Security Analyst\HoneyAccounts\TaskSched\step1_CreateTask.png" srcset="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-4.png?w=637 637w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-4.png?resize=300%2C229 300w" sizes="(max-width: 637px) 100vw, 637px" data-recalc-dims="1" />

  1. Move to the trigger tab and edit trigger.

<img class="wp-image-304" src="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-5.png?w=688" alt="Q:\Restricted\SECURITY\Application Security Analyst\HoneyAccounts\TaskSched\step2_NewTrigger-Custom.png" srcset="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-5.png?w=600 600w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-5.png?resize=300%2C260 300w" sizes="(max-width: 600px) 100vw, 600px" data-recalc-dims="1" />

  1. Select New Event Filter and select the XML tab. Check Edit query manually. Modify the following query to fit your user and domain. Since we don’t know what username format the attacker will use, we need some OR statements in there to cover our bases. Typically, we could use the SID but in Event 4678, it is not logged. You can also add granularity and only log on certain events but in this case, I thought it best to alert on any account activity.

<img class="wp-image-305" src="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-6.png?w=688" alt="Q:\Restricted\SECURITY\Application Security Analyst\HoneyAccounts\TaskSched\step3_EventCustomFilter.png" srcset="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-6.png?w=546 546w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-6.png?resize=292%2C300 292w" sizes="(max-width: 546px) 100vw, 546px" data-recalc-dims="1" />

  1. You should be able to save that and move to the Action tab. You can do whatever sort of event here that you like. I chose a powershell script that alerts various people and provides details about the event.

<img class="wp-image-306" src="https://i1.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-7.png?w=688" alt="Q:\Restricted\SECURITY\Application Security Analyst\HoneyAccounts\TaskSched\step4_actionEmail.png" srcset="https://i1.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-7.png?w=459 459w, https://i1.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-7.png?resize=273%2C300 273w" sizes="(max-width: 459px) 100vw, 459px" data-recalc-dims="1" />

  1. Select Start the task only if computer is on AC power.

<img class="wp-image-307" src="https://i1.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-8.png?w=688" alt="Q:\Restricted\SECURITY\Application Security Analyst\HoneyAccounts\TaskSched\step5_conditionsTask.png" srcset="https://i1.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-8.png?w=639 639w, https://i1.wp.com/jordanpotti.com/wp-content/uploads/2017/11/q-restricted-security-application-security-analys-8.png?resize=300%2C229 300w" sizes="(max-width: 639px) 100vw, 639px" data-recalc-dims="1" />

## Powershell Script:

> <pre>function sendMail{

Write-Host "Sending Email"

#SMTP server name

$smtpServer = "smtpserver"

#Creating a Mail object

$msg = new-object Net.Mail.MailMessage

#Creating SMTP server object

$smtp = new-object Net.Mail.SmtpClient($smtpServer)

$file = "C:\users\Administrator\Desktop\alert.txt"

$att = new-object Net.Mail.Attachment($file)

#Email structure

$msg.From = "noreply@example.com"

$msg.ReplyTo = "noreply@example.com"

$msg.To.Add("securityteam@example.com")

$msg.To.Add("securityteam@example.com")

$msg.subject = "ALERT:Honey Account"

$msg.body = "Honey Account Activity Detected"

$msg.Attachments.Add($att)

#Sending email

$smtp.Send($msg)

 


}

function collectDetails{

 $attachment= Get-WinEvent -LogName "Security" -FilterXPath "*[EventData[(Data='ws_admin@security.local' or Data='ws_admin' or Data='SECURITY-DC\ws_admin')]]" | Format-List -Property * | Out-File C:\users\Administrator\Desktop\alert.txt

}

#Calling function

collectDetails

sendMail</pre>