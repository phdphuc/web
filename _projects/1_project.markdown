---
layout: page
title: Mac-A-Mal
description: Mac-A-Mal ultimate hunting of macOS malware
img: /assets/img/12.jpg
importance: 1
---

## Abstract

With macOS increasing popularity, the number, and variety of macOS malware are rising as well. Yet, very few tools exist for dynamic analysis of macOS malware. In this paper, we propose a macOS malware analysis framework called Mac-A-Mal. We develop a kernel extension to monitor malware behavior and mitigate several anti-evasion techniques used in the wild. Our framework exploits the macOS features of XPC service invocation that typically escape traditional mechanisms for detection of children processes. Performance benchmarks show that our system is comparable with professional tools and able to withstand VM detection. By using Mac-A-Mal, we discovered 71 unknown adware samples (8 of them using valid distribution certificates), 2 keyloggers, and 1 previously unseen trojan involved in the APT32 OceanLotus.

## Introduction

Contrary to popular belief, the Mac ecosystem is not unaffected by malware. In 2014, the first known ransomware appeared, and other ransomware has been discovered as Software-as-a-Service (SaSS), where malware is available as requests. Mac devices saw more malware attacks in 2015 than the past five years combined, according to a cyber-security report from the Bit9 and Carbon Black Threat Research team.Footnote1 In 2016, Mac malware grew 744% with around 460,000 instances detected, says McAfee report and increases 270% between 2016 and 2017.

## Projects code

Details could be found here: https://github.com/phdphuc/mac-a-mal