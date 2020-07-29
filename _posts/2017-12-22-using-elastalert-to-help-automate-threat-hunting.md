---
title: Using ElastAlert to Help Automate Threat Hunting
author: jp
type: post
date: 2017-12-22T19:43:31+00:00
url: /2017/12/22/using-elastalert-to-help-automate-threat-hunting/
categories:
  - elastic
  - ELK
  - threat hunting
---

I first want to say thanks to CyberWarDog for his fantastic lab walk through for setting up a Threat Hunting Lab. It is hands down the best guide I have read to getting started with Threat Hunting. I followed his guide and got my lab completely setup. I then decided that Elastalert would be pretty nice for getting some of the highly likely IOC’s sent off to a security team for further analysis. This post will guide you through setting up Elastalert to get notifications when certain actions are logged.

This guide assumes you have gone through all parts of CyberWarDogs tutorials: <https://cyberwardog.blogspot.com/2017/02/setting-up-pentesting-i-mean-threat.html>

Not required, but it also assumes that you have set up Enhanced Powershell Logging so that we can begin to capture useful PowerShell data. <https://cyberwardog.blogspot.com/2017/06/enabling-enhanced-ps-logging-shipping.html>

Also not required but useful for this guide: A Slack Channel.

Cool, ready to go?

  * SSH or Console into your Ubuntu server running your ELK stack.
  * Download Elastalert from Yelp’s GitHub.

<pre>git clone <a href="https://github.com/yelp/elastalert">https://github.com/yelp/elastalert</a></pre>

  * Copy Elastalert to ‘/etc/’

<pre>sudo cp -r elastalert /etc/</pre>

  * Change directory into your new Elastalert directory.

<pre>cd /etc/elastalert</pre>

  * If not already installed, install pip.

<pre>sudo apt install python-pip</pre>

  * Install Elastalert

<pre>pip install elastalert</pre>

  * Install ElasticSearch-py
<li style="list-style-type: none;">
  <pre>pip install <span class="pl-s"><span class="pl-pds">"</span>elasticsearch&gt;=5.0.0<span class="pl-pds">"</span></span></pre>
</li>

  * Install dependencies:

<pre>pip install -r requirements.txt</pre>

  * Lets make a directory for our Elastalert templates:

<pre>sudo mkdir templates</pre>

  * Change directory into our new templates directory

<pre>cd templates</pre>

  * Create a new template for monitoring commands executed:

<pre>sudo nano cmd_template.yaml</pre>

Paste:

<pre>es_host: localhost
es_port: 9200
name: "PLACEHOLDER"
index: winlogbeat-*
filter:
- terms:
    event_data.CommandLine: ["PLACEHOLDER"]
type: any
alert:
- slack
slack_webhook_url: "SLACK_WEB_HOOK"</pre>

**es_host**: This is the host your ELK stack is running on.

**es_port**: This is the port Elastic Search is listening on.

**index**: This is the index you setup with CyberWarDog’s blog.

**filter**: This is tell Elastalert to filter its search, in this case, we are filtering with ‘terms’ and we are looking for ‘event_data.CommandLine’ that equals whatever we put in place for PLACEHOLDER.

**type**: This means that Elastalert should alert on an matches that our Filter hits. We could also specify this Type to alert on new values identified, a spike in certain logs, a lack of logs and a bunch of other cool things.

**alert**: This tells elastalert how to alert you! There are a bunch of ways to get these alerts and I chose Slack for its simplicity to set up and use. For more options you can visit: <http://elastalert.readthedocs.io/en/latest/ruletypes.html#alerts>

  * Create a new template for monitoring powershell commands executed:

<pre>sudo nano powershell_template.yaml</pre>

Paste:

<pre>es_host: localhost
es_port: 9200
name: "PLACEHOLDER"
index: winlogbeat-*
filter:
- terms:
    powershell.scriptblock.text: ["PLACEHOLDER"]
type: any
alert:
- slack
slack_webhook_url: "SLACK_WEB_HOOK"</pre>

  * Create your main config.yaml file.

<pre>cd ..</pre>

