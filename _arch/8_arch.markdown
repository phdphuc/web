---
layout: page
title: Qakbot Evolves to OneNote Malware Distribution
description: March 23, 2023
img: /assets/img/arch/DarkPower.assets/media/image5.jpeg
importance: 88
---
Shining Light on Dark Power: Yet Another Ransomware Gang

By [Pham Duy
Phuc](https://www.trellix.com/en-us/about/newsroom/stories/contributors/pham-duy-phuc.html), [Max
Kersten](https://www.trellix.com/en-us/about/newsroom/stories/contributors/max-kersten.html) and [Tomer
Shloman](https://www.trellix.com/en-us/about/newsroom/stories/contributors/tomer-shloman.html) ·
March 23, 2023

Another day, another ransomware gang. The Dark Power ransomware gang is
new on the block, and is trying to make a name for itself. This blog
dives into the specifics of the ransomware used by the gang, as well as
some information regarding their victim naming and shaming website,
filled with non-paying victims and stolen data.

Based on our observations, there is no specific sector nor geographic
area that is targeted by the gang, as they seem rather opportunistic. 

Sample information

Filename

ef.exe

MD5

df134a54ae5dca7963e49d97dd104660

SHA-1

9bddcce91756469051f2385ef36ba8171d99686d

SHA-256

11ddebd9b22a3a21be11908feda0ea1e1aa97bc67b2dfefe766fcea467367394

File size

1323422 bytes (1.3 MB)

Compile date

2023-01-29 02:01:33

Compiler

Nim MINGW x64

Starting out as a bit of an obscure language originally, Nim is now more
prevalent with regards to malware creation. Malware creators use it
since it is easy to use and it has cross-platform capabilities.

Encryption key initialization

Upon starting, the ransomware creates a randomized 64-character long
lowercase ASCII string. This string is used later on to initialize the
encryption algorithm. An example is given in the figure below. The
randomization ensures the key is unique each time the sample is
executed, and therefore it is unique on each targeted machine, hindering
the creation of a generic decryption tool. Within the sample,
the Nimcrypto library is used to carry out cryptographic operations. The
used cryptographic algorithm is AES CRT.

![Figure 1 Encryption
key](/assets/img/arch/DarkPower.assets/media/image1.jpeg){width="6.5in"
height="0.6347222222222222in"}Figure 1 Encryption key

Binary string encryption

The strings within the ransomware are encrypted, which is likely done to
make it harder for defenders to create a generic detection rule. The
ciphertext strings are present within the binary in a base64 encoded
format. Once the encrypted string is decoded, the string is decrypted
using a fixed key, which is the SHA-256 hash of a hard-coded string. The
initialization vector (IV) is also included within the binary, but each
decryption call uses a different IV.

![Code 1 String decryption
disassembly.](/assets/img/arch/DarkPower.assets/media/image2.jpeg){width="6.5in"
height="3.5215277777777776in"}Code 1 String decryption disassembly.

The disassembled instructions above are used to decrypt a string, the
output of which is ".darkpower". The IV is loaded into "rdx", while
"rax" is used to store the base64 encoded and encrypted string. The
function call to "decrypt_AES_CTR" decrypts the given string.

To make the analysis of the malware easier, all encrypted strings can be
decrypted, as can be seen in the image below. This will allow an analyst
to more easily understand the ransomware's inner workings.

![Figure 2 Decrypted strings will be added as comments in the decompiler
view.](/assets/img/arch/DarkPower.assets/media/image3.jpeg){width="5.719444444444444in"
height="7.581944444444445in"}Figure 2 Decrypted strings will be added as
comments in the decompiler view.

Stopping services

The Dark Power ransomware targets specific services on the victim\'s
machine. It stops the following services: *veeam, memtas, sql, mssql,
backup, vss, sophos, svc\$, and mepocs*. By disabling these services,
the ransomware makes it difficult for the victim to recover their files,
as the services either free files (i.e. databases) which allows the
ransomware to encrypt them. Additionally, the [*Volume Shadow Copy
Service
(VSS)*](https://learn.microsoft.com/en-us/windows-server/storage/file-server/volume-shadow-copy-service) is
stopped, which is common for ransomware to do. VSS keeps shadow copies
of files. 

Additionally, other back-up and anti-malware services are stopped. All
in all, the goal is to increase the chance that a victim will pay the
demanded ransom. If the ransomware detects any services or processes
that match the predefined list, it will print *\"\[YES\] in killing
(service name)*\" to the console, as can be seen in the figure below.

![Figure 3 Terminating VSS
service](/assets/img/arch/DarkPower.assets/media/image4.jpeg){width="6.5in"
height="0.80625in"}Figure 3 Terminating VSS service

Process termination

Much like the stopped services, processes which often block files (such
as office related activity) are terminated. The ransomware queries the
Windows Management Instrumentation (WMI) named *"winmgmts: &
{impersonationLevel=impersonate}!.\\root\\cimv2"* with the
query *"select \* from win32_process"*. This query returns a list of all
running processes. Any matches with the predefined process names are
terminated. Within the list, entries such as *taskmgr.exe,
steam.exe,* and *firefox.exe* are present, as well as Microsoft Office
processes, such as *excel.exe, winword.exe,
powerpnt.exe,* and *visio.exe*. The ransomware also targets specific
processes related to database management, including *sql.exe,
oracle.exe,* and *dbsnmp.exe* to ensure the encryption process of all
databases. By terminating these processes, the ransomware aims to ensure
that it can complete its encryption process without encountering locked
files. The complete list of process names is given below.

taskmgr.exe

agntsvc.exe

synctime.exe

encsvc.exe

mspub.exe

infopath.exe

powerpnt.exe

onenote.exe

mydesktopservice.exe

ocssd.exe

winword.exe

firefox.exe

steam.exe

thebat.exe

oracle.exe

isqlplussvc.exe

excel.exe

sqbcoreservice.exe

outlook.exe

mydesktopqos.exe

dbeng50.exe

sql.exe

ocautoupds.exe

tbirdconfig.exe

ocomm.exe

thunderbird.exe

msaccess.exe

visio.exe

dbsnmp.exe

wordpad.exe

xfssvccon.exe

Table 1 List of process names for termination

Excluding files and folders from encryption

Once all targeted services and processes are stopped and terminated,
there is one more filter to be applied by the ransomware: the exclusion
of file names, file extensions, and folder names. In order for a victim
to see the ransom note, a working machine is required. As such, certain
files and folders, which are crucial for the system to remain
operational, are excluded. Below is the list of the excluded files,
folders, and extensions:

.lib

.theme

.dll

.bin

.ocx

.search-ms

.msi

.hta

.mod

.rom

.dat

.sys

.deskthemepack

.ics

.prf

.ini

.wpx

.nomedia

.com

.themepack

.regtrans-ms

.cpl

.msu

.hlp

.msstyles

.ps1

.adv

.rtp

.spl

.lnk

.log2

.msc

.msp

.nls

.icns

.log1

.scr

.idx

.cab

.mpa

.blf

.bat

.ani

.exe

.drv

.ldf

.key

.386

.diagpkg

.cur

.lock

.ico

.diagcfg

.icl

.diagcab

.cmd

.shs

Table 2 List of excluded extensions

readme.pdf

autorun.inf

bootfont.bin

ntldr

ntuser.dat.log

ntuser.dat

thumbs.db

boot.ini

ntuser.ini

bootsect.bak

iconcache.db

desktop.ini

Table 3 List of excluded files

program files

\$recycle.bin

tor browser

appdata

windows.old

windows

mozilla

programdata

intel

\$windows.\~ws

system volume information

perflogs

application data

program files (x86)

msocache

\$windows.\~bt

boot

google

Table 4 List of excluded folders

Clearing logs and console 

After killing services, the ransomware sleeps for 30 seconds and
executes the Windows command *\"C:\\Windows\\system32\\cmd.exe /c
cls\"* to clear the console (Code 2). In this sample, the ransomware
uses the WMI
query *"Select \* from Win32_NTEventLogFile"* and *"ClearEventLog()"* to
clear the system logs. Clearing logs is a common tactic used by
ransomware to hide their tracks and to make it difficult for analysts to
investigate the attack. Afterwards, it initiates the spreading ransom
notes and encryption process. 

![Code 2 Console clearing command
execution.](/assets/img/arch/DarkPower.assets/media/image5.jpeg){width="6.5in"
height="3.332638888888889in"}Code 2 Console clearing command execution.

Writing ransom notes

Every folder which is enumerated by the ransomware, will receive a copy
of the ransom note (Figure 4). Unlike the usual plain text ransom notes,
this ransom note is a PDF file. The note is created using *Adobe
Illustrator 26.0, and last* modified on the 9th of February 2023.

![Figure 4 The first page screenshot of Dark Power ransom
note.](/assets/img/arch/DarkPower.assets/media/image6.jpeg){width="6.5in"
height="4.554861111111111in"}Figure 4 The first page screenshot of Dark
Power ransom note.

The ransom note demands \$10,000 USD to a Monero blockchain address:
XMR\
*85D16UodGevaWw6o9UuUu8j5uosk9fHmRZSUoDp6hTd2ceT9nvZ5hPedmoHYxedHzy6QW4KnxpNC7MwYFYYRCdt\[redacted\]*\
The ransom note also provides a Tor website (power*\[redacted\]*.onion)
that includes claimed victims (Figure 5) and a qTox ID
(*EBBB\[redacted\]*) for chat. Both the Tor network and the qTox
messenger allow anonymity due to decentralized servers and communication
protocols. The claimed victims are shown in the figure below, where each
rectangle is a logo of the victimised company. The logos are blurred in
the image below to refrain from sharing who the victims are, but there
is no such blur on the actor's website. 

![Figure 5 Censored screenshot of the Dark Power web
page.](/assets/img/arch/DarkPower.assets/media/image7.jpeg){width="5.5152777777777775in"
height="9.0in"}Figure 5 Censored screenshot of the Dark Power web page.

Data encryption

The encryption of the files which aren't filtered out, are encrypted
using AES (CRT mode). The encrypted file paths are printed, along with a
counter, in the standard output, as can be seen in the image below.

![Figure 6 Encrypted file
paths](/assets/img/arch/DarkPower.assets/media/image8.jpeg){width="6.5in"
height="4.789583333333334in"}Figure 6 Encrypted file paths

There are two versions of the ransomware in the wild, each with a
different encryption key and format:

1.  In the first variant, the resulted sha256 digest of the randomized
    key is split into two halves. The first half is used as the AES key,
    while the second half is used as the IV (nonce). 

2.  In the second variant, the resulted sha256 digest of the randomized
    key is used as the AES encryption key, and a fixed 128-bit
    value, *\"73 4B D9 D6 BA D5 12 A0 72 7F D6 4C 1E F4 96 87\"* is used
    as the encryption nonce.

After a full encryption of the file contents, the ransomware renames
encrypted files with the *\".dark_power\"* extension.

Threat intelligence

The Dark Power ransomware group is one of the many groups to extort
their victims twice: once by encrypting the data, and once by publishing
stolen data if no payment is received.

The ransomware itself does not upload any files, leading to the
assumption that the data exfiltration is done manually, and prior to the
deployment of the ransomware.

The Dark Power gang is operating on a global scale, with claimed victims
in Algeria, the Czech Republic, Egypt, France, Israel, Peru, Turkey, and
the USA. According to the group\'s website, they have successfully
targeted ten victims. The sectors of the victims, in no particular
order, are: education, IT, healthcare, manufacturing, and food
production.

What should you do if your system is infected with Dark Power
ransomware 

It is important to note that paying the ransom does not guarantee that
the files will be decrypted. In addition, paying the ransom only
encourages and funds the activities of ransomware authors. Victims are
advised to not pay the ransom and report the attack to law enforcement
agencies and seek assistance from reputable security professionals to
recover their files. The NoMoreRansom initiative is a good place to
start if you are the victim of ransomware.

Conclusion

The adoption of new(er) languages, such as Nim, Golang, or Rust, by
malware authors is a trend that isn't new. The cost of the continuous
upkeep of knowledge from the defending side is higher than the
attacker's required skill to learn a new language. Sharing this
information, in blogs such as these, will help the community as a whole.

Dark Power shows that a pretty generic approach to ransomware can still
be effective, regardless of the language it is written in.

Appendix A - Trellix Dark Power detection signatures

Product

Signature

Endpoint Security (ENS)

Ransomware-HMQ

Endpoint Security (HX/AV/MG)

Trojan.GenericKD.65499855\
Trojan.GenericKD.65428966 

Network Security(NX)\
Detection as a Service\
Email Security\
Malware Analysis\
File Protect

Ransomware.Win64.Crypren.FEC3\
Malware.Binary.exe 

Appendix B - Key behaviors: ATT&CK techniques

Execution

Defense Evasion

Discovery

Impact

T1059 Command and Scripting Interpreter

T1027 Obfuscated Files or Information

T1082 System Information Discovery

T1486 Data Encrypted for Impact

T1047 Windows Management Instrumentation

T1140 Deobfuscate/Decode Files or Information

T1057 Process Discovery

T1490 Inhibit System Recovery

T1070.001 Indicator Removal: Clear Windows Event Logs

T1489 Service Stop

Appendix C - IoCs

SHA256 hashes of the analysed samples:

33c5b4c9a6c24729bb10165e34ae1cd2315cfce5763e65167bd58a57fde9a389
11ddebd9b22a3a21be11908feda0ea1e1aa97bc67b2dfefe766fcea467367394 

*This document and the information contained herein describes computer
security research for educational purposes only and the convenience of
Trellix customers. Any attempt to recreate part or all of the activities
described is solely at the user's risk, and neither Trellix nor its
affiliates will bear any responsibility or liability.*
