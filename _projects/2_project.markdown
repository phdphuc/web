---
layout: page
title: AHMA
description: Automated Hardware Malware Analysis – AHMA
img: /assets/img/3.jpg
importance: 2
---

The Internet of Things (IoT) will influence the majority of our daily life’s infrastructure. The IoT is still only in its early stages, but the number of internet-enabled devices is beginning to explode (likely to hit 50 billion by 2020). While efficiency and diffusion of IoT are increasing, security threats are becoming a far-reaching problem. Here we are particularly concentrating on ensuring the security of IoT nodes against malware threats, which may seriously disrupt daily life and economic activity or even reveal privacy critical data of users.
As state-of-the-art software monitoring techniques (static or dynamic) can still be circumvented by sophisticated attackers, we propose an automated hardware malware analysis (AHMA) framework that is non-intrusive and cannot easily be controlled or hidden by the malware attacker. AHMA uses side-channel information of the underlying hardware IoT device to detect if a device is infected by malware or in its typical running state. The framework includes supervised and unsupervised machine learning techniques to classify already known (mutated) malware as well as unknown malware.
As side-channel sources we will first concentrate on power consumption and/or electromagnetic emanation which is captured after a teardown of the device.
In this proposal we cover three real-world case studies:

1) Dedicated IoT devices
This first case study covers home routers and dedicated IoT devices which are designed to make our daily life easier and simpler. These devices often do not have any user interface and typically do not run standard operating systems that support the commonly used security tools (e.g. antivirus, firewall) or just do not have enough resources.
In this study we will rely on published malware samples as open-source and/or collaborate with researchers collecting IoT malware samples through honey pots. One of the most predominant Distributed Denial of Service (DDoS) IoT botnet in recent times is Mirai, which source code has been published as open- source. At its peak, Mirai infected 4000 IoT devices per hour and in the beginning of 2017 it was estimated to have more than half a million infected active IoT devices.

2) Connected cars
Early in 2018 another new variant of Mirai, called Mirai Okiru, targeted ARC-based IoT devices, which are widely used also in automotive applications. Therefore also devices inside connected cars could be enslaved to perform DDoS attacks.
Moreover, malware may directly attack the automotive system. In this context the motivation of attackers could be to breach drivers privacy, ransom, theft, sabotage, harm people and properties, disrupt transportation, and/or fun and publicity.
Any network interface, physical or wireless, could be exploited by malware to infect a vehicle. Given the large interconnectivity and multiple different architectures in this context of connected cars, additional to power consumption/ electromagnetic emanation we may observe new side-channel sources to detect malware which have not been considered and studied before.

3) Mobile phones/devices
In the last decade, Android became the most popular operating environment for smart devices, with almost 85% of the market share in the first quarter of 2017. This popularity makes it a very attractive target for malware attackers. However, its open application market and lax review mechanism have led to a rapid proliferation of Android malware as well as security threats.
In this case study, we aim at detecting recent malware samples on modern devices such as Nexus = 4 or Galaxy = S III using power consumption/ electromagnetic emanation.

Our novel framework is of high importance and impact for industries, and thus for users benefitting from increasing protection.