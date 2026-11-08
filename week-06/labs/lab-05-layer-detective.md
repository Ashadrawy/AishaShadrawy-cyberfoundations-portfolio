# Week 6 Lab 05 — Layer Detective

**Student Name:** Aisha Shadrawy

**Date Completed:** November 07, 2026

**Module:** 2 — Networking & Cloud Foundations | **Week:** 6  
**Submission Path:** `week-06/labs/lab-05-layer-detective.md`

---

## Overview

**This is a SHORT lab — 20 to 30 minutes — and it needs no VM.** No Cloud Heights session, no simulator, no screenshot. This is a thinking lab: you take the evidence you have already collected in Weeks 5 and 6 and sort it into layers.

This is an **independent** lab.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | This worksheet only — nothing to start, nothing to connect to |
| Prerequisite | Week 5 labs and Week 6 Labs 01–04 |
| Screenshot | None required |

---

## Part A — The Seven-Row Table

Fill in every row. For the last column, name one **real thing you personally saw** in Weeks 5–6 that belongs at that layer.

| # | Layer name | One-line job | Real thing from Weeks 5–6 |
| --- | --- | --- | --- |
| 7 | Application | lets programs use the network | curl showing the Grid Beacon webpage |
| 6 | Presentation | formats and encrypts data | TLS encryption in the packet capture |
| 5 | Session | starts and manages connections | My SSH login session |
| 4 | Transport | Uses ports to deliver data | TCP port 443 |
| 3 | Network | Uses IP addresses to route traffic | 10.60.6.4 and ip route |
| 2 | Data link | Moves data on the local network | My VM's network interface |
| 1 | Physical | carries signals through cables or wireless connections | the virtual network's underlying hardware |

---

## Part B — Case Files

For each case, name the layer where the problem lives, and name the evidence proving the layers **below** it were already working.

### Case File 1 — The Name That Went Nowhere

A hostname lookup fails, but pinging the machine's IP address directly succeeds.

Layer:

```
Application
```

Evidence that the layers below were working:

```
Pinging the IP address worked, so the machine could communicate over the network. The problem appears to be the DNS resolving the hostname.
```

### Case File 2 — Permission Denied

`ssh` to a host returns `Permission denied` after a password prompt.

Layer:

```
Application
```

Evidence that the layers below were working:

```
SSH reached the server and asked for a password. This shows the network connection worked, but the authentication failed. 
```

### Case File 3 — The Cable Story

A machine reports no link on its interface and has no address at all.

Layer:

```
Physical
```

Evidence and reasoning:

```
The machine reports no network link and has no IP address. I would check the physical or virtual connection first.
```

### Case File 4 — Ping Works, The Page Does Not

`ping` to a server succeeds, but `curl http://<that server>` returns nothing useful.

Layer:

```
Application
```

Evidence that the layers below were working:

```
Ping worked, so the server was reachable through the network. I would check whether the web service is running and whether its port is accessible. 
```

### Case File 5 — Wrong Neighbourhood

A machine has an address, but its default route points somewhere that cannot forward its traffic.

Layer:

```
Network
```

Evidence and reasoning:

```
The machine has an IP address, but its default route points to the wrong place. This prevents traffic from reaching other networks. 
```

---

## Part C — The Silent Gateway Case

In Lab 03 the Azure default gateway did not answer your ping. However, your VM had a valid default route configured, and your local communication with the Grid Beacon — the ping replies, the HTTP banner, and `TRACE ID: CF-NET-0604` — succeeded.

A failed gateway ping is one piece of evidence — not automatically proof of a gateway or network failure. But the evidence you weigh against it has to be the right kind of evidence.

The Grid Beacon at `10.60.6.4` sits on the same local subnet as your VM (`10.60.6.0/26`). Reaching it proves **local-subnet connectivity** — that traffic never crosses the default gateway, so beacon success alone cannot prove the gateway forwarded anything. Your `ip route` output proves a **default route is configured** — your VM knows where it intends to send non-local traffic — but it does not prove the gateway forwarded that traffic. The evidence that demonstrates the **default path is functioning** is successful communication with a destination outside `10.60.6.0/26`, such as the outbound internet access through NAT that you examined in Lab 04.

