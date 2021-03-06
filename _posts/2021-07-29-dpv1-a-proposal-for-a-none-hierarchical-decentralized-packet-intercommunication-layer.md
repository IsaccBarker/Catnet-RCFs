---
layout: post
title: 'DPv1: A Proposal for a None-Hierarchical Decentralized Packet Intercommunication Layer'
date: 2021-07-29 12:18 -0600
---


# Overview.
Why does the Internet need fixing?
The internet hasn’t been around that long in the grand scheme of things, and yet it already needs replacing. This is not due to bad design, far from it. The internet is a technological wonder that was revolutionized global communications. However, it was designed in an era with limited computing. The global rise of hugely complicated machines, at a competitive price, was not predicted in the slightest. In other words, the infrastructure is ageing, and the attacks are getting smarter. With all the attacks we are seeing, it is almost impossible to not think that we need a replacement. Hence, Catnet is born.

## The name
The name Catnet is derived from Catenet, which is what the internet was almost named. The cate was changed to cat which leads to a lot of puns and acronyms.

## Core technologies
In order for Catnet to become a reality, almost all of the technology underlying the internet need to be replaced. While the original internet was based highly on the concept of hierarchies, this restricts the concept of an open internet. While it does provide control, it concentrates said control in the hands of very few people. If these people were to go rogue, or a coup occurred, the consequences wouldn’t be catastrophic; they would be cataclysmic.

Decentralization and blockchain are key theories that are critical to allowing Catnet to function.
Decentralization is needed to ensure that no one party has control over the entire system. This no managing body or corporation, copy, or any other entity can control any significant part of the system.
Blockchain also keeps a record of all major changes to the network (e.g. domain management events, new heads, etc...)

## Problems (and their solutions)
### Solving network inefficiencies during low network saturation

Unlike other decentralized internet implementations, Catnet does not require specialized hardware, nor does it require much hardware at all. It does not require Bluetooth, which was previously necessary for data transmission. This is a stupidly bad idea. Not only is it insecure, it is also very, very, slow.

It also means that is you don’t have enough people on the network (or in a localized place), then network saturation is below a certain threshold. This means no one gets any internet, and that’s very bad.

Catnet still uses underlying communication infrastructure means, no matter how few people use it (assume it’s above 10 people), it can still be secure.

### The head net mesh discovery problem

In order for many protocols to work (especially SDSP, the simple data synchronization protocol) each head has to know where to locate and how to talk to every other head in existence. While it is not required for every head to be connected (e.g. there can be two Catnets), it would break the synchronization of data, thus meaning some areas wouldn’t have access to the correct DNS records or servers.

To solve this problem, Catnet participants, every time they connect to a head, give up a list of every head they have connected head. This raises a privacy concern, as you can, with a fair amount of certainty, link an online account to a person (so long as you know the whereabouts of the person). We solve this smaller problem by using the CP protocols direct mode. See the write-up on different CP modes later in this paper.

## The location history give up problem
Stuff

# Overview of the Catnet Protocol Suite Version One (CPv1)

## Application
No application layer protocols are defined for this version. This is because we just need PTP and CP to be able to get a platform that is able to convey information.

## Transport
### ATP/EDP (equiv. TCP/UDP)
The Advanced Transmission Protocol (ATP) and the Elementary Datagram Protocol (EDP) are the replacements for the Transmission Control Protocol (TCP) and the User Datagram Protocol (UDP). 
Unlike TCP, ATP sends packets much like UDP does, but still implements the verification and guarantees that packets arrive on time and in the right order. Each packet gets a reassembly number, and in the header is a hash that encompasses both the header and the payload. How this is accomplished is explained later.
Internet

### CP (equiv. IP)
The Catnet Protocol is the replacement for the Internet Protocol. It follows many of the principles (e.g. best-effort delivery, and being a connectionless protocol). CPv1 address consists of 248 bit, double that of IPv6, and quadruple that of IPv4. More details are explained later.

## Link
The link layer is not changed by CPv1. This is low level enough to not have to replace (we can just replace the tech on top). One of our goals with Catnet is to replace the least amount we can, so it’s easier to implement on all platforms.

## Physical
Catnet does not make changes to the physical layer. Hardware is much harder to switch out than software, and the standards are already good enough for our purpose. We could not do better, as these standards (SDH, DSL, etc...) are extremely fine-tuned for performance.

# The CP protocol
## Overview
The CP protocol is the Internet protocol that establishes Catnet. CP's goal is to deliver packets only based on CP addresses in packet headers from the source host to the target host. CP sets packet structures that encapsulate the data to be provided for this purpose. It also provides addressing methods used to mark the datagram with information about the source and destination.
Two components are in every datagram: one header and one payload. The CP header contains the CP address, the CP address and other necessary metadata for routing and delivery of the datagram. The payload is the transported information. This method of nesting the data payload in a packet with a header is called encapsulation.

### Header

