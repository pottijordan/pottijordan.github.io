---
title: Using Elastic Curator To Clean Up ELK
author: jp
type: post
date: 2017-12-29T13:35:12+00:00
url: /2017/12/29/using-elastic-curator-to-clean-up-elk/
categories:
  - Uncategorized
tags:
  - curator
  - elastic
  - ELK

---
I recently setup ELK in order to begin collecting logs from Sysmon for security monitoring in my lab. The problem I could foresee running into was the issue of disk space. Unfortunately when my ELK server runs out of space, it runs out of space. I needed a way to clean up the logs when the server began to reach a threshold. This led me to [Elastic Curator][1]. Curator allows us to manage our indices which includes deleting indices over a given number of days ago. However, this is just a one-off command we can run so I wanted to add some logic to the process.

_Disclaimer: This is strictly for one node setups, if you have a large setup with multiple clusters you will want a different solution. (You can use this method still but you might get some weird issues)_

Install Curator:

<pre>sudo pip install elasticsearch-curator</pre>

Create Curator Directory:

<pre>sudo mkdir /etc/curator</pre>

Create config file:

<pre>sudo nano /etc/curator/config.yml</pre>

Paste in:

<div class="gist-oembed" data-gist="1b1383fa89f94d58af903643b3d9eee9.json" data-ts="8">
</div>

Create delete config action file:

<pre>sudo nano /etc/curator/delete-after.yml</pre>

Paste in:

<div class="gist-oembed" data-gist="8265b17406211daf7a7d6a656d57c3d7.json" data-ts="8">
</div>

Unless you followed this [guide][2] you will most likely have to change some of the details in this action config file. Namely the filters &#8211;> value field. You will need to put the name of your index here.

Create Script file:

<pre>sudo nano /etc/curator/cleanup.sh</pre>

Paste in:

<div class="gist-oembed" data-gist="988aa89cc5a5cbe6b92a215147050d87.json" data-ts="8">
</div>

_Warning: The above script will check that the disk space is less than 80 percent. It will check the free space of the root or whatever drive is mounted on &#8220;/&#8221;. If the free space is less than 20 percent, it will begin to delete the oldest indices, once it deletes the oldest one, it will check again and delete the next oldest until the disk space usage is below 80 percent. It will stop deleting regardless of disk space if there is less than 2 days of indices left. Since I couldn&#8217;t find an easy way to simple delete the oldest indices, I start at 90 days ago and move forward until it starts to find indices, if you have more than that, please adjust the script (the days variable at line 8). You may also want more than 2 days as a safety net._

Add script to cron:

<pre>sudo su</pre>

<pre>(crontab -l <span class="pl-k">2&gt;</span>/dev/null<span class="pl-k">;</span> <span class="pl-c1">echo</span> <span class="pl-s"><span class="pl-pds">"</span>5 0 * * * /etc/curator/cleanup.sh<span class="pl-pds">"</span></span>) <span class="pl-k">|</span> crontab -</pre>

This cronjob will run the script 5 minutes after midnight forever.

Curator should be added to the [HELK][3] soon. If you aren&#8217;t aware of the [HELK][3] and want to get into Threat Hunting (Or just want a super quick way to spin up and ELK stack) you should definitely look into the [HELK.][3] The incredible HELK (Hunting, Elasticsearch, Logstash, Kibana) makes it is easy as running a script to setup a reliable ELK stack tailored for threat hunting.

Check out these labs: <https://cyberwardog.blogspot.com/2017/02/setting-up-pentesting-i-mean-threat_98.html> for an in-depth guide on how to set this stuff up manually as well as build the lab around it.

If you run into any issues, feel free to reach out to me on [Twitter][4] or by [email][5]!

_The advice and scripts contained and referenced in this point are provided with no warranty. As always, never blindly trust scripts off the internet let alone throw them into a cron job running as root. _

&nbsp;

 [1]: https://github.com/elastic/curator
 [2]: https://cyberwardog.blogspot.com/2017/02/setting-up-pentesting-i-mean-threat_98.html
 [3]: https://github.com/Cyb3rWard0g/HELK
 [4]: https://twitter.com/ok_bye_now
 [5]: mailto:admin@jordanpotti.com