---
title: Basics of Windows Incident Response
author: jp
type: post
date: 2017-01-20T15:25:44+00:00
url: /2017/01/20/basics-of-windows-incident-response/
categories:
  - Uncategorized

---
<p style="text-align: left;">
  For most people, including me, it is difficult to determine just what is “normal” when looking for signs of a compromised host. As someone who has done multiple CCDC’s as a blue teamer, I can say that this is easily one of the biggest struggles since it affects incident response as well as identifying a compromise.
</p>

<p style="text-align: left;">
  I recently watched a webinar from Black Hills Info Sec that covered the basics of live Windows IR from the SANS 504 course. I recommend watching it, you can find it on YouTube <a href="https://www.youtube.com/watch?v=HcUMXxyYsnw">HERE</a>.
</p>

<p style="text-align: left;">
  Obligatory thank you to BHIS and SANS.
</p>

<h5 style="text-align: left;">
  Overview
</h5>

<p style="text-align: left;">
  Some of the most reliable tools for basic IR are built into Windows and Linux. This post will cover mostly Windows IR and definitely is nothing in depth but will get you started in the right direction. Windows has an extremely powerful tool named WMIC. WMIC stands for Windows Management Instrumentation Command-Line. It is extremely useful for IR as well as penetration tests. Between WMIC, netstat and tasklist, you likely have enough to notice some of the telltale signs that you have an infected host.
</p>

<p style="text-align: left;">
  The biggest thing about these tools is recognizing what is not normal. The best way to do that is to become familiar with the output of the tools. Look at the associated DLLs for common processes like svchost so that you can determine whether or not it’s actually a malicious executable or that the associated DLLs are legitimate. Of course, there are numerous ways around this from an attacker’s standpoint but you get the idea.
</p>

<p style="text-align: left;">
  SANS Instructor Mark Bagget developed a fairly neat tool that spawns a backdoor and quizzes you to find some information about that backdoor using some of the tools I go over.
</p>

<p style="text-align: left;">
  You can download that tool <a href="https://www.dropbox.com/sh/gb6k64cm3m641td/AACAAzyzjYoySuR64xslLLF8a/504lab-32bit.exe?dl=0">HERE</a>.
</p>

<p style="text-align: left;">
  I have a walk through for the tool below, it is very straight forward but is a good introduction to Windows IR.
</p>

<p style="text-align: left;">
  Most of the commands used to determine the answers to the questions can be found on the SANS IR Cheat Sheet.
</p>

<p style="text-align: left;">
  <a href="https://jordanpotti.com/wp-content/uploads/2017/01/linux-cheat-sheet.pdf">Linux IR Cheat Sheet</a>
</p>

<p style="text-align: left;">
  <a href="https://jordanpotti.com/wp-content/uploads/2017/01/windows-cheat-sheet.pdf">Windows IR Cheat Sheet</a>
</p>

<p style="text-align: left;">
  <a href="https://jordanpotti.com/wp-content/uploads/2017/01/Log_Review.pdf">Log Review Cheat Sheet</a>
</p>

<h4 style="text-align: left;">
  Windows IR Commands:
</h4>

<h5 style="text-align: left;">
  Event Logs
</h5>

<p style="text-align: left;">
  Event logs can be a great source of information, that is if you know what you are looking for. I would prefer querying it for known bad indicators versus looking at 25,000 logs that really don’t tell us anything useful. Luckily, there is an easy way to look for specific logs.
</p>

<p style="text-align: left;">
  Wevtutil enables us to retrieve information from event logs via the Windows command line, which is pretty awesome. You can do all this stuff from a remote machine and supply the command line with credentials as well.
</p>

<p style="text-align: left;">
  For more reading on Wevtutil, check out:
</p>

<p style="text-align: left;">
  <a href="https://www.petri.com/command-line-event-log">https://www.petri.com/command-line-event-log</a>
</p>

<p style="text-align: left;">
  <a href="https://technet.microsoft.com/en-us/library/cc732848(v=ws.11).aspx">https://technet.microsoft.com/en-us/library/cc732848(v=ws.11).aspx</a>
</p>

<p style="text-align: left;">
  Some of the logs that might be useful to use are Security logs that indicate user account changes or additions, failed user login attempts, or service status changes.
