# Nmap: Scanning Multiple IP Addresses Simultaneously

This was my first real hands-on lab with **Nmap** (Network Mapper), done on [LabEx](https://labex.io/labs/nmap-how-to-scan-multiple-ip-addresses-simultaneously-using-nmap-in-cybersecurity-414798) as practice alongside my Cisco NetAcad, IBM SkillsBuild, and TS Academy coursework. I'd read about network scanning in theory before, but this was the first time I actually ran the commands myself in a live terminal and saw what the output looks like.

## Why I did this lab

Understanding what's on a network, which hosts are alive, what ports are open, is one of the most basic but important skills in cybersecurity. I wanted to move past just reading about it and actually get comfortable typing the commands and reading real scan output, since that's what I'll need for any future security work.

## 1. Installing Nmap

First step was getting the tool installed. After opening the terminal, I refreshed the package lists on my system. I didn't really understand the need for that so I browsed it and I realized it was to let my system know about the latest available software. These are the commands I used:

```bash
sudo apt update
sudo apt install nmap -y
```

Then I checked it installed correctly:

```bash
nmap --version
```

Nothing complicated here, but it was satisfying to see the version number come back and know I had a real working copy of a tool actual security professionals use.

## 2. My first scan

I started with the simplest possible scan, against my own machine:

```bash
nmap localhost
```

I could see open ports (like `22/tcp` for SSH) and understand that this is because I was running a secure terminal session.

## 3. Scanning a range of IPs

Then I moved on to scanning more than one address at once, which is really the point of this lab:

```bash
nmap 127.0.0.1-5
```

This scans 5 addresses in one go instead of running the command five separate times. Small thing, but it's the kind of efficiency that matters when I'm dealing with a real network with dozens or hundreds of devices.

## 4. Using CIDR notation

I'd seen CIDR notation (like `/24`) mentioned in my networking coursework, but this was the first time I actually used it practically:

```bash
nmap 127.0.0.0/24
```

This scans all 256 addresses in that block at once. Once I ran it and saw 256 results come back, the concept of "the first 24 bits are fixed, the last 8 vary" finally felt concrete instead of just something I'd memorized for a quiz.

## 5. A faster way to just check who's alive

Toward the end I learned about `-sn`, which skips port scanning entirely and just checks if hosts respond:

```bash
nmap -sn 127.0.0.0/24
```

## My results

Here's what I actually got when I ran these:

**Single host scan (`nmap localhost`):**
```
PORT     STATE SERVICE
22/tcp   open  ssh
3001/tcp open  nessus

Nmap done: 1 IP address (1 host up) scanned in 0.03 seconds
```

**IP range scan (`nmap 127.0.0.1-5`):**
All 5 addresses came back up, each with the same two open ports:
```
Nmap done: 5 IP addresses (5 hosts up) scanned in 0.11 seconds
```

**CIDR ping scan (`nmap -sn 127.0.0.0/24`):**
All 256 addresses came back as up — makes sense since this whole block loops back to my own machine:
```
Nmap done: 256 IP addresses (256 hosts up) scanned in 0.04 seconds
```

**What stood out to me:** the ping scan across 256 addresses (0.04s) was actually *faster* than the full port scan of just 5 addresses (0.11s). At first that seemed backwards, more addresses, less time? But it made sense once I understood `-sn` skips port checking entirely and just tests reachability, which is a lot cheaper per host. That's exactly why it's used as a first step before running a slower, detailed scan on a smaller, confirmed-live set of hosts because you don't waste time port-scanning something that isn't even online.

## Quick reference

- `nmap <target>` — scan a single host
- `nmap <start>-<end>` — scan a range of addresses
- `nmap <network>/<CIDR>` — scan a whole subnet
- `nmap -sn <target>` — quick host discovery (no port scan)
