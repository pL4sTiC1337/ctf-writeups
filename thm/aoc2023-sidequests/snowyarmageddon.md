# Snowy ARMageddon
<img src="https://tryhackme-images.s3.amazonaws.com/room-icons/f54ecb7d1f43f336714d5b8363d02834.png" height="150">

## Breaching the CyberPolice perimeter
Ho ho, lads and lasses! Van Spy's whispers are golden! By pickin' apart the packet data we've sniffed out a mysterious device in the CyberPolice building. Seems like this old thing's been forgotten in their tech upgrade blizzard. It's as neglected as a lone iceberg.

This clunky old device is a buzzin', ripe for the pickin'. If we can get our claws into this enigmatic device, we'll have ourselves a sly backdoor right into the CyberPolice's icy fortress. Imagine the treasure troves of secrets just waitin' to be plundered! And oh, the cherry on top – we might just finagle our way into Frost-eau's laptop. That'll spill the beans on this Best Festival Company and AntarctiCrafts merger. McSkidy, that clever elf who snagged me last time, ought to keep an eye over her shoulder!

Remember, though, nothin' in the South Pole comes easy. But am I, the Bandit Yeti, the one to back down from a challenge? Never! Let's roll up our sleeves and dive into this digital snowstorm. The Bandit Yeti don't wait for no winter!

### The Yeti Hints!
Alright, my frosty friend, it's time to gear up for a simulated red team engagement. Think like the Yeti – be stealthy, keep it low-profile. We're treadin' through digital snow here, so make sure to minimize any detectable noise. It's like stalkin' a penguin in a blizzard; you gotta be silent, you gotta be sneaky. Now, if you get noisy at any point, don't get all ruffled if you hit a wall and find yourself blocked. It happens to the best of us, even the Bandit Yeti. Sometimes you gotta backtrack to the start, rethink your strategy. It's all part of the icy dance.

Your main target? Access that internal-only web application. That's where the juicy stuff is hidden. Now, gettin' full privileges on the machine – that's a tasty bonus, but don't sweat it if it's out of reach. The key is to complete the mission without kickin' up a snowstorm.

Remember, this is all about bein' as silent as the falling snow and as cunning as the arctic fox. Ready? Let's dive into this digital blizzard and show 'em what the Bandit Yeti's made of!

### Questions
1. What is the content of the first flag?
2. What is the content of the `yetikey2.txt` file?

## Writeup

### Reconaissance
First things first, let's launch the challenge VM and see what ports we have open.
```
PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 5e:43:0f:fe:f5:6f:02:d6:92:1d:42:fe:10:3c:31:05 (RSA)
|   256 d7:16:80:64:ed:1e:29:4a:87:94:63:5a:fe:f1:be:8e (ECDSA)
|_  256 8b:e5:fd:06:3e:be:15:1a:0b:a9:a8:e4:98:26:80:35 (ED25519)
23/tcp    open  tcpwrapped
8080/tcp  open  http       Apache httpd 2.4.57 ((Debian))
|_http-title: TryHackMe | Access Forbidden - 403
|_http-server-header: Apache/2.4.57 (Debian)
50628/tcp open  tcpwrapped
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
#### Port 22 - SSH
We don't have much to exploit this yet.

#### Port 23
Port 23 is normally utilized by Telnet.  Tried connecting with `nc -v $IP 23`, but the server killed the connection immediately; tried this a few times with the same result.  Maybe we'll come back to this later.

#### Port 8080
Seems to be some sort of protection on this website.  Without any further enumeration or use of tools, every attempt to poke around the website leads me to the following error:
![only elves](sq2-pic1.png)

#### Port 50628
A quick check of this port yields something potentially useful!  Seems we have a Trivision NC-227WF HD 720P.
![webcam](sq2-pic2.png)

### IP Camera Exploitation
A quick Google search for Trivision 227WF exploits brings me to https://pierrekim.github.io/blog/2017-03-08-camera-goahead-0day.html. This blog post mentions about 7 potential exploits worth checking out:

* CVE-2017-8224 - Backdoor account
* CVE-2017-8222 - RSA key and certificates
* CVE-2017-8225 - Pre-Auth Info Leak (credentials) within the custom http server
* Authenticated RCE as root
* Pre-Auth RCE as root
* CVE-2017-8223 - Misc - Streaming without authentication
* CVE-2017-8221 - Misc - "Cloud" (Aka Botnet)

Unfortunately, none of these really seemed to pan out.  Started digging deeper, and included the ARM architecture clue in the room name into my searches.  I came across the following write-up: https://no-sec.net/arm-x-challenge-breaking-the-webs/.  Same camera, bingo!

