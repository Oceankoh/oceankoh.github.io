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

We are given 3 files: source code, binary, encoded flag. By analysing the source code, we can see that there is a seed determined by time(0). A quick google tells us time(0) return s us the current unix timestamp in seconds. This means, if we know when the program was executed, we are able to find to seed. Lucky for us, we can rougly estimate the time from the metadata of the encoded flag file. `Exiftool` gives us the following details:

```
ExifTool Version Number         : 10.80
File Name                       : enc_flag.txt
Directory                       : .
File Size                       : 48 bytes
File Modification Date/Time     : 2019:09:04 01:43:33+08:00
File Access Date/Time           : 2019:09:22 01:23:28+08:00
File Inode Change Date/Time     : 2019:09:22 01:17:51+08:00
File Permissions                : rw-rw-r--
Error                           : Unknown file type
```
Knowing this, we can simply convert the timestamp in the metadata to seconds using online tools, and we get `1567532613`. Using this value, we can brute the seed backwards to account for execution time of the code which may have affected the value of the seed. However, for this challenge, the execution time is quite fast(around 0.007s) which meant the seed was the converted time. Modifying the given source code a little, we have:

```c
#include <iostream>
#include <fstream>
#include <iomanip>
#include <string.h>
#include <time.h>
#include <openssl/aes.h>
#include <openssl/sha.h>

unsigned char iv[16] = {0};
unsigned char key[20];

using namespace std;

int main(int argc, char * argv[])
{
    if(argc < 2)
    {
        cout<<"Usage: ./secure_encryptor <filename>\n";
        return 0;
    }

    ifstream in;
    in.open(argv[1], ios::in | ios::binary | ios::ate);
    int filesize = in.tellg();
    if(filesize == -1)
    {
        cout<<"Unable to open file!\n";
        return 0;
    }
    in.seekg(0, in.beg);

    unsigned char * image = new unsigned char [filesize + 100];
    unsigned char * enc = new unsigned char [filesize + 100];

    memset(image, 0, filesize + 100);
    int padded = (filesize + 16) & 0xFFFFFFFFFFFFFFF0LL;

    in.read ((char *)image, filesize);
    in.close();
	uint32_t seed = 1567532613;
	SHA1((const unsigned char *)&seed, 4, key);
	AES_KEY dec_key;
	AES_set_decrypt_key(key, 128, &dec_key);
	AES_cbc_encrypt(image, enc, padded, &dec_key, iv, AES_DECRYPT);
    cout<<enc;
    return 0;
}
```

After compiling and executing the code with given enc_flag as the input file, we get: `TIMCTF{D0_not_truST_ChRONos1234}`


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

Viewing the png gives us the flag: `flag{I_s33_the_uns33n}`


Web
------
#### Secret Key of Swag [150]

The link provided lead us to a completely empty site (actually just all white). Inspecting element also didn't show anything in the HTML. However we are provided with the source code of the site which is as follows: 

```php
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

From the source code, we see in order to echo the flag, 2 conditions need to be true: `$action === 'login'` and `$processed_key === 'hax0r'`. The first part is simple enough. We just need to set `action=login` in the get parameters which will be set in the line `$action = $res['action']`. 

At first glance, the second condition seems like a much harder task since it converts our input for `$key` to uppercase. However, in the source code, we also see a `parse_str($query)` which has the vulnerability which can be exploited to overwrite values. This means that we need not set a value for `$key` and can instead directly overwrite `$processed_key`. Hence, our payload would be:

`/?action=login&&processed_key=hax0r`

Sending this using Burp Repeater gives us the flag: `TIMCTF{Welcome_M4N_of_SW4G}`