<pre>sudo nano config.yaml</pre>

Paste:

<pre>rules_folder: alert_rules
run_every:
    seconds: 30
buffer_time:
    seconds: 60
es_host: localhost
es_port: 9200
alert_time_limit:
    days: 1
writeback_index: elastalert_status
alert_text: "Username: {0} \nHost: {1} \nTime: {2} \nLog:{3}"
alert_text_type: alert_text_only
alert_text_args: ["user.name","host", "@timestamp","log_name"]</pre>

_To change the body of the alert, you can modify the last three lines, you can add or remove attributes to include in your report. <https://elastalert.readthedocs.io/en/latest/ruletypes.html#alert-content>_

  * Create our Rules directory:

<pre>sudo mkdir alert_rules</pre>

<pre>cd alert_rules</pre>

  * Copy our templates here:

<pre>sudo cp ../templates/* .</pre>

  * Make copies of our templates.

<pre>cp cmd_template.yaml cmd_whoami.yaml</pre>

<pre>cp powershell_template.yaml powershell_invoke_webrequest.yaml</pre>

  * Modify cmd_whoami.yaml to alert when whoami is executed.

<pre>sudo nano cmd_whoami.yaml</pre>

  * Replace the PLACEHOLDER text in both locations with ‘whoami’, you can also copy this file many times over to alert on multiple commands ran.

<pre>es_host: localhost
es_port: 9200
name: "whoami"
index: winlogbeat-*
filter:
- terms:
 event_data.CommandLine: ["whoami"]
type: any
alert:
- slack
slack_webhook_url: "SLACK_WEB_HOOK"</pre>

  * Replace SLACK\_WEB\_HOOK with your Slack Web Hook. <https://get.slack.help/hc/en-us/articles/115005265063-Incoming-WebHooks-for-Slack>
  * Modify powershell\_invoke\_webrequest.yaml to alert when Invoke-WebRequest is executed.

<pre>sudo nano powershell_invoke_webrequest.yaml</pre>

  * Replace the first Placeholder with ‘Invoke-WebRequest’ and the second Placeholder with “invoke”, “webrequest”.
  * Replace SLACK\_WEB\_HOOK with your Slack Web Hook. <https://get.slack.help/hc/en-us/articles/115005265063-Incoming-WebHooks-for-Slack>

<pre>es_host: localhost
es_port: 9200
name: "invoke-webrequest"
index: winlogbeat-*
filter:
- terms:
    powershell.scriptblock.text: ["webrequest"]
type: any
alert:
- slack
slack_webhook_url: "SLACK_WEB_HOOK"</pre>

_Only query lowercase terms._

  * Remove the two template files we copied over:

<pre>sudo rm *template.yaml</pre>

  * Run elastalert-create-index and follow the prompts

<pre>elastalert-create-index</pre>

_Remember: You host is localhost and your port is 9200, if you followed CyberWarDog’s guide, you also do not have authentication set up for ElasticSearch (You used nginx instead) so leave username and password empty. You also don’t have SSL or TLS setup._

  * Change directory back to /etc/elastalert

<pre>cd /etc/elastalert</pre>

  * Run elastalert –verbose

<pre>elastalert --verbose</pre>

  * Go to your Windows machine running winlogbeat and open up your command prompt.
  * Enter whoami and monitor your slack.

<pre>whoami</pre>

  * Profit

<img src="/images/2017/12/word-image.png">

Commands you may want to monitor for:

<pre>Whoami

Netstat

Wmic</pre>

Powershell Functions you may want to monitor on:

<pre>Invoke-WebRequest

Invoke-Obfuscation

Downloadstring

Invoke-ShellCommand</pre>

_If you are going to take this Threat Hunting thing seriously, you will most likely want to add alerts for Spikes, Frequency, Cardinality and a billion other types of things that are good ideas to check for with any Production system._

_For comments, questions, concerns you can reach me at [Twitter][1] or via [Email][2]_

[UPDATE: Several issues fixed 12/26]

 1: <https://twitter.com/ok_bye_now>
 
 2: <mailto:admin@jordanpotti.com>