### Step 1 — Rule on the Case

Is the failed gateway ping enough evidence to declare a network-layer failure? Explain your answer using the other evidence you collected. In your response, distinguish between:

- evidence that proves **local-subnet connectivity**
- evidence that proves a **default route is configured**
- evidence that supports **successful off-subnet connectivity**

```
No, a failed gateway ping doesn't prove the network is broken. The Grid Beacon at 10.60.6.4 responded, proving my local network connection worked. The ip route command showed that my VM had a default route configured. Successful internet access through NAT would show that traffic could also reach outside my subnet.
```

### Step 2 — Name the Correct Conclusion

For each of these four results, state what it actually proves: the Grid Beacon at `10.60.6.4` answering, the default route shown by `ip route`, a successful connection to a destination outside your local subnet, and the gateway's failed ping. Then state the rule you would give a junior colleague about the difference between an observation ("the gateway did not answer my ICMP probe") and a diagnosis ("the gateway is broken"):

```
The Grid Beacon responds: proves my VM can communicate within its local subnet
ip route shows a default route: proves my VM knows where to send traffic outside its subnet.
An outside destination responds: proves my VM can communicate beyond its local subnet.
Gateway ping fails: proves the gateway did not answer the ping, but it may still be working.

The rule I would give a junior colleague: Just because something doesn't respond does not mean its broken. A failed ping is an observation, not a diagnosis. Run other tests before deciding there's a network problem. 
```

---

## Part D — Two Models, One Job

The OSI model has seven layers. The practical TCP/IP model most engineers speak day to day has four or five.

### Step 1 — Map Them

Briefly show how the seven OSI layers collapse into the practical model:

```
7: Application - Application
6: Presentation - Application
5: Session - Application
4: Transport - Transport
3: Network - Internet
2: Data Link - Network Access
1: Physical - Network Access
```

### Step 2 — When Each Is Useful

Explain when the seven-layer vocabulary helps and when the practical model is the better tool:

```
The seven-layer OSI model helps me understand exactly where a network problem might be happening. The TCP/IP model is simpler and more useful when working with real networks. Both help me troubleshoot, but one is more detailed.
```

---

## Analysis Questions

**Analysis Question 1.** Explain the Ladder Rule using layer language. What does "test the near thing first" mean when the rungs are layers? *(Minimum 3 sentences.)*

```
The Ladder Rule means checking the lower layers before moving to the higher ones. I would first check whether my machine has a working connection and IP address. Then I would test routing, ports, and applications to find where the problem starts.
```

**Analysis Question 2.** Why is "which layer is this?" a faster question than "what is broken?" when you are under pressure? *(Minimum 3 sentences.)*

```
Asking which layer helps me narrow down the problem. Instead of checking everything, I can focus on the area that might be failing. This saves time and makes troubleshooting easier.
```

**Analysis Question 3.** Pick one case file from Part B and describe the very next command you would run to confirm your ruling, and what result would change your mind. *(Minimum 2 sentences.)*

```
I would choose Case File 1, where the hostname lookup failed. I would run 'dig hostname' to check whether DNS can find the IP address. If it returns the correct IP address, I would reconsider my diagnosis because DNS may be working and the problem could be somewhere else. 
```

---

## Submission Checklist

- [x] All seven rows of the OSI table completed with a real Week 5–6 anchor each (Part A)

- [x] All five case files given a layer and supporting evidence (Part B)

- [x] Silent gateway case ruled on correctly (Part C)

- [x] OSI vs. practical TCP/IP model compared (Part D)

- [x] All three Analysis Questions answered (minimum sentence counts met)

- [x] No screenshot required for this lab

- [x] This file is committed to your portfolio repo at `week-06/labs/lab-05-layer-detective.md`

---

## GitHub Commit Subsection

1. Open **Week 6 → Lab 05: Layer Detective** in the Lab Portal.
2. Fill in the worksheet fields.
3. Click **Submit to GitHub** — the Portal commits to `week-06/labs/lab-05-layer-detective.md`.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
