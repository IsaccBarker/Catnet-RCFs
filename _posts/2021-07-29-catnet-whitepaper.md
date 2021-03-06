---
layout: post
title: Catnet Whitepaper
date: 2021-07-29 13:50 -0600
---

#  Introduction

## Abstract
This is the specification for Catnet,  an open-source project, with the primary implementation written by Milo Banks and Eli Hatton at ZincSoft (available here).
Catnet is similar to Tor, otherwise known as The Onion Router, with several key differences. Firstly, Catnet provides users with open source and decentralized web browsing and internet usage, without the worry of dangerous threats. Here, we explain the critical differences between Catnet and Tor.

Every user of Catnet has an “account,” consisting of login credentials (these can be passwords, keys, auth tokens, etc...). This “account” is not tied to you, your computer, your location, or any related personal information that can be used for tracking purposes, allowing for almost full anonymity. To put this in other words, a website owner can not tell that you are accessing their website; however, they can tell that someone is accessing their website without ever knowing their identity. The reason an account is required is to allow operators of a cat (explained later) to block persons with malicious intent, without actually knowing who the person is.

Another key difference is the concept of cats. A cat is, in essence, a group of machines, computers, and servers (these machines, computers, or servers individually are called whiskers) that relay information and create the same effect as Tor. A cat is like an anonymous system, except it is meant to be deployed on top of an existing network. A Catnet user (using a Catnet client driven by a driver) can then “log into” a cat with their “account,” at which point they will be let in and allowed access, or disallowed, at which point they can contact an admin or operator.

The Catnet specification uses a unique blend of a written specification and code specification. Everything except code will be found in this document. Please note that this means no example .proto files will be provided. You can find these in the official reference implementation code repository.

## Unique Terminology
CN: Abbreviation for Catnet.
Cat: A group of machines that exchange packets. A cat is like an anonymous system, except it is meant to be deployed on top of an existing network. 
Head: The server keeping track of active users, account details, etc… Designed to be lightweight enough to run on a single Raspberry Pi efficiently, making Catnet effectively deployable anywhere at any time. A head is what is created when CN Registrar Mode is activated.
Whisker: A machine logged into a cat that can take advantage of the benefits of Catnet. Can send/receive packets to be rerouted, send/receive packets to/from the outside world. A head is what is created when CN Participant Mode is activated.
CN Driver: A piece of software that can interact with (drive) libcatnet.
CN Client: A piece of software used for interacting and giving commands to the CN Daemon.
CN Daemon: A piece of software that has two modes: participant and registrar.
CN Participant Mode (Whisker): A mode of the CN Daemon that captures outgoing local packets, and sends them to another whisker to be rerouted. It also controls what to do with packets it has received (that should be rerouted). 
CN Registrar Mode (Head): A mode of the CN Daemon that keeps track of all computers on the cat, account details, etc...

## A brief explanation of Tor
As previously mentioned, Catnet is primarily inspired by Tor, or The Onion Router as it is sometimes called. If you want to know more about Tor after reading this section, you can find their website here and their specification here.
Tor is based on military technology pioneered in the late 1990s by U.S. Naval Research Laboratory employees Paul Syverson, Michael G. Reed, and David Goldschlag to protect U.S. intelligence communications online.

Tor works by sending your internet traffic through three “nodes.” The first node is a guard node, the second is a regular node, and the third and final node is also a guard node. The guard nodes mask the identity of the regular node, and you, the sender. Each node is locked into its own TLS encryption session, which it uses to provide additional security.
Catnet is based on the same principles, but with more techniques that modern-day computers and internet access can allow, such as cats.

## How Catnet users are protected from threats
Heads (see section 1.1) can disallow users from accessing specific websites. This is because, while anonymous, it is still known when a website is being accessed. Heads can block traffic to any website that the administrators deem necessary to be blocked, and heads can also whitelist or blacklist certain accounts based on their individual needs, such as not allowing Youtube to keep students on task or banning certain users for unsavoury or malicious behaviour (deemed this by the admin(s)). 

## How we solve the exit node problem.
The exit node problem in an infamous “break in security” for systems cenetered around a peer to peer idealism. Catnet solves this issue, while simultaneously making the connection between 0 and 200% faster than TOR and other leading solutions. How do we achieve this?

Every packet that gets injected into a cat (this mechanism is loving called the syringe) is broken up into multiple fragments. Each of these fragments are sent along various routes. These routes are decided via a weighted routing algorithm utilising Monte-Carlo techniques. Example:

Lets say Dylan sends a packet. Based on the number of people on the cat, we may decide to break dylans packet up into 14 pieces. Each of these packets are then sent to other participants via different routes. This happens concurrently, which means we can speed up the process of networking probably exponentially, which is a huge benefit over TOR.

Since each “exit node” gets a very small, fragment of the data, this means that most of the fragments will be encrypted (assuming HTTPS). The exit nodes that get the headers of the traffic present a bigger problem. If we give each exit node that gets a part of the header only a part of the header element (e.g. only getting the first four bytes of a IPv4 address), then it becomes nearly impossible to trace back the originator (the first sender of the packets) with 100% accuracy. You can guess, but chances are it will not be correct.

## Cats vs Whiskers vs Heads
A cat (synonyms to a type of network, discussed in section 1.1) is a group of multiple whiskers (discussed in brief in section 1.1) controlled by a single head.

### Whiskers
A whisker is a user-facing (meaning controlled by an individual user) installation of the Catnet daemon. Whiskers are where web traffic and packets originate from before being routed through other whiskers in their respective cats. More information on how these processes work is available in the technical section 4.

### Heads
A head is an outward-facing computer (most-likely on the DMZ, although not necessary) that enables and facilitates the communication between whiskers inside the cat and external computers. The head computer keeps track of active connections, stores account information, manages load, and more. More information on how these processes work is available in the technical section 3.

# Head to Whisker Intercommunication

## Abstract
This section describes the relationship between (and the communication between) a whisker and a head. The communication between these parts is achieved with a held TCP socket connection that contains serialized data in the format of Google Protocol Buffers (ProtoBuf3). In Catnet-rs, communication consists on Bincode. The connection between a head and a whisker should occur on port 44400. Every communication of this type should occur over TCP. Heads can be hosted on local networks or worldwide, so head to whisker communication will use both public and private networks.

# Whisker to Whisker Intercommunication

## Abstract
This segment covers the intercommunication of Whiskers. It mainly covers the CATNIP protocol, which is a Packet Encapsulation protocol designed to send network packets efficiently over UDP. The connection between a whisker and a whisker should occur on port 44500 Heads can be hosted on local networks or worldwide, so head to whisker communication will use both public and private networks.

## CATNIP (Catnet Applied Transport of Network Intercommunicational Packets)
While other packet encapsulation protocols use TCP, CATNIP uses UDP. This is because UDP is many times faster. We chose not to use TCP because it is slow. 

### TCP over UDP over CATNIP
CATNIP assumes that encryption is already performed.
