---
title: Automating the detection of Mimikatz with ELK
author: jp
type: post
date: 2018-01-03T18:20:47+00:00
url: /2018/01/03/automating-the-detection-of-mimikatz-with-elk/
categories:
  - elastic
  - ELK
  - mimikatz
  - threat hunting

---
I’ve been going through CyberWarDog’s Threat Hunting posts as of late and stumbled upon his ‘[Hunting for In-Memory Mimikatz][1]’ Series. The methods used to build signatures are very straight forward and seem to remove a barrier to entry for figuring out how to profile malicious tools.

[The method used to detect Mimikatz is referred to as grouping which consists of taking a group of unique artifacts and identifying when multiple of the unique artifacts appear together.][2] So for this post, we will use Cyberwardog’s guidance to build an alert for the detection of Mimikatz using Sysmon and the ELK Stack.

This post assumes you already have an ELK Stack stood up with [ElastAlert configured][3]. This can be stood up in minutes with the [HELK][4]. (ElastAlert will be merged soon)

I want to start out by saying this is definitely not the most elegant solution. The idea was simple enough, alert when 5 DLL’s are accessed within 1 second of each other from the same host. Unfortunately, ElastAlert does not have this functionality built in, so Python it is..

The Sysmon config I am using is the [Ion-Storm sysmon config][5]. By default, the proper events are forwarded. Lines 571-579.

{% gist d103dc36af8212df7cb25e5dc3dfda9c excerpt-sysmonconfig-export.xml %}

To get started, we need a script to handle some of the logic required to verify a couple things before we fire off an alert. I tried to make the python tool as modular as possible so that we can easily alert on other event ‘groupings’.

The 5 DLL’s we will be detecting are:

<pre>Cryptdll.dll

Hid.dll

Samlib.dll

Vaultcli.dll

Winscard.dll</pre>

We will also only be detecting these if they happen to be accessed within one second of each other.

On your server running your ELK stack:

<pre>sudo nano /bin/py-alert.py</pre>

Paste in:

{% gist d103dc36af8212df7cb25e5dc3dfda9c py-alert.py %}

<pre>sudo chmod 755 /bin/py-alert.py</pre>

This script handles all of our logic and also sends our Slack Notification. Using the options, we can alert on any combination of events.

Add our individual rules to our alerts rules directory.

Grab our rules off GitHub:

<pre>git clone https://github.com/jordanpotti/ElastAlertGrouper.git</pre>

Copy our rules into our ElastAlert rules directory:

<pre>sudo cp ElastAlertGrouper/alert_rules/* /etc/elastalert/alert_rules/</pre>

We now have 6 new rules in our rule directory. Each rule with a DLL name alerts when that given DLL is loaded.

{% gist d103dc36af8212df7cb25e5dc3dfda9c samlib.yaml %}

As you can see, we have the typical alert rule options and we are querying for samlib in event_data.ImageLoaded. When this alert is tripped, it calls our python script with this command:

<pre>python3 /bin/py-alert.py –T D –a Mimikatz –o /tmp/mimikatz –c $ComputerName</pre>

<pre>-T is telling the script what action to take, in this case, we are just writing the hostname to a file so we want to use the ‘Document’ or D option.

-a is the alert type, in this case Mimikatz

–c is the hostname taken from the alert.</pre>

This is reflected across all DLL alerts. So when mimikatz is ran, the output file will have 5 hostnames written there.

Now let’s take a look at our Mimikatz rule.

{% gist d103dc36af8212df7cb25e5dc3dfda9c mimikatz.yaml %}

This alert uses frequency as well as a different index. ElastAlert actually has its own index that indexes everytime an alert is queried. So now we can check this index if all five of the DLL alerts were fired in less than one second. It does this by filtering for only the DLL rules, only returning those with the alert_sent flag set to true, and alerting only if identifies 5 results within 1 second.

[You will need to generate a Slack Web Hook and replace &#8216;SLACKWEBHOOK&#8217; with your web hook. ][6]

The alert is a command calling our Python script again:

<pre>python3 /bin/py-alert.py –T S –S SLACKWEBHOOK –a Mimikatz –t 5</pre>

<pre>-T is telling the script that we want to perform a ‘Send’ action.

-S needs to have our Slack Web Hook

-a tells the script what detection type we are alerting on

-t tells the script to only alert if there are 5 or more unique hostnames in our output file.</pre>

This last part is important. This number should always be the amount of rules that make up your grouping.

Run Mimikatz:

<img src="/images/2018/01/1.png">

Profit:

<img src="/images/2018/01/2.png>
          
One thing to keep in mind is that these have been tested in a lab environment with a small population of end points. Deploying this in production will likely involve major tuning.

Check out these labs: <https://cyberwardog.blogspot.com/2017/02/setting-up-pentesting-i-mean-threat_98.html> for an in-depth guide on how to set this stuff up manually as well as build the lab around it.

If you run into any issues, feel free to reach out to me on [Twitter][7] or by [email][8]!

_The advice and scripts contained and referenced in this point are provided with no warranty. As always, never blindly trust scripts off the internet._

&nbsp;

 [1]: https://cyberwardog.blogspot.com/2017/03/chronicles-of-threat-hunter-hunting-for.html
 [2]: https://sqrrl.com/threat-hunting-reference-guide/
 [3]: https://jordanpotti.com/2017/12/22/using-elastalert-to-help-automate-threat-hunting/
 [4]: https://github.com/Cyb3rWard0g/HELK
 [5]: https://github.com/ion-storm/sysmon-config/blob/master/sysmonconfig-export.xml
 [6]: https://api.slack.com/incoming-webhooks
 [7]: https://twitter.com/ok_bye_now
 [8]: mailto:admin@jordanpotti.com
