---
layout: single
title: Talk's abstracts
excerpt: "Abstracts for Blackhoodie 2018"
modified: 2018-11-15T00:44:38.564948-04:00
permalink: /schedule/abstracts
header:
    image: /assets/images/banner_1.png
---

# Bushwhacking your way around a bootloader

<a href="../assets/archive/bx_blackhoodie.pdf"><i class="fa fa-file"></i> slides</a>

This talk will cover the methods and tools I developed to "reverse engineer" the open source Das U-Boot bootloader, and how they can be applied more generally to as a dynamic analysis technique to correlate interesting events with control flow and ultimately a location in the binary/source code.

Even when you have access to some binary's source code, it can still be challenging to understand said software. In this talk, I will discuss the techniques and I tools I developed in order to understand and navigate the pile of code that is the open-source Das U-Boot bootloader. The tools I developed do not rely on proprietary software and instead make use of free and powerful debugging tools such as Capstone, Unicorn, and the GDB Python plugin API. My approach strives to highlight the temporal and mechanical connections that exist between higher-level behaviors and regions of the code base/binary by instrumenting, tracing, and analyzing all memory writes with respect to the software's current execution path. This technique allows us to develop and test our understanding of the relationships between code and objects (data structures and/or regions of memory). I will demonstrate how these tools and techniques can be used in practice by discussing how they were used to identify and distinguish between different phases of U-Boot execution (including distinct phases of initialization and relocation). This talk aims to be both accessible to software folk who merely want to learn more about bootloaders as well as interesting to those with bootloader and/or reverse-engineering experience.

# Down the Drain: A look into Pinball's Embedded systems

<a href="../assets/archive/Down_the_Drain_blackhoodie.pdf"><i class="fa fa-file"></i> slides</a>

I've been digging to into the pinball update file for fun. I had to create a little python tool to unpack the file. Then used strings to find out about the processor type and OS and find the files that were most interesting. I'm now learning radare2 to try and reverse through the binary a bit. This is not a finished project and I don't know where it will lead me but thought I should stick out my neck and try something scary.

# Malware dissection 101 : TinyNuke

What is a banking trojan ? How does it work ? Let's dissect a TinyNuke sample to find it out.

Last year, a massive malware campaign have been spotted targeting French and American banks.
This banking trojan called TinyNuke or NukeBot targets American and French bank customers in order to steal credentials by keylogging. The presentation will cover a quick analysis of TinyNuke to understand how it works. So we will talk about basic malware analysis method including lab setup, macro word deobfuscation, basic anti-debugg bypass, dynamic analysis and finding IoCs. The goal of this presentation is to show that nowadays malwares are not always as tricky as we might think. Some are still basic and easy to dissect for beginners.

# Linux servers under siege: a real case forensic analysis of a cryptocurrency miner attack

<a href="../assets/archive/Linux_Servers_Under_Siege_blackhoodie.pdf"><i class="fa fa-file"></i> slides</a>

Everything started with an email alert to a network administrator hinting that something was not right in one of the organization's Linux servers. What followed is two months of digital forensic analysis where we followed the trail of breadcrumbs left behind by the attackers, only to find that what we found was just the last of a series of attacks that have started almost a year before.

# Android_Emuroot: Abusing Google Play emulator debugging to RE non-cooperative apps as root

<a href="../assets/archive/emuroot_blcakhoodie.pdf"><i class="fa fa-file"></i> slides</a>

Rooting detection mechanisms implemented by Android applications can be a pitfall for reverse-engineers who want to study these applications. This presentation aims at sharing an interesting way based on Google Play emulator debugging to get a rooted shell not trivially detectable by the applications allowing to go further in reverse-engineering (RE).

# Arming malware with GANs

<a href="../assets/archive/Arming_Malware_with_GANs_blackhoodie.pdf"><i class="fa fa-file"></i> slides</a>

This talk is about an application of Generative Adversarial Networks (GANs) in network security. More specifically, it is about our experiments in using GANs to modify malware C&C traffic so that it mimics normal network traffic in order for the malware to remain undetected while at the same time it continues to be effective.

# "May I see your credentials, please?"

Credential theft is an important part of the attacker playbook when attempting lateral movement. This process mostly involves dumping credentials saved locally on the machine. In many cases these passwords can be retrieve from the Windows Credential Manager, allowing attackers an easy path into the organization. This was evident in major attacks such as the NotPetya ransomware, and high-profile tools like Mimikatz.
In this talk, we explain how to detect credential theft out of the Windows Credential Manager using Windows Defender Advanced Threat Protection (WDATP). This involves modifying the Windows operating system to send telemetry to the WDATP cloud which was extended with new detection rules.

# JaSt: Fully Syntactic Detection of Malicious (Obfuscated) JavaScript

<a href="../assets/archive/JaSt_blackhoodie.pdf"><i class="fa fa-file"></i> slides</a>

In this talk, we present JaSt, a low-overhead solution that combines the extraction of features from the abstract syntax tree with a random forest classifier to detect malicious JavaScript instances. Even though the analysis is entirely static, it yields a high detection accuracy of almost 99.5\% and has a low false-negative rate of 0.54%.

# Enter The Matrix (Ransomware)

<a href="../assets/archive/Matrix_Ransomware_blackhoodie.pdf"><i class="fa fa-file"></i> slides</a>

The Matrix ransomware family introduced some new techniques for obfuscating internal structures and embedded data. In this presentation, I'll walk attendees though my investigation of the Matrix ransomware family and a reverse engineering of its main components.

# Fun With C++

C++ has a lot of features. Sometimes what you think you are doing and what is actually happening are two different things. We are going to look at some C++ anti-patterns and how you can abuse them.

# Linux Security APIs and the Chromium Sandbox

<a href="../assets/archive/Chromium_Sandbox_on_Linux_blackhoodie.pdf"><i class="fa fa-file"></i> slides</a>

The Linux Security and Isolation APIs have become the basis of some of the most useful features server-side, providing the isolation required for efficient containers. However, these APIs also form the basis of the Chromium Sandbox on Linux, and we will study them in that context in this talk.

# Hidden in plain sight

<a href="../assets/archive/hidden_in_plain_sight_blackhoodie.pdf"><i class="fa fa-file"></i> slides</a>

This talk will take a look at some living-off-the-land techniques and fileless persistence methods in Windows. Tools that are already installed on a system provide a good opportunity to hide in plain sight and Windows gives us various options to 'legitimately' play around.
The overall goal is to point to interesting fields worth taking a closer look at later on.
