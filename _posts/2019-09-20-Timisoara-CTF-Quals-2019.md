---
layout: post
title: Timisoara CTF Qualifiers 2019
---

Introduction
------

I recently participated in the Timisoara CTF Quals 2019 under the team acsii. We finished 12th by the end of the 5 days. The available challenge categories were: Crypto, Forensics, Reverse Engineering, Exploit, Web, Programming. The following are the challenges I solved.

---


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

The link provided lead us to a completely empty site (actually just all white). Inpsecting element also didn't show anything in the HTML. However we are provided with the source code of the site which is as follows: 

```
<?php

if (!empty($_SERVER['QUERY_STRING'])) {
    $query = $_SERVER['QUERY_STRING'];
    $res = parse_str($query);
    if (!empty($res['action'])){
        $action = $res['action'];
    }
}

if ($action === 'login') {
    if (!empty($res['key'])) {
        $key = $res['key'];
    }

    if (!empty($key)) {
        $processed_key = strtoupper($key);
    }
    if (!empty($processed_key) && $processed_key === 'hax0r') {
        echo file_get_contents( "flag.txt" );
    }
    else {
        echo 'Sorry, you don\'t have enough swag to enter';
    }
}

?>
```

From the source code, we see in order to echo the flag, 2 conditions need to be true: `$action === 'login'` and `$processed_key === 'hax0r'`. The first part is simple enough. We just need to set `actoin=login` in the get parameters which will be set in the line `$action = $res['action']`. 

At first glance, the second condition seems like a much harder task since it converts our input for `$key` to uppercase. However, in the source code, we also see a `parse_str($query)` which has the vulnerability which can be exploited to overwrite values. This means that we need not set a value for `$key` and can instead directly overwrite `$processed_key`. Hence, our payload would be:

`/?action=login&&processed_key=hax0r`

Sending this using Burp Repeater gives us the flag: TIMCTF{Welcome_M4N_of_SW4G}





