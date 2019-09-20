---
layout: post
title: Timisoara CTF Qualifiers 2019
---

Introduction
------
I recently participated in the Timisoara CTF Quals 2019 under the team acsii. We finished 12th by the end of the 5 days. The available challenge categories were: Crypto, Forensics, Reverse Engineering, Exploit, Web, Programming. The following are the challenges I solved.



Crypto
------
#### Don't Trust Time [200]




#### Strange Cipher [250]


Forensics
------
#### Deleted File [100]

The challenge provided us with a a `.img` file. Running file on it gives us:

```
file 10MB.img 
10MB.img: Linux rev 1.0 ext4 filesystem data, UUID=fd3671d1-d6d0-4e22-a8b1-ed5f3bbb0159 (extents) (64bit) (large files) (huge files)
```

Knowing this, we can mount the file to access the contents inside. However, I was lazy and just used `foremost` to carve out the contents inside. The output is as follows:

```
-------------------------------------------------------------------
File: 10MB.img
Start: Fri Sep 13 22:08:52 2019
Length: 10 MB (10485760 bytes)
 
Num	 Name (bs=512)	       Size	 File Offset	 Comment 

0:	00016550.jpg 	     139 KB 	    8473600 	 
1:	00020226.jpg 	      46 KB 	   10355712 	 
2:	00020354.png 	      47 KB 	   10421248 	  (493 x 494)
Finish: Fri Sep 13 22:08:52 2019

3 FILES EXTRACTED
	
jpg:= 2
png:= 1
-------------------------------------------------------------------
```

Viewing the png gives us the flag: flag{I_s33_the_uns33n}


Reverse Engineering
------
#### Math [150]

Web
------
#### Secret Key of Swag [150]
