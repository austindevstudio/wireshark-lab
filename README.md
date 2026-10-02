<div align="center">

<h1>🧪 Lab — Wireshark &amp; Network Analysis</h1>

<h3>🦈 Hands-on Network Analysis Lab</h3>

<p><b>🎣 Capture → 🔍 Filter → 🔬 Analyze → 🔗 Reconstruct → 📝 Document</b></p>

![Tool](https://img.shields.io/badge/Tool-Wireshark-1679A7?style=for-the-badge&logo=wireshark&logoColor=white)
![Cost](https://img.shields.io/badge/Cost-%240-2ea44f?style=for-the-badge)
![Time](https://img.shields.io/badge/Time-2--4%20hrs-orange?style=for-the-badge)
![Author](https://img.shields.io/badge/Author-Adam%20Austin-8957e5?style=for-the-badge)

![Network+](https://img.shields.io/badge/CompTIA-Network%2B-red?style=flat-square)
![Security+](https://img.shields.io/badge/CompTIA-Security%2B-red?style=flat-square)
![CySA+](https://img.shields.io/badge/CompTIA-CySA%2B-red?style=flat-square)

</div>

---

## 📋 Lab Overview

| Field | Value |
|---|---|
| 👤 **Author** | **Adam Austin** |
| 🧪 **Lab** | Lab 2 — Wireshark & Network Analysis |
| 🎓 **Certification Alignment** | CompTIA Network+ · Security+ · CySA+ |
| 🦈 **Primary Tool** | Wireshark |
| 🖥️ **Environment** | Local Machine or Azure VM |
| 💸 **Cost** | **$0** |
| ⏱️ **Estimated Time** | 2–4 hours across multiple sessions |
| 💼 **Career Relevance** | Network Engineer · SOC Analyst · Cloud Security Engineer · Incident Responder |

### 🎯 Lab Objective

Learn to capture and analyze real network traffic using Wireshark, identify common protocols and connection patterns, apply display filters, inspect DNS and TCP behavior, reconstruct TCP conversations, and recognize why unencrypted HTTP traffic creates a security risk.

---

## 🧭 Table of Contents

- [🏗️ Architecture — How Wireshark Captures Traffic](#-architecture--how-wireshark-captures-traffic)
- [💼 The Business Problem This Lab Solves](#-the-business-problem-this-lab-solves)
- [📚 Key Concepts — Read Before Starting](#-key-concepts--read-before-starting)
- [🎯 What You Will Learn](#-what-you-will-learn)
- [🛠️ Step 1 — Install Wireshark](#-step-1--install-wireshark)
- [🧪 Step 2 — Your First Capture](#-step-2--your-first-capture)
- [🔎 Step 3 — Essential Display Filters](#-step-3--essential-display-filters)
- [🧪 Step 4 — Guided Exercises](#-step-4--guided-exercises)
- [🅰️ Exercise A — Capture a DNS Lookup](#-exercise-a--capture-a-dns-lookup)
- [🅱️ Exercise B — Watch the TCP Three-Way Handshake](#-exercise-b--watch-the-tcp-three-way-handshake)
- [🔓 Exercise C — Demonstrate Cleartext HTTP](#-exercise-c--demonstrate-cleartext-http)
- [🔗 Exercise D — Follow a Full TCP Stream](#-exercise-d--follow-a-full-tcp-stream)
- [💾 Step 5 — Save and Export Captures](#-step-5--save-and-export-captures)
- [🖥️ Bonus — Command-Line Capture with TShark](#-bonus--command-line-capture-with-tshark)
- [🧠 Verification — Prove You Can Do It](#-verification--prove-you-can-do-it)
- [📝 Lab Evidence Checklist](#-lab-evidence-checklist)
- [🧩 Troubleshooting](#-troubleshooting)
- [☁️ Cloud Engineering Connection](#-cloud-engineering-connection)
- [🚀 Portfolio Challenge](#-portfolio-challenge)
- [📊 Analyst Report Template](#-analyst-report-template)
- [🏁 Lab Completion Standard](#-lab-completion-standard)
- [🎓 Skills Demonstrated](#-skills-demonstrated)

---

# 🏗️ Architecture — How Wireshark Captures Traffic

The basic workflow is:

```text
┌──────────────────┐
│    Internet      │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ Router / Gateway │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ Network Interface│
│  Wi-Fi / Ethernet│
└────────┬─────────┘
         │
         │ Packets
         ▼
┌──────────────────┐
│    Wireshark     │
│                  │
│ Capture → Filter │
│ Analyze → Follow │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ Analyst Findings │
│                  │
│ DNS / TCP / HTTP │
│ Errors / IOC /   │
│ Connectivity     │
└──────────────────┘
```

### 🧠 Important Mental Model

Wireshark does **not** create network traffic. It observes traffic that reaches a network interface and records it for analysis.

Think of it like a security camera:

> **Network traffic = activity**  
> **Network interface = camera location**  
> **Wireshark = recording and analysis system**

On a modern switched network, you generally see traffic involving your own host plus broadcast/multicast traffic. Seeing traffic from other hosts typically requires an appropriate capture point, such as a SPAN/port-mirroring configuration.

---

# 💼 The Business Problem This Lab Solves

Networks carry critical business activity:

- Authentication
- API requests
- Database connections
- Email
- File transfers
- Web traffic
- Cloud service communication
- Monitoring and management traffic

When a service becomes unreachable, an application becomes slow, or a security alert fires, network evidence can help determine **what actually happened**.

Wireshark allows an analyst to inspect captured traffic at multiple layers, from Ethernet frames through network and transport protocols to application-level data when it is not encrypted.

| Role | How This Lab Applies |
|---|---|
| 🔧 **Network Engineer** | Diagnose connectivity issues and inspect connection behavior |
| 🛡️ **SOC Analyst** | Investigate suspicious traffic patterns and potential indicators of compromise |
| ☁️ **Cloud Security Engineer** | Build packet-level networking intuition that transfers to Azure Network Watcher and flow logs |
| 🎧 **Help Desk / IT Support** | Determine whether an issue is client-side, network-side, or server-side |
| 🚨 **Incident Responder** | Reconstruct network conversations from packet captures |

---

# 📚 Key Concepts — Read Before Starting

## 📦 1. What Is a Packet?

A **packet** is a unit of network communication.

When you load a webpage, send an API request, or communicate with another computer, the data is transported through a series of network messages rather than one giant block.

Depending on the protocol and network layer, you'll encounter:

- Ethernet frames
- IP packets
- TCP/UDP segments or datagrams
- Application-layer data

Wireshark displays these layers so you can move from:

```text
Frame
  ↓
Ethernet
  ↓
IP
  ↓
TCP / UDP / ICMP
  ↓
Application Protocol
  ↓
Payload
```

---

## 📡 2. What Is a Network Protocol?

A **protocol** is a defined set of rules for communication between systems.

Common protocols you'll encounter in this lab:

| Protocol | Purpose |
|---|---|
| 🌐 **DNS** | Resolves names to IP addresses |
| 🔓 **HTTP** | Transfers web content without TLS encryption |
| 🔒 **HTTPS** | HTTP protected by TLS |
| 🤝 **TCP** | Reliable, connection-oriented transport |
| ⚡ **UDP** | Connectionless transport |
| 📡 **ICMP** | Network diagnostics and control messaging |

---

## 🤝 3. What Is the TCP Three-Way Handshake?

Before normal TCP data exchange begins, TCP establishes a connection using three steps:

```text
CLIENT                              SERVER
  │                                    │
  │──────────── SYN ──────────────────►│
  │                                    │
  │◄────────── SYN + ACK ──────────────│
  │                                    │
  │──────────── ACK ──────────────────►│
  │                                    │
  │       TCP connection established   │
```

### 📨 The three packets

| Packet | Flags | Meaning |
|---|---|---|
| **1** | SYN | Client requests a TCP connection |
| **2** | SYN + ACK | Server acknowledges and responds |
| **3** | ACK | Client acknowledges the server |

### 🔧 Troubleshooting insight

A SYN without an expected SYN-ACK can indicate several possibilities, including:

- Destination is unreachable
- Firewall filtering
- Routing problem
- Server is unavailable
- Packet loss
- The service is not listening

Do **not** automatically interpret a missing SYN-ACK as proof that a server "refused" the connection. Packet captures provide evidence that must be interpreted in context.

---

## 🌐 4. What Is DNS?

**Domain Name System (DNS)** translates names such as:

```text
example.com
```

into addresses such as:

```text
93.184.216.34
```

A simplified lookup looks like:

```text
Your Computer
     │
     │ DNS Query:
     │ "What is the A record for example.com?"
     ▼
DNS Resolver
     │
     │ DNS Response:
     │ "93.184.216.34"
     ▼
Your Computer
```

### 🗂️ Common DNS record types

| Record | Purpose |
|---|---|
| **A** | IPv4 address |
| **AAAA** | IPv6 address |
| **MX** | Mail server |
| **CNAME** | Alias to another domain name |
| **TXT** | Text-based metadata, often used for verification/security |

---

## 🔓🔒 5. HTTP vs HTTPS

### 🔓 HTTP

HTTP traffic is not protected by TLS.

If sensitive application data is transmitted over HTTP, the contents may be visible to someone who can legitimately observe that traffic.

### 🔒 HTTPS

HTTPS uses HTTP over TLS.

```text
HTTP
  +
TLS encryption
  =
HTTPS
```

With HTTPS, a packet capture can still reveal useful metadata such as IP addresses, ports, packet timing, and connection behavior, but the HTTP application payload is generally encrypted.

> [!WARNING]
> Never capture or inspect credentials belonging to another person. Use only systems, accounts, and test data you own or have explicit authorization to analyze.

---

## 👀 6. What Is Promiscuous Mode?

A network interface normally processes traffic relevant to its own host and the network environment.

**Promiscuous mode** allows the interface to pass additional frames to the capture software when the network interface and environment support it.

However:

> [!IMPORTANT]
> **Promiscuous mode does not magically allow you to see every device's traffic on a modern switched network.**

To capture traffic between other hosts, you may need a dedicated capture point, such as:

- SPAN / port mirroring
- Network TAP
- Virtual switch capture
- Cloud network monitoring tools

---

# 🎯 What You Will Learn

| Skill | Real-World Application |
|---|---|
| **Capture live traffic** | Foundation of packet analysis |
| **Apply display filters** | Quickly isolate relevant packets in large captures |
| **Read TCP handshakes** | Diagnose connection establishment problems |
| **Analyze DNS** | Troubleshoot name resolution and investigate suspicious lookups |
| **Identify HTTP traffic** | Understand the security implications of unencrypted protocols |
| **Follow TCP streams** | Reconstruct application conversations |
| **Save PCAPNG evidence** | Preserve captures for later analysis |
| **Use TShark** | Perform command-line packet capture on servers and remote systems |

---

# 🛠️ Step 1 — Install Wireshark

Wireshark is free and open source.

Download it from the official Wireshark website:

**https://www.wireshark.org/download.html**

| OS | Installation | Notes |
|---|---|---|
| 🪟 **Windows** | Windows x64 installer | Install Npcap when prompted |
| 🍎 **macOS** | Intel or Apple Silicon installer | Follow the installer instructions and allow capture permissions when prompted |
| 🐧 **Linux** | Package manager | Ubuntu/Debian example shown below |

### 🐧 Ubuntu / Debian

```bash
sudo apt update
sudo apt install wireshark
```

If your distribution uses the `wireshark` group:

```bash
sudo usermod -aG wireshark $USER
```

Log out and back in for the group membership change to take effect.

### ✅ Verify the installation

```bash
wireshark --version
```

You should see the installed Wireshark version.

---

# 🧪 Step 2 — Your First Capture

This first capture is intentionally simple.

### 📝 Procedure

1. Open **Wireshark**.
2. Locate your active network interface.
3. Look for the interface showing live packet activity.
4. Double-click the active **Wi-Fi** or **Ethernet** interface.
5. Wireshark begins capturing immediately.
6. Open a web browser.
7. Visit a website.
8. Generate some normal network activity.
9. Capture for approximately **30 seconds**.
10. Click the **red Stop button**.

### 💡 What just happened?

You created a packet capture in memory.

A short browsing session can generate hundreds or thousands of packets.

That's why analysts use filters.

---

# 🔎 Step 3 — Essential Display Filters

The filter bar is one of the most important parts of Wireshark.

Enter a filter and press **Enter**.

## 🔀 Display Filters vs Capture Filters

### 🎣 Capture filter

Controls what gets captured **before or during capture**.

### 🔍 Display filter

Controls what you see **after packets have already been captured**.

For this lab, focus on **display filters**.

The advantage:

```text
One Capture
    │
    ├── DNS filter
    ├── TCP filter
    ├── HTTP filter
    ├── IP filter
    └── ICMP filter
```

You can analyze the same evidence from multiple perspectives without capturing everything again.

---

## 🔑 Essential Filter Reference

| Filter | What It Shows | Common Use |
|---|---|---|
| `dns` | DNS traffic | Name-resolution troubleshooting |
| `http` | HTTP traffic | Inspecting unencrypted web traffic |
| `tcp` | TCP traffic | Connection analysis |
| `tcp.flags.syn == 1` | TCP SYN packets | Connection attempts |
| `tcp.flags.reset == 1` | TCP RST packets | Reset connections |
| `icmp` | ICMP traffic | Ping/reachability analysis |
| `ip.addr == 192.168.1.1` | Traffic to/from an IP | Isolate one host |
| `ip.src == 10.0.0.5` | Traffic from a source IP | Analyze outbound traffic |
| `tcp.port == 443` | TCP traffic using port 443 | Identify common HTTPS connections |
| `http.request` | HTTP requests | Analyze web requests |
| `http.request.method == "POST"` | HTTP POST requests | Inspect HTTP form submissions |

> [!TIP]
> Replace example IP addresses with addresses that actually appear in your capture.

---

# 🧪 Step 4 — Guided Exercises

Work through the exercises in order.

Each exercise builds a specific packet-analysis skill.

---

# 🅰️ Exercise A — Capture a DNS Lookup

## 🎯 Objective

Capture a DNS request and identify its corresponding response.

---

## 🛠️ What Is `nslookup`?

`nslookup` is a command-line utility that performs DNS queries.

You run it from your **operating system's terminal**, not inside Wireshark.

The workflow is:

```text
Wireshark
   │
   │ Start capture
   ▼
Terminal
   │
   │ nslookup example.com
   ▼
DNS traffic generated
   │
   ▼
Wireshark captures packets
   │
   ▼
Apply dns filter
```

### 💻 Open a terminal

**Windows**

```text
Windows Key → type "cmd" → Enter
```

**macOS**

```text
Cmd + Space → type "Terminal" → Enter
```

**Linux**

```text
Ctrl + Alt + T
```

---

## 📍 DNS A Record

An **A record** maps a hostname to an IPv4 address.

Example:

```text
example.com
     ↓
A record
     ↓
93.184.216.34
```

---

## 📝 Procedure

### 1. Start a Wireshark capture

Select your active network interface.

### 2. Open a separate terminal

Run:

```bash
nslookup google.com
```

### 3. Observe the result

Your terminal should return one or more IP addresses.

### 4. Stop the Wireshark capture

Click the red **Stop** button.

### 5. Apply the DNS display filter

```text
dns
```

### 6. Locate the query

Look for a packet similar to:

```text
Standard query A google.com
```

### 7. Locate the response

Look for:

```text
Standard query response A google.com
```

### 8. Inspect the DNS response

Select the response packet.

In the packet details pane:

```text
Domain Name System
       ↓
Answers
       ↓
A record
       ↓
IPv4 address
```

Compare the returned address with the result from `nslookup`.

---

## ✅ Exercise A Checkpoint

You should be able to explain:

- What DNS does
- What an A record represents
- Which packet was the DNS query
- Which packet was the response
- Which IP address was returned
- How the DNS transaction connects to the eventual network connection

### 🔥 Real-World Connection

Unexpected DNS queries can be useful during security investigations.

For example, an analyst may investigate:

```text
Host
  ↓
Unexpected DNS query
  ↓
Unusual domain
  ↓
Suspicious IP
  ↓
Additional investigation
```

A suspicious DNS lookup is **an investigative clue, not automatically proof of malicious activity**.

---

# 🅱️ Exercise B — Watch the TCP Three-Way Handshake

## 🎯 Objective

Identify:

```text
SYN → SYN-ACK → ACK
```

---

## 📝 Procedure

### 1. Start a new capture

### 2. Generate HTTP traffic

Open:

```text
http://example.com
```

> [!NOTE]
> The site may redirect or behave differently depending on your browser/network. The goal is to generate TCP traffic, not to rely on a specific web response.

### 3. Stop the capture

### 4. Resolve the destination

In your terminal:

```bash
nslookup example.com
```

Record one of the returned IPv4 addresses.

### 5. Filter the traffic

Replace `<IP>` with the address you identified:

```text
tcp and ip.addr == <IP>
```

### 6. Find the handshake

Look for:

```text
SYN
SYN, ACK
ACK
```

---

## 🔬 TCP Handshake Analysis

| Packet | Flags | Meaning |
|---|---|---|
| **1** | SYN | Client requests a TCP connection |
| **2** | SYN + ACK | Server acknowledges and responds |
| **3** | ACK | Client acknowledges |

### 🖼️ Visual Model

```text
CLIENT                                  SERVER

   │
   │──────────── SYN ─────────────────►│
   │                                    │
   │◄────────── SYN + ACK ──────────────│
   │                                    │
   │──────────── ACK ─────────────────►│
   │                                    │
   │        CONNECTION ESTABLISHED      │
   │                                    │
```

---

## 🔍 What If Something Looks Wrong?

### ⏳ SYN with no SYN-ACK

Possible explanations include:

- Firewall filtering
- Routing problem
- Destination unavailable
- Service unavailable
- Packet loss
- Capture point limitations

### 🛑 RST

A TCP reset indicates that a connection was reset.

Investigate the surrounding packets and application context before deciding why.

---

# 🔓 Exercise C — Demonstrate Cleartext HTTP

## 🎯 Objective

Understand why transmitting sensitive information over unencrypted HTTP is dangerous.

> [!CAUTION]
> **AUTHORIZED LAB ONLY** — Use only systems, accounts, credentials, and test data that you own or are explicitly authorized to analyze. Never intercept another person's credentials or traffic.

---

## 🧰 Recommended Lab Setup

Use a deliberately controlled local HTTP test environment.

Example architecture:

```text
Browser
   │
   │ HTTP
   ▼
Local Test Web Server
   │
   ▼
Wireshark
```

Use a **fake username and fake password**.

Example:

```text
Username: testuser
Password: TestPassword123!
```

Do not use a real account password.

---

## 📝 Procedure

1. Start your authorized local HTTP test environment.
2. Start a Wireshark capture.
3. Submit the test login form.
4. Stop the capture.
5. Apply:

```text
http.request.method == "POST"
```

6. Locate the POST request.
7. Expand the HTTP request details.
8. Look for the submitted form data.

If the application actually transmits the form fields in plaintext HTTP, the data may be visible in the packet capture.

---

## 🔐 Why This Matters

Without TLS protection, sensitive application data can potentially be exposed to someone who is able to observe the traffic path.

The security principle is:

```text
Sensitive Data
      ↓
   HTTP
      ↓
No TLS protection
      ↓
Potential exposure
```

Compared with:

```text
Sensitive Data
      ↓
   HTTPS
      ↓
TLS encryption
      ↓
Protected application payload
```

This is one reason HTTPS is fundamental to modern web security.

---

# 🔗 Exercise D — Follow a Full TCP Stream

## 🎯 Objective

Reconstruct a complete TCP conversation from individual packets.

---

## 📝 Procedure

### 1. Capture HTTP traffic

Generate authorized HTTP traffic in your test environment.

### 2. Find an HTTP packet

Use:

```text
http
```

### 3. Select an HTTP packet

Right-click the packet.

Choose:

```text
Follow
    ↓
TCP Stream
```

### 4. Analyze the conversation

Wireshark reconstructs the TCP stream into a readable conversation.

Conceptually:

```text
Individual Packets
       │
       ▼
TCP Sequence Numbers
       │
       ▼
Reassembled Stream
       │
       ▼
Application Conversation
```

### 💭 Why this matters

Individual packets are pieces of a conversation.

Following a stream can help an analyst understand:

- What the client requested
- What the server returned
- Which resources were requested
- What data was transferred
- How the application communicated

> [!NOTE]
> For encrypted protocols such as HTTPS, the reconstructed stream will generally contain encrypted application data rather than readable HTTP content.

---

# 💾 Step 5 — Save and Export Captures

Captures are useful evidence and excellent portfolio artifacts.

## 💾 Save a Capture

In Wireshark:

```text
File
  ↓
Save As
  ↓
capture.pcapng
```

Use the `.pcapng` format when possible.

---

## 📤 Export Filtered Packets

First apply a display filter.

Example:

```text
dns
```

Then:

```text
File
  ↓
Export Specified Packets
  ↓
Displayed
```

This lets you create a smaller evidence file containing only the packets currently displayed.

---

## 📂 Reopen a Capture

```text
File
  ↓
Open
  ↓
capture.pcapng
```

Confirm that the packets load successfully.

---

# 🖥️ Bonus — Command-Line Capture with TShark

Wireshark includes **TShark**, the command-line version of Wireshark's packet-analysis engine.

This is especially useful on:

- Linux servers
- Azure VMs
- Remote systems
- Headless environments
- Automation workflows

### 🧪 Example

```bash
tshark -i eth0 -w capture.pcapng -c 1000
```

### ⚙️ Parameters

| Option | Meaning |
|---|---|
| `-i eth0` | Capture on interface `eth0` |
| `-w capture.pcapng` | Write packets to a capture file |
| `-c 1000` | Stop after 1,000 packets |

> [!NOTE]
> Interface names vary by operating system. Run `tshark -D` to list available capture interfaces.

---

# 🧠 Verification — Prove You Can Do It

Do not consider the lab complete until you can perform these tasks without following the instructions.

| Skill | Verification |
|---|---|
| **DNS capture** | Apply `dns`, identify the query and response, and explain the A record |
| **TCP handshake** | Identify SYN → SYN-ACK → ACK and explain each packet |
| **Display filters** | Filter by protocol, IP address, and port without looking up the syntax |
| **TCP stream reconstruction** | Follow a TCP stream and explain what the conversation represents |
| **Capture management** | Save a `.pcapng`, close Wireshark, reopen it, and verify the packets |
| **TShark** | Identify your capture interface and perform a basic command-line capture |

---

# 📝 Lab Evidence Checklist

Capture screenshots or save artifacts showing:

- [ ] Wireshark interface with a live capture
- [ ] DNS query and response
- [ ] DNS A record
- [ ] TCP SYN
- [ ] TCP SYN-ACK
- [ ] TCP ACK
- [ ] HTTP request
- [ ] Authorized test HTTP POST, if performed
- [ ] TCP Stream reconstruction
- [ ] Saved `.pcapng` capture
- [ ] TShark capture, optional

---

# 🧩 Troubleshooting

## 🚫 No packets appear

Check:

- You selected the active interface.
- The interface is actually connected.
- You have appropriate permissions.
- Another VPN or virtual adapter is not the interface carrying the traffic.

Try:

```text
Capture → Refresh Interfaces
```

---

## 🕳️ `dns` returns nothing

Try generating a fresh lookup:

```bash
nslookup example.com
```

Then remove and reapply the display filter.

Also remember that DNS may be handled through:

- Local cache
- DNS-over-HTTPS
- DNS-over-TLS
- A local resolver

These can change what traditional DNS packets look like in a capture.

---

## 📭 `http` returns nothing

Modern websites overwhelmingly use HTTPS.

Use an **authorized local HTTP test environment** rather than assuming a public website will provide HTTP traffic.

---

## 🙈 You cannot see another device's traffic

This is expected on many switched networks.

Your capture point may only provide visibility into:

- Your own traffic
- Broadcast traffic
- Multicast traffic
- Traffic specifically mirrored to the interface

For enterprise investigations, the capture architecture matters.

---

# ☁️ Cloud Engineering Connection

The packet-analysis skills from this lab transfer directly into cloud networking.

### 🏢 On-premises

```text
Wireshark
    ↓
Packets
    ↓
TCP / DNS / HTTP
    ↓
Network troubleshooting
```

### ☁️ Azure

```text
Azure Network Watcher
    ↓
Connection Monitor / NSG Flow Logs / Packet Capture
    ↓
Network evidence
    ↓
Connectivity + security analysis
```

The tools differ, but the underlying mental model remains:

> **Who communicated with whom, over what protocol, on what port, and what happened during the connection?**

---

# 🚀 Portfolio Challenge

After completing the guided exercises, perform one investigation without following the instructions.

## 🎬 Scenario

> A user reports that an application is unreachable.

Your job is to determine whether the evidence points toward:

- DNS
- TCP connectivity
- Application-layer behavior
- A reset connection
- An unreachable destination
- A client-side issue

### 🧭 Investigation Workflow

```text
1. Capture
   ↓
2. Identify the affected host
   ↓
3. Check DNS
   ↓
4. Check TCP connection attempts
   ↓
5. Inspect SYN / SYN-ACK / ACK
   ↓
6. Look for RST packets
   ↓
7. Inspect application traffic
   ↓
8. Follow relevant streams
   ↓
9. Document findings
```

---

# 📊 Analyst Report Template

Use this template after your investigation.

```text
# Network Investigation Report

## Date
YYYY-MM-DD

## Analyst
Adam Austin

## Problem Statement
Describe the reported network issue.

## Source Host
IP:

## Destination Host
IP:

## Destination Port
Port:

## Protocol
TCP / UDP / ICMP / Other:

## DNS Findings
What DNS queries and responses were observed?

## TCP Findings
Was a TCP handshake completed?

SYN:
SYN-ACK:
ACK:

## Reset / Error Findings
Were RST packets or other errors observed?

## Application Findings
What application-layer traffic was observed?

## Stream Findings
What did the reconstructed conversation show?

## Conclusion
Summarize the evidence without assuming more than the capture proves.

## Recommended Next Investigation
What should be checked next?
```

---

# 🏁 Lab Completion Standard

You have completed **Lab 2 — Wireshark & Network Analysis** when you can independently:

1. Capture network traffic.
2. Identify your active interface.
3. Apply Wireshark display filters.
4. Find DNS queries and responses.
5. Explain DNS A records.
6. Identify a TCP three-way handshake.
7. Recognize TCP resets.
8. Explain the security difference between HTTP and HTTPS.
9. Follow and interpret a TCP stream.
10. Save and reopen a `.pcapng` capture.
11. Perform a basic TShark capture.
12. Write a short evidence-based network investigation.

---

# 🎓 Skills Demonstrated

**🌐 Networking**

- Packet analysis
- TCP/IP
- DNS
- TCP
- HTTP/HTTPS
- ICMP
- Ports and protocols
- Network troubleshooting

**🛡️ Security**

- Traffic analysis
- Cleartext credential risk
- Packet-based investigation
- Evidence preservation
- Incident-response fundamentals

**☁️ Cloud**

- Network troubleshooting mindset
- Azure network monitoring concepts
- Flow-log interpretation
- Packet-capture fundamentals
- Infrastructure connectivity analysis

**🧰 Tools**

- Wireshark
- TShark
- `nslookup`
- Terminal / Command Prompt

---

## 🏆 Portfolio Outcome

A completed version of this lab gives you more than a Wireshark screenshot.

It demonstrates that you can:

> **Generate network traffic → capture evidence → filter packets → analyze protocols → reconstruct conversations → identify anomalies → document technical findings.**

That is the core workflow behind practical network troubleshooting and packet-based security analysis.

---

<div align="center">

**👤 Author:** Adam Austin · **📚 Series:** Cloud / Network / Security Engineering Portfolio · **🧪 Lab:** 2 — Wireshark & Network Analysis

⭐ *If this lab helped you, give the repo a star!* ⭐

</div>
