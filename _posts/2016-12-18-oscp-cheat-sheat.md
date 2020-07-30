A place to gather tips and general knowledge/tools that I have found useful for the Pentesting With Kali course. This definitely does not have any new information here and there are a ton of good sites with the “cheat sheets” but I have found that making my own is so much more useful. Also, you might ask why I am making it public if it is nothing new and there are much more comprehensive ones out there? Well That is a good question and I am really just doing it for myself. If I post it on my website, I will be much more likely to make it neat and easy to understand. otherwise, I would always be referring to my sloppy put together on the fly note sheet which I can barely make sense of.

The Offensive Security folks recommend Keepnote for note taking. I ended up using OneNote from Microsoft and I thought that worked phenomenal. It has pretty good organizational features  and the search function is awesome since it can search images for text, which is super helpful when you have tons of screenshots but forgot to write something down.

None of this stuff is original. Big thanks to the people that run the sites I have listed!

A tidbit for when you have command execution and you want to upload an ftp config file or upload a wget vbs script to get an interactive shell, don’t copy paste in each “echo” 50 times. Using Burp Intruder you can do it in only a couple seconds. I go over how to do it here.

## Enumeration:

**General Enumeration:**

`nmap -vv -Pn -A -sC -sS -T 4 -p- 10.0.0.1`

**Web Enumeration:**

`dirb http://10.0.0.1 /usr/share/wordlists/dirb/common.txt`

`nikto –host http://10.0.0.1`

**SMB\RPC Enumeration:**

`enum4linux 10.0.0.1`

`nmap –script=smb-enum-domains.nse,smb-enum-groups.nse,smb-enum-processes.nse,smb-enum-sessions.nse,smb-enum-shares.nse,smb-enum-users.nse,smb-ls.nse,smb-mbenum.nse,smb-os-discovery.nse,smb-print-text.nse,smb-psexec.nse,smb-security-mode.nse,smb-server-stats.nse,smb-system-info.nse,smb-vuln-conficker.nse,smb-vuln-cve2009-3103.nse,smb-vuln-ms06-025.nse,smb-vuln-ms07-029.nse,smb-vuln-ms08-067.nse,smb-vuln-ms10-054.nse,smb-vuln-ms10-061.nse,smb-vuln-regsvc-dos.nse,smbv2-enabled.nse 10.0.0.1`

**Mysql Enumeration:**

`nmap -sV -Pn -vv –script=mysql-audit,mysql-databases,mysql-dump-hashes,mysql-empty-password,mysql-enum,mysql-info,mysql-query,mysql-users,mysql-variables,mysql-vuln-cve2012-2122 10.0.0.1 -p 3306`

**SMTP Enumeration:**

`nmap –script=smtp-commands,smtp-enum-users,smtp-vuln-cve2010-4344,smtp-vuln-cve2011-1720,smtp-vuln-cve2011-1764 -p 25 10.0.0.1`

**SNMP Enumeration:**

`snmpwalk -c public -v1 10.0.0.0`

**FTP Enumeration:**

`nmap –script=ftp-anon,ftp-bounce,ftp-libopie,ftp-proftpd-backdoor,ftp-vsftpd-backdoor,ftp-vuln-cve2010-4221,tftp-enum -p 21 10.0.0.1`

## Password Cracking:

 Use hash-identifier to determine the hash type.

https://hashkiller.co.uk

Paste the entire /etc/shadow file in a test file and run john with the text file after john.

`john hashes.txt`

`hashcat -m 500 -a 0 -o output.txt –remove hashes.txt /usr/share/wordlists/rockyou.txt`

*Bruteforcing:*

`hydra 10.0.0.1 http-post-form “/admin.php:target=auth&mode=login&user=^USER^&password=^PASS^:invalid” -P /usr/share/wordlists/rockyou.txt -l admin`

`hydra -l admin -P /usr/share/wordlists/rockyou.txt -o results.txt ssh://10.0.0.1`

## Tunneling:

`sshuttle -r root@10.0.0.1 10.10.10.0/24`

sshuttle is an awesome tunneling tool that does all the hard work for you. It gets rid of the need for proxy chains. What this command does is tunnels traffic through 10.0.0.1 and makes a route for all traffic destined for 10.10.10.0/24 through your sshuttle tunnel.

## AV Bypass:

`root@kali:~/Hyperion-1.0# wine hyperion.exe ../backdoor.exe ../backdoor_mutation.exe`

(This is a general example of how to evade AV)

## General Meterpreter Stuff

`set AUTORUNSCRIPT post/windows/manage/migrate`

##Privilege Escalation:

**Linux:**

https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/

https://github.com/pentestmonkey/unix-privesc-check

**Windows:**

https://github.com/pentestmonkey/windows-privesc-check

http://www.fuzzysecurity.com/tutorials/16.html

## Shell Generation:

Common Meterpreter Payloads:
http://netsec.ws/?p=331

Sometimes, if you have code execution but nc shells don’t seem to be working, no need to worry, you have a ton of other options. (Pep talk to myself)

https://highon.coffee/blog/reverse-shell-cheat-sheet/

If you have a jenky shell, you may need to spawn a tty shell manually.

Spawning a TTY Shell:
https://netsec.ws/?p=337