</p>

<p style="text-align: left;">
  <img src="/images//2017/01/2017-01-19-22_05_43-Book1-Excel-300x179.png"/>
</p>

<p style="text-align: left;">
  This command will query the Security logs for event ID 4720 (User Account Creation)
</p>

> wevtutil qe security /q:&#8221;*[System[(EventID=4720)]]&#8221; /c:5 /f:text /rd:true

<p style="text-align: left;">
  <strong>qe:</strong> What logs to query, here you would place system for system logs etc.
</p>

<p style="text-align: left;">
  <strong>/q:</strong> Specifies the query. The only thing you really need to change in here is the EventID, just replace it for the one you want. You can use truth operators in here as well as query specific alert levels. Check out that link above for more information on that.
</p>

<p style="text-align: left;">
  <strong>/c:</strong> specifies the number of events to display. (If you place nothing here, it will find all matching events)
</p>

<p style="text-align: left;">
  <strong>/f:</strong> Specifies the output type, by default it uses XML which can be difficult to read.
</p>

<p style="text-align: left;">
  <strong>/rd:</strong> This takes True or False. Set this to true in order to see the newest logs first.
</p>

<p style="text-align: left;">
  <img src="/images/2017/01/1.png"/>
</p>

<h5 style="text-align: left;">
  Netstat
</h5>

<p style="text-align: left;">
  netstat is an awesome tool that comes with Windows and Linux. It allows you to display active TCP connections, listening ports and a whole bunch of other stats including what Process ID the connection is associated with.
</p>

<p style="text-align: left;">
  This command will display all active TCP connections as well as listening TCP and UDP ports. You would want to run this to determine if this host is connecting to any strange locations, or if your host has something listening that shouldn’t be.
</p>

> netstat -anob

<p style="text-align: left;">
  <strong>-a:</strong> Displays all active TCP connections and the TCP and UDP ports on which the computer is listening.
</p>

<p style="text-align: left;">
  <strong>-n:</strong> Displays active TCP connections, however, addresses and port numbers are expressed numerically and no attempt is made to determine names.
</p>

<p style="text-align: left;">
  <strong>-o:</strong> Displays active TCP connections and includes the process ID (PID) for each connection.
</p>

<p style="text-align: left;">
  <strong>-b:</strong> This will display the PID’s actual filename and requires elevation.
</p>

<h5 style="text-align: left;">
  Tasklist
</h5>

<p style="text-align: left;">
  tasklist displays a list of applications and services with their Process ID. It is very useful for determining what process is associated with a PID. For example, if you notice a strange connection in the netstat output, you can determine the process with this tool.
</p>

<p style="text-align: left;">
  This command will display the associated task as well as the associated DLL’s.
</p>

> tasklist /m /fi “pid eq <Insert Process ID here w/out the brackets>”

<p style="text-align: left;">
  <strong>/m:</strong> Displays the associating modules.
</p>

<p style="text-align: left;">
  <strong>/fi:</strong> Allows you to use truth statements to refine your command.
</p>

<h5 style="text-align: left;">
  Net
</h5>

<p style="text-align: left;">
  net commands have a variety of uses. The net family has multiple siblings. All of them are helpful in identifying system information as well as active network activity.
</p>

<p style="text-align: left;">
  These commands display open sessions with your host.
</p>

> net session
> 
> net use

<p style="text-align: left;">
  Some other commands include:
</p>

> net user
> 
> net view
> 
> net user USERNAME

<h5 style="text-align: left;">
  WMIC
</h5>

<p style="text-align: left;">
  WMIC is an extremely useful tool for all sorts of IT folks. Having WMIC in your toolkit can immensely speed up the process of determining system information in IR, pen tests and system administration. There are some neat scripts out there that will gather a bunch of system data through WMIC for post-exploitation and enumeration as well. The link to the script is at FuzzySecurity’s write up on Windows privilege escalation.
</p>

<p style="text-align: left;">
  <a href="http://www.fuzzysecurity.com/tutorials/16.html">http://www.fuzzysecurity.com/tutorials/16.html</a>
</p>

<p style="text-align: left;">
  This command will display the name and parent process ID of a given process ID. This would be the next step after determining which process is performing strange network activity. The parent process will be the process that spawned the suspicious process.
</p>

> wmic process get name,processid,parentprocessid\|find “<Insert PID here without the brackets>”

<p style="text-align: left;">
  You can then follow up with running the same command with the parent process ID to determine the name of the parent process.
</p>

<p style="text-align: left;">
  You can also determine the command used to run the process.
</p>

> wmic process where processid=”PID without the quotes” get commandline

<p style="text-align: left;">
  To determine startup tasks.
</p>

`wmic startup get caption,command`

<p style="text-align: left;">
  To kill a process:
</p>

`wmic process where name=”nc.exe” delete`

<h5 style="text-align: left;">
  Some other useful commands to know:
</h5>

<p style="text-align: left;">
  To verify firewall state
</p>

`netsh firewall show state`

<p style="text-align: left;">
  To view scheduled tasks
</p>

`schtasks /query /fo LIST /v`

<p style="text-align: left;">
  View running Windows services
</p>

`net start`

<h5 style="text-align: left;">
  SANS 504 Incident Response Quiz
</h5>

<p style="text-align: left;">
  You probably want to shut your firewall off for this quiz since it will run a harmless backdoor on your host.
</p>

<p style="text-align: left;">
  Begin by running the executable from an elevated command prompt.
</p>

<p style="text-align: left;">
  <img src="/images/https://i1.wp.com/jordanpotti.com/wp-content/uploads/2017/01/2.png"/>
</p>

<p style="text-align: left;">
  You can use the netstat -anob to determine what is listening in which port. Typically, powershell.exe should not be listening on a random port so this should raise a red flag.
</p>

<p style="text-align: left;">
  <img src="/images/2017/01/3.png"/>
</p>

<p style="text-align: left;">
  Since we used the b switch with netstat, we determined the PID as well as the listening port.
</p>

<p style="text-align: left;">
  <img src="/images/2017/01/4.png"/>
</p>

<p style="text-align: left;">
  To find the parent process, we can use WMIC. We can run it twice to find the name of the parent process, we can see that our quiz executable is the parent process.
</p>

<p style="text-align: left;">
  <img src="/images/2017/01/6.png"/>
</p>

<p style="text-align: left;">
  This part will be easiest done with netcat. You can find the windows pre-compiled version all over the place if you don&#8217;t have it. If you don&#8217;t know how to use netcat, check out the SANS cheat sheet or some tutorials on it, it is an extremely useful tool.
</p>

<p style="text-align: left;">
  <a href="https://www.sans.org/security-resources/sec560/netcat_cheat_sheet_v1.pdf">https://www.sans.org/security-resources/sec560/netcat_cheat_sheet_v1.pdf</a>
</p>

<p style="text-align: left;">
  <img src="/images/2017/01/7.png" />
</p>

<p style="text-align: left;">
  As you can see, when we connected to that port, this flag was returned to us.
</p>

<p style="text-align: left;">
  <img src="/images/2017/01/8.png"/>
</p>

<p style="text-align: left;">
  Now things get a little more tricky since we have to use tasklist to determine malicious processes running. As you can see, I determined powershell.exe was running even though I definitely did not spawn it.
</p>

<p style="text-align: left;">
 <img src="/images/2017/01/12.png"/>
</p>

<p style="text-align: left;">
  Now in order to determine the command line, we can use the wmic commandline option.
</p>

<p style="text-align: left;">
  <img src="/images/2017/01/14.png" />
</p>

<p style="text-align: left;">
  Since it is Base64 decoded, you can use your favorite decoder to find the flag.
</p>

<p style="text-align: left;">
 <img src="/images/2017/01/15.png"/>
</p>

<p style="text-align: left;">
  And.. That&#8217;s all! As you can see, it is very simple but it does get you somewhat familiar with some of the built in tools that can help with determining a compromised host.
</p>

<p style="text-align: left;">
 <img src="/images/2017/01/16.png"/>
</p>

<p style="text-align: left;">
  If you have any questions or comments, feel free to send me an email at admin(at)jordan potti . com or reach me on twitter. <a href="https://twitter.com/ok_bye_now">@ok_bye_now</a>
</p>

&nbsp;
