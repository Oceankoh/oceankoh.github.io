---
layout: post
title: Cyberthon Finals 2020
---

Introduction
------

Local CTF held by CSIT. I think I played quite decently this time compared to last year's failure :( The following are the challenges I solved/played a part in solving. For solutions to other problems, you may check out write-ups by my other teammates (or go check out the legends in team HCI's write-ups).

---

Network Security
------
#### What was Leaked

The challenge description talks about a `.7z` file being sent through the network. Presumably, this file is in the `.pcap` supplied to us. There is also mention of a password, so we'll have to keep a look out for that as well. 

Opening in wireshark, we first see the 7 zip file in `tcp.stream 18`, when there is a `POST` request to `/upload.php`. We can identify that it is indeed a 7z by looking at the file header. However, this is not the end of the contents. To extract all bytes, we select File > Export Objects > HTTP, and save the packets where `Filename = upload.php`. By looking at the contents of the packets, we can deduce that the first 3 packets contain the 7z file, since there is no weird unreadable data in the 4th packet. 

Now we have to reconstruct our 7z file. To do so, we need to remove some bytes from the top and bottom. The bytes to remove are:
```
-----------------------------7e41241a101d6
Content-Disposition: form-data; name="fileToUpload"; filename="data.7z.001"
Content-Type: application/octet-stream

**redacted 7z file data**

-----------------------------7e41241a101d6
Content-Disposition: form-data; name="submit"

Upload Image
-----------------------------7e41241a101d6--
```

It is also important to note that while visually, it appears that we just need to remove bytes until the last "-", this is not the case. We also have to delete the bytes `0D 0A` as they are not part of our zip file data. This can be deduced by comparing the 4 different packets extracted and noticing that these are common across all of them. 

After making the necessary modifications we can construct our zip file by appending the bytes in the same packet order we extracted. This can be done using the following command:

```bash
cat extracted1 extracted2 extracted3 > extracted.7z
```

if we try to unzip, we are prompted for a password. This can be found in the 4th packet whose contents are:
```

-----------------------------7e419426101d6
Content-Disposition: form-data; name="password"

4H%XND63TsXJo#cFMx^kL
-----------------------------7e419426101d6
Content-Disposition: form-data; name="submit"

Upload Image
-----------------------------7e419426101d6--
```

It may look like gibberish but the password is `4H%XND63TsXJo#cFMx^kL`. Unzipping the file, we have a massive text wall which we're definitely not meant to read... To get the flag, we just do `grep Cyberthon data.txt`, and just like that we get the flag.

Flag: `Cyberthon{D4t48r3@ch!!11!one}`




#### Patience is Key Element of Success

This challenge was worth an insane amount of points. This challenge took some massive teamwork to solve, even though on hindsight it wasn't actually that hard. From the challenge description we can tell 

* Our first clue lies in port range 2000-2100 for the IP 167.99.28.65
* Expect it to be slow

```python
from pwn import *

for i in range(100):
	r = remote('167.99.28.65', 2000+i)
	print r.recvall()
```

To speed up this process, we split the workload between our 4 PCs, so each of us took 25 ports. I think you could just open 4 terminals and do the same thing? but sending code through Discord is easier so welp...

Anyway, after running the script, port 2042 tells us: `Secret door @ 4242`.

So we just netcat port 4242 and we are told:
```
220 pyftpdlib 1.5.6 ready.
421 Control connection timed out.
```

A quick google tells us that this is an FTP server. So connecting to the FTP server via the web browser, we are presented with some files. Amongst those, is the ssh key. So we just have to download it, which takes a lot of patience cause the network transmission speed is insanely slow...

After that it's just a matter of adding the SSH key to your machine. We can do so by just copying all the files files downloaded form the FTP server to `~/.ssh` in your machine, making sure your permissions are configured correct, or else they might cause an issue connecting. In particular, for at least these 2 files, ensure the permission are:
```
-rw-r--r--  1 ocean ocean  381 May  2 23:02 authorized_keys
-rw-------  1 ocean ocean 1679 May  2 15:08 id_rsa
```

Then it is just a matter of connecting to the same IP via SSH. However, we have to login as admin (cause why not) and not root. Listing the directory, we find  `secret.txt`, which is also the flag.

Flag: `Cyberthon{RSA_Is_Only_Secure_If_Key_Was_Secured}`


Web Services
------
#### Caged Up

There are many solutions to this problem. Most of them require messing with the console. The HTML comments hint at using the destory function, but I never got that to work, so here is my sort of unintended solution.

First we are presented with a game. I had previously seen this another CTF so I knew that I had to modify the variables somehow. When we move all the way down, we can see "Promo Code is Below". This suggest we need to somehow move beyond this point. 

![Promo Code](../../attachments/cyberthon/promoCode.png)

Upon inspection of the javascript, I noticed variable `esacpe_A` seemed quite important. I confirmed this by setting `escape_A = null`, and this made the game unplayable, which meant I were clearly doing something we aren't supposed to, so let's take a closer look. We do this by just printing it to console with:
```js
console.log(escape_A)
````

![escape_A](../../attachments/cyberthon/escapeA.png)

From the above, the first thing that caught my eye was `camera`. If we could somehow control the camera, it wouldn't matter if the player is stuck, we could still view the flag. One of the properties of camera is `position` (And for a short moment there I thought I solved it). 

![camera](../../attachments/cyberthon/cameraProperties.png)

Unfortunately, it is not as simple as that. Changing the x and y coordinates didn't seem to do anything at all. After around 10 mins of playing around, I noticed the `world` variable in camera also had a position property. Furthermore, the values of x and y also seemed to change when I moved the player. So instead I tried changing that. After doing

```js
escape_A.camera.world.position.y=-100
``` 


I am greeted with a nice surprise. 

![Flag1](../../attachments/cyberthon/flag_1.png)

Now I just need to shift the x coordinate to the right and read the flag. 

![Flag2](../../attachments/cyberthon/flag_2.png)

Flag: `Cyberthon{client_sided_javascript_cant_cage_me_up}`