---
layout: page
title: What is SafeFinder/OperatorMac campaign?
description: 2018 
img: /assets/img/arch/SafeFinder.assets/Untitled-1.jpg
importance: 92
---

![](/web/assets/img/arch/SafeFinder.assets/Untitled-1.jpg)

What is SafeFinder/OperatorMac campaign?
========================================

A new variant of adware was just discovered yesterday. It’s going viral on Twitter and other media, since they use valid Apple developer certificate to sign all packed samples. I’m quite overbusy these days but it got my interest when seeing the name stated in that certificate: “Quoc Thinh”, quite a unique Vietnamese name. So why not take a break from desperate thesis, toss adware in my lame automated MacOS analysis framework and see what our ‘countryman’ doing?

The sample was first noticed by _Gavriel State_‏ on Aug 7, then _Thomas A Reed_ – the Mac malware boss hunter from Malwarebytes - confirmed it relates to OperatorMac on the next day. I think you all know the famous Mac free security tools’ author: _Patrick Wardle_, wrote an amazing report on [objective-see.com](https://objective-see.com/blog/blog_0x20.html) in Aug 9. So I’m not going too deep in reverse engineering - static analysis, just throw in my Grey-cuckoo framework and grab results. In case somebody doesn’t know, it’s my thesis project and and soon to be released right after my judgment day - defense (hopefully).

From Patrick’s report we understand it’s an adware which installs lots of crabs. By default, cuckoo sandbox timeout is 120 sec. Let’s extend it a bit, 300 sec (5 min) would be enough.

[![](/web/assets/img/arch/SafeFinder.assets/img_598c71a4b4bf1.png)](https://i1.wp.com/babyphd.net/wp-content/uploads/2017/08/img_598c71a4b4bf1.png?ssl=1)

First result we got, that Apple developer ID “Quoc Thinh”, and he got his certificate revoked from Apple today by the way 😄.

`spctl -avv "Mughthesec-Player(dmg).dmg"   Mughthesec-Player(dmg).dmg: **CSSMERR_TP_CERT_REVOKED**`

From captured screenshots, we can see what this adware apparently executes: Install Adobe flash and offer you a bunch of PUA (potential unwanted applications) – Booking, Advanced Mac Cleaner, SafeFinder Safari extension, and AdBlock (in some relevant samples that will be discussed later).

[![](/web/assets/img/arch/SafeFinder.assets/Webp.net-gifmaker.gif)](https://i1.wp.com/babyphd.net/wp-content/uploads/2017/08/Webp.net-gifmaker.gif?ssl=1)

Behavioral analysis shows the packed DMG sample invoked a ‘mac’ binary, thereafter ‘Mughthesec’ with a persistence ‘I’ binary. Screenshots below already included other analysis variant of this campaign.

[![](/web/assets/img/arch/SafeFinder.assets/img_598c722bde0a9.png)](https://i1.wp.com/babyphd.net/wp-content/uploads/2017/08/img_598c722bde0a9.png?ssl=1)[![](/web/assets/img/arch/SafeFinder.assets/img_598c72309c103.png)](https://i0.wp.com/babyphd.net/wp-content/uploads/2017/08/img_598c72309c103.png?ssl=1)[![](/web/assets/img/arch/SafeFinder.assets/img_598c7244ef84c.png)](https://i0.wp.com/babyphd.net/wp-content/uploads/2017/08/img_598c7244ef84c.png?ssl=1)

Move on to the network DNS feature, we see a lot of queries and some domains look "suspicious". Their DNS servers mostly are pointed to Akamai so I suggest we rather use domain as IOC than IP address, which could be different from viewers location. Filtering system calls logs with some rules of mine, there is no evasion technique been found. It’s quite surprising because Virustotal behavioral analysis shows a shorter execution trace than mine, which usually means its environment was detected at some point and malware stop running. In Patrick’s report he said there might be MAC address verification to detect VM (VM MAC usually starts with ’00:xx’). Fortunately, my VM framework MAC address was modified long ago since my colleague Yorick Koster at Securify used same trick to abuse my lame framework (thanks Yorick). I should add new rule – “MAC address check” later.

[![](/web/assets/img/arch/SafeFinder.assets/img_598c755999974.png)](https://i2.wp.com/babyphd.net/wp-content/uploads/2017/08/img_598c755999974.png?ssl=1)

Additionally instead of execve(), MacOS sandbox policy usually invokes processes using XPCProxy or launchd services. So we got several processes created with posix\_spawn(): delete Safari, iBooks, Mail cache (likely Advanced Mac cleaner doing its job), install mentioned PUAs and we got some new IOCs.

[![](/web/assets/img/arch/SafeFinder.assets/img_598c75725dafc.png)](https://i2.wp.com/babyphd.net/wp-content/uploads/2017/08/img_598c75725dafc.png?ssl=1)

Other great things from Cuckoo sandbox are Network analysis and Dropped files. However previous report detailed it well enough, hence only some screenshots from these features will be showed:

Some dropped binaries like AMCleaner (93dd0c34a4ec25a508cd6d5fb86d8ccc0c318238d9fee0c93342a20759bf9b7e) already marked as malicious on VirusTotal (VT) 7/56, which could be an indication for vigilant users.

Also with some fancy nonsense statistic screenshots, intent to scare analyst (:p)

[![](/web/assets/img/arch/SafeFinder.assets/img_598c7796efc07.png)](https://i0.wp.com/babyphd.net/wp-content/uploads/2017/08/img_598c7796efc07.png?ssl=1)

[![](/web/assets/img/arch/SafeFinder.assets/img_598c779ebc535.png)](https://i0.wp.com/babyphd.net/wp-content/uploads/2017/08/img_598c779ebc535.png?ssl=1)

At this moment, we have got all indicators to make behavioral detection rule and go hunting for other similar adware samples. Reason why I call this blog post – “a campaign”: there are numerous similar packed DMG/Mac apps matched ‘my behavioral rule’: fake Adobe Flash installer, lots of PUAs from subdomain name \[cdn, dl, api\] and Vietnamese developer certificate ID.

![image-20220319020601558](/web/assets/img/arch/SafeFinder.assets/image-20220319020601558.png)

Please note that VT score is an indicator only, people usually fail to judge hardworking AVs by looking at VT score. We can never know if AV would detect those adwares live running whether or not. Also instead of Mughthesec, other adware use different loader names such as SearchWebSvc, TrustedSafeFinder, etc. I don’t think OSX/Mughthesec would be appropriate for the adware name. I suggest it would be OperatorMac because all campaign packages call a simple loader “mac” binary.

**Conclusion**

Be **vigilant**, no one needs Flash nowadays it’s dead. Apple Mac is not virus-free even with those fancy Apple protection XProtect, GateKeeper, Mac sandbox, code signing, etc, many security researchers already warned.

It’s likely an affiliation advertising campaign, in which adware authors spent quite some money (~$800) for these 8 Apple developer certificates and only 2 of them are revoked. Some of dropped MachO executables are not signed, and we don’t know what if those can be really dangerous (like the unsigned MachO executable from APT32 Ocean Lotus campaign targeted Vietnamese organizations lately is really a sophisticated one). Based on timeline and periods of certificate registration, and money they have spent, I doubt these adware creators are making lots of money. Last, let me remind you some incidents happened recently and could be related to this campaign:

* [Malicious browser extension – fake IDM Downloader harvested around 5M cookies from popular websites, targeted Vietnamese netizens.](http://genk.vn/chuyen-gia-bao-mat-phat-hien-duong-day-chiem-doat-tai-khoan-ngan-hang-facebook-gmail-cuc-lon-o-viet-nam-ban-cung-co-the-la-nan-nhan-20170622230143671.chn)
* [Fake App store AV app – making more than $80K per month.](https://www.techsignin.com/tintuc/lua-dao-tu-viet-nam-kiem-hon-80-000-usdthang-tu-apple-store/)

[Phamus](https://twitter.com/phd_phuc)

**P/S:** I confronted one famous hacker in cyber pirate community – “Quoc Thinh” aka G4mm4, he seriously admitted he's behind the "crime". Not sure that’s true or he was just kidding :)

[![](/web/assets/img/arch/SafeFinder.assets/img_598c77b2a0e82.png)](https://i2.wp.com/babyphd.net/wp-content/uploads/2017/08/img_598c77b2a0e82.png?ssl=1)

**IOCs:**

**appfastplay.com**

Created

2017-04-08

New

\-none-

198.54.117.212

2017-04-23

Change

198.54.117.212

198.54.117.215

2017-07-07

Change

198.54.117.215

198.54.117.210

Namecheap domain hosting @ USA.

**mughthesec.com**

198.54.117.210

**SimplyEApps.com**  
198.54.117.210

**(api./dl./cdn.)dynacubeapps.com**

198.54.117.210

**(api./dl./cdn.)cloudmacfront.com**

**(api./dl./cdn.)api.airautoupdates.com**

**(api./dl./cdn.)osxessentials.com**

**(api./dl./cdn.)api.vertizoom.com**

**(api./dl./cdn.)macgabspan.com**

**install.searchwebsvc.com**

**install.trustedsafefinder.com**

**searc.trustedsafefinder.com/h?\_pg=XXXX-1234-67890**

**searc.trustedsafefinder.com/[\[email protected\]](/cdn-cgi/l/email-protection)@@&\_pg=XXXX-1234-67890**

**~/Library/LaunchAgents/com.Mughthesec.plist**

**~/Library/Application Support/com.Mughthesec**

**~/Library/LaunchAgents/SearchWebSvc.plist**

**~/Library/Application Support/SearchWebSvc**

**~/Library/LaunchAgents/TrustedSafeFinder**

**~/Library/Application Support/com.TrustedSafeFinder.plist**

**~/Library/LaunchAgents/com.pcv.hlpramcn.plist  (Advanced Mac Cleaner)**

_phan anh 4f62cc7e6f923ffd3d01de7ed47c3a62593e8c245bfc6cb81783a70fb821ca3c_

_9e72aea77562c7d85950076f8acef12580050d2bfd199de9500cd9a3cf18e5ba_

_04343158bd4942f25f8ff4e39c5bc21fa08b2e98c9f4dd3391f017667a47e59e_

_b31baed2592708d5fb8227fc7d18faa339f813ff1db1aa32580f54d0601b08f0_

_5afe86f9ec0764f53721452199383a0732d262b679356f4e5c716ca5710502c8_

_4dcde58b6bd4b415eae924b62b1f0ce4e0b8d11a714fbcf99c4da553a66751d7_

_08f2dfa3d011aa90fa90f2a2fabeb9aff72ad0acde01378f8126a3ab6ce61b41_

_7ceb92839c025760203fac5d0ad4ff63df9c92d409831b3536c99f0c7f0874c6_

_e0606875fb61db097f618b5e2ea9c140e3e5dff733ec3a30719af8452ca06aab_

_4b665b885db33498129a86b354f09b4067e51b7add4595cb231d0d0b7f8a8678_

_22ca8d75544d061d3e8b986b0af3dc2a462d9acfa29a4be5a5589fa51282dedb_

_0650ce68e2d3b1e9e53a72115f5da42120d6eff83a07aa309b1f04f11f55c1de_

_4f62cc7e6f923ffd3d01de7ed47c3a62593e8c245bfc6cb81783a70fb821ca3c_

_9e72aea77562c7d85950076f8acef12580050d2bfd199de9500cd9a3cf18e5ba_

_cf63d5861eb654efcafef25d758ca5daf877e3c63888ee086bcd29f22e3f77ad_

_04343158bd4942f25f8ff4e39c5bc21fa08b2e98c9f4dd3391f017667a47e59e_

_b31baed2592708d5fb8227fc7d18faa339f813ff1db1aa32580f54d0601b08f0_

_5afe86f9ec0764f53721452199383a0732d262b679356f4e5c716ca5710502c8_

_4dcde58b6bd4b415eae924b62b1f0ce4e0b8d11a714fbcf99c4da553a66751d7_

_08f2dfa3d011aa90fa90f2a2fabeb9aff72ad0acde01378f8126a3ab6ce61b41_

_8690299992e9a1d8bf1b5184a3274619eb6c95c44a83b42f7ee455b419947d5d_

_7ceb92839c025760203fac5d0ad4ff63df9c92d409831b3536c99f0c7f0874c6_

_e0606875fb61db097f618b5e2ea9c140e3e5dff733ec3a30719af8452ca06aab_

_f47246e7b4ae43d6aa284145292d67247d428d52f327c00a7f7af2dd65fb1c0b_

_4b665b885db33498129a86b354f09b4067e51b7add4595cb231d0d0b7f8a8678_

_22ca8d75544d061d3e8b986b0af3dc2a462d9acfa29a4be5a5589fa51282dedb_

_0650ce68e2d3b1e9e53a72115f5da42120d6eff83a07aa309b1f04f11f55c1de_

_52ac6206d109acb15547e8f655d1e522d28bbb39c9e40784126de5f27778f51c_

_cd9c564799f88ad6efae4bd5acb047d5f0d5b39378965386925c171eec3977b5_

_15d796bd76339a0fbb430bc8c6cc9bcbd6a21f3aef619d0cb81fe0069552a29d_

_99c598ef5fe10347803296d20a37fb6c33d793f35443725dbcfc41382dbd9391_

_34886986af88810585238c2ecf44924e48a08f775b25794e553689f0f2585899_

_12effeff2bc280d144bd1432f9bbfae2efaae483731d20b6f0b061e8be505a0c_

_3460877be00af3632895539567b57647ca1e07363213b4f93d52ac80b17454ee_

_79b54c71b74112881a8c4b88ad36a9bae4c2eecedcba510e2b4f52c215a9ebac_

_quoc thinh_  
_9c4f74feff131fa93dd04175795f334649ee91ad7fce11dc661231254e1ebd84_

_f5d76324cb8fcae7f00b6825e4c110ddfd6b32db452f1eca0f4cff958316869c_

_63b9e81a0c3a57bcbaf2aac308ecc53035f7fff6a416a6752acf13f16352a94a_

_431c30c1db1aefb87d5fa5485c0fd11e792ec94bb95fb401cc93ef0528ad41cb_

_687def9ff3cd0fa8dab1a7d4da5fa04b0604292fa74a66f23a96fb1eb31cd2c2_

_pham huong_

_af1e6391dd48f84beac69fbd69dbbb20eefd7ca69d33c686ed4d5a85ba760254_

_7ce4d0ec31dc334388d2461b65617ba5dbdebf935da2ed2a7d65d8a9cc14148c_

_f995bd07e5f782cf823b45d226c63407695d4a1bfb06358f49621291f1629f60_

_a2bd399d8087752776762fa9a805429de6973994f26e17bdac9ca4130dcb87fb_

_bafe800c397e69e9e6859311c437e5c4b6cdd200f3ed832306e6c9f331eb6bae_

_nhien nguyen_

_3028aa7ece2f140f6fa28d348bf18156e6e4da4cb2f9208925d15ca7b564f35f_

_fc89afe4f72c3e02dca3e998351e59684f5ad6a9000c9f2eaee3b7195d4f0fda_

_minh duc_

_17f39a0268ec97cf5528bcc9d871c7f7b428379a2549c3b01581440acd7ed8a5_

_79e821bab2adbdb9a53e471b7a1937c8f0a1ba1ce0801b8d2eca49a7c9391e42_

_dfc0b3618a3eb246ae6026c460596a102f45ff71660f7fdec39c5f105a3190cd_

_90d825d481297def07771865a5e719c4e55ed1109008721744608bf94841d7cc_

_tran phong_

_8c1b1b2b997a11d8b408091e12d02960bf71bb83304a3b23c439056c4e9882ad_

_9acb781d19d6ed4c6ac6e10448d113fca868bba21f95496f535013005fe2d29e_

_f159c3dc2aa704d42a07f145985fa7b339cbd4bcdf7ff9783220b9dd3a9e097e_

_a1dc898586e1697bd19d6c6ec8421e1871ac918132bcbcf89db9b523e199664d_

_e537d868f9ab708e3c5a8427dd4036798570b7d6b55b3ef0e1be9775e64e9c9d_

_f7468d3af9267e9a6325fb981a5bd734dafeea32265d0318cb4793bd1b52f112_

_mai linh_

_c7c11ae9fdaeef0c359621de06d4de5264cc3d62929d5e8a1e2c3d2c08290e2f_

_202d7e5bd230c59051d5c21124e9af613f70576f3511a0a79c567f48844e5b45_

_thanh thuy_

_6494a2bd4e9da2f3000b0774a13682a3f9fcd17e7da8d0fd42bfa88d1dce14f5_

_aa556a1b27356c35be3890f7c6f022c431d48ff7b5c15b2a0586609b15e5d5f6_

_f86b4d24627d5dc9d806a3f89c03aea19aaa987bcedb44fc5635140bf9191d03_

_387dddd9bb34fb1fa697f4f5c208ad425e14f6a37e943a3ddf73cca12356054b_

_086ad9c053fc3a670db2954b120733ba97109e1eb845ca383e88d2623289ae66_

_da329f9a9b2ea505fb5ffb4a8d08ff8755b3c960ce84c342c50ab7e808c835b5_

_22179b6701cf203abfa94eee9152495d409bcb9f5293fb5aa87fe342f7285a18_

_a60503e089b805ca7b99186c1b4014bbca98655eadd615f911a7379708b0405a_

_b3be9a9b5b6cd97815a1a2a5c14713b761b664e195a6e1384219a701ccc12036_

_22179b6701cf203abfa94eee9152495d409bcb9f5293fb5aa87fe342f7285a18_

_a60503e089b805ca7b99186c1b4014bbca98655eadd615f911a7379708b0405a_

_da329f9a9b2ea505fb5ffb4a8d08ff8755b3c960ce84c342c50ab7e808c835b5_

_22179b6701cf203abfa94eee9152495d409bcb9f5293fb5aa87fe342f7285a18_

_387dddd9bb34fb1fa697f4f5c208ad425e14f6a37e943a3ddf73cca12356054b_

_5b4703281a185e81113b303277e546c5f87ae599fba4565932a2638b3d40b41f_

_da329f9a9b2ea505fb5ffb4a8d08ff8755b3c960ce84c342c50ab7e808c835b5_

_5b4703281a185e81113b303277e546c5f87ae599fba4565932a2638b3d40b41f_

_5b4703281a185e81113b303277e546c5f87ae599fba4565932a2638b3d40b41f_

_b3be9a9b5b6cd97815a1a2a5c14713b761b664e195a6e1384219a701ccc12036_

_aa556a1b27356c35be3890f7c6f022c431d48ff7b5c15b2a0586609b15e5d5f6_

_a60503e089b805ca7b99186c1b4014bbca98655eadd615f911a7379708b0405a_

_da329f9a9b2ea505fb5ffb4a8d08ff8755b3c960ce84c342c50ab7e808c835b5_
