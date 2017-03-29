# awesome-ipmi
The one that just not exploit IPMI protocol.

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
Well you could use nmap or anything that can give open ports and services at a local or not so local network. Yes sir, you could also use commercial tools like Nessus or Nexpose but at the end of day is still poor security of IPMI that can allow an attacker to hack it in under 5 minutes (if he writes commands in terminal by hand and tends to make lots of mistakes).
But you know what? We can use Shodan (shodan.io) to search for a target (hei hei ... stop, stop .. be ethical!).

Hunting tactics > open pc > connect to the internet (sarcasm) > Log in / Register to Shodan > Search for port:623 (#facepalm, #hacking_like_a_pro, #hashtag, #even_more_sarcasm)
Well by doing that you will get like over 100.000 (> 100k) possible vulnerable servers from all around the world.
Don't you believe it? here is a picture of it:
![Sitting ducks for Hackers](https://preview.ibb.co/c506BF/ipmi.png)
