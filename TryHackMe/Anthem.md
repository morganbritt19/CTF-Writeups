# Anthm THM Room
## Scanning
### Nmap
```
nmap -sC -sV -oN nmapInitial target-ip
```

```python
Starting Nmap 7.60 ( https://nmap.org ) at 2023-09-22 15:04 BST
Nmap scan report for ip-10-10-193-115.eu-west-1.compute.internal (target-ip)
Host is up (0.00053s latency).
Not shown: 998 filtered ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=WIN-LU09299160F
| Not valid before: 2023-09-21T14:00:00
|_Not valid after:  2024-03-22T14:00:00
|_ssl-date: 2023-09-22T14:04:46+00:00; -1s from scanner time.
MAC Address: 02:AA:AA:21:A9:3F (Unknown)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -1s, deviation: 0s, median: -1s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 109.51 seconds
```

### Gobuster
```
gobuster dir -u http://target-ip -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

```python
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://target-ip
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2023/09/22 15:07:20 Starting gobuster
===============================================================
/search (Status: 200)
/blog (Status: 200)
/rss (Status: 200)
/sitemap (Status: 200)
/archive (Status: 301)
/categories (Status: 200)
/authors (Status: 200)
/Search (Status: 200)
/tags (Status: 200)
/install (Status: 302)
/RSS (Status: 200)
/Blog (Status: 200)
/Archive (Status: 301)
/SiteMap (Status: 200)
/siteMap (Status: 200)
/INSTALL (Status: 302)
/Sitemap (Status: 200)
/1073 (Status: 200)
/Rss (Status: 200)
/Categories (Status: 200)
```
***Note: This is not a complete scan, but a complete scan would've taken much longer than necessary for this challenge.***

## Enumeration
- We can start by visiting the webpage and see what we can find. When navigating to that IP address through a browser, we can see the following:

![Anthem Web Page](https://github.com/morganbritt19/CTF-Writeups/assets/60797871/572fb88b-8825-4cce-b3fe-eb80e9f46483)


- We can view the page source for this page and see if there is anything interesting. At first, I didn't notice it, but there seems to be a flag contained in HTML for the search bar. According to THM, this is flag 2: `THM{G!T_G00D}`
- Now that we've done that, we can do some more basic enumeration like viewing the page source and clicking around on other pages. While doing this, we can find some potentially relevant information on the `We are Hiring` article. 

![We Are Hiring Page](https://github.com/morganbritt19/CTF-Writeups/assets/60797871/bd829400-92c1-4840-a2d0-446d3cef8311)



- I was working through manually looking at the directories located with [Gobuster](https://github.com/OJ/gobuster) when I stumbled across this:

![Authors Flag](https://github.com/morganbritt19/CTF-Writeups/assets/60797871/7ec57c2c-97ab-4cc9-93dd-7ff2cc4af3f7)



- Looks like we found a flag. Great! It is important to note that this is flag 3 on the TryHackMe challenge page, which is interesting. We'd better keep looking around for the others. 
	- Flag: `THM{LoL_WHo_D15}`

- We can also look at the `robots.txt` file and see if there are any directories that [Gobuster](https://github.com/OJ/gobuster) couldn't or wouldn't look for. We get the following information:
```
UmbracoIsTheBest!

# Use for all search robots
User-agent: *

# Define the directories not to crawl
Disallow: /bin/
Disallow: /config/
Disallow: /umbraco/
Disallow: /umbraco_client/
```

- This looks like it gives us some information about the CMS on which the blog is hosted. The CMS system is Umbraco, which is something that we may be able to enumerate or exploit later. The `UmbracoIsTheBest!` string is pretty interesting here. Honestly, it looks like a password, and the THM questions confirmed this. We should make note of that. Let's keep looking. 
- Let's go and check out the `/umbraco` directory. When we arrive, we are greeted with a login page:

![Umbraco Login Page](https://github.com/morganbritt19/CTF-Writeups/assets/60797871/937e2776-288b-47ac-9e91-62516e3c2875)


- I tried using the email we uncovered earlier with the `UmbracoIsTheBest!` password, but that didn't work. So I visited some other places and I found another article that was written that could point us in the direction of discovering a username:

![Potential Username from Author](https://github.com/morganbritt19/CTF-Writeups/assets/60797871/31d8d3a5-34cf-4b1d-a9bb-b22e29e22963)



- The authors name (`James Orchard Halliwell`) may lead us to a username if we can discern the email address scheme for this website. However, the poem can lead us to some information. If you are unaware, the poem is about a character from an English nursery rhyme named `Solomon Grundy`. I would love to tell you I knew that, but I only recognized the `Solomon Grundy` rhyme from the Batman Arkham games. Interestingly enough, `Solomon Grundy` is the name of the administrator, which we can discern via the author thanking the admin for his hard work during the site redesigning. 
- With the name of the administrator, we can try to extrapolate the admin email address. From what we've seen so far, it looks like the email naming convention is just the initials of the users name. This can be seen via the `Jane Doe` user having the email `jd@anthem.com`. It stands to reason that the `Solomon Grundy` user would have something similar, meaning his email would be `sg@anthem.com`. This is confirmed via the THM questions. 
- This username, paired with the password we found earlier, let's use effectively authenticate to the Umbraco CMS. When we log in, we are greeted by the following page:


![Umbraco Dashboard](https://github.com/morganbritt19/CTF-Writeups/assets/60797871/8892986d-b1bd-402d-a21e-42279f97453a)


- Let's look around and see what we can see. 
- After spending more time than I care to admit looking for places to upload scripts or edit stuff in the developer tab, I went back to my initial [[Nmap]] scan. RDP is also open, so we may be able to leverage that to get a foothold onto the box, rather than something as hackery as a reverse shell. 

- At this point, I have only found two of the four flags. I could keep looking around on the web pages, but I think it might be easier to get a copy of everything using something like `wget`. If I pull all of this down onto my machine, I may be able to automate searching a little more easily. To do this, I used the following syntax:
```
wget --recursive target-ip
```

- Now that I have it all, I can open the entire directory in Sublime Text and use its find function to search for every instance of `thm`. I did this and was easily able to find the following flags:
	- Flag 1 - `THM{L0L_WH0_US3S_M3T4}` found in  `target-ip/archive/we-are-hiring/index.html`  in the `<meta content>` tag
	- Flag 4 - `THM{AN0TH3R_M3TA}` found in `target-ip/archive/a-cheers-to-our-it-department/index.html` in the `<meta content>` tag.

## System Hacking
- It looks like our administrator reused his credentials for RDP. We can successfully RDP into the system with the username/password combination of `sg`/`UmbracoIsTheBest!`. 
- Once on the box, we can see the `user.txt` file on the desktop. 
	- `user.txt` - `THM{N00T_NO0T}`

## Privilege Escalation
- Since I have a GUI to work with on the machine via RDP, it might be worth looking around in the File Explorer. Since I'm doing this manually, I decided to check the 'View Hidden Items' so I can find anything juicy that isn't readily viewable. 
- When I navigated to the `C:\` drive, I noticed a hidden folder called `backup` that has a file in it called `restore`. Now, we don't have permission to open this file, but we can inspect its properties to maybe get a little more information. 
- Strangely enough, we can't open the file, but we can add ourselves to the group that can:

![Add User to Restore Permissions](https://github.com/morganbritt19/CTF-Writeups/assets/60797871/5b34de98-d5af-439e-a46d-8bb0aee2bf28)

![SG Added to Group](https://github.com/morganbritt19/CTF-Writeups/assets/60797871/06141bd5-005e-4ee9-b0e8-3ae1547c9cdc)



- Now that we can view the `restore` file, we can get the admin password hidden within: `ChangeMeBaby1MoreTime`.
- All we have to do at this point is navigate to the `Users\Administrator\Desktop` folder and grab the `root.txt` file.
	- `root.txt` - `THM{Y0U_4R3_1337}`

## THM Questions
### Website Analysis
1. Let's run [Nmap](https://nmap.org/) and check what ports are open.
2. What port is for the web server?
	1. 80
3. What port is for remote desktop service?
	1. 3389
4. What is a possible password in one of the pages web crawlers check for?
	1. `UmbracoIsTheBest!`
5. What CMS is the website using?
	1. Umbraco
6. What is the domain of the website?
	1. anthem.com
7. What's the name of the Administrator?
	1. Solomon Grundy
8. Can we find the email address of the administrator?
	1. `sg@anthem.com`

### Spot the Flags
1. What is flag 1?
	1. `THM{L0L_WH0_US3S_M3T4}`
2. What is flag 2?
	1. `THM{G!T_G00D}`
3. What is flag 3?
	1. `THM{L0L_WH0_D15}`
4. What is flag 4?
	1. `THM{AN0TH3R_M3TA}`

### Final Stage
1. Let's figure out the username and password to log in to the box. (The box is not on a domain).
2. Gain initial access to the machine, what is the contents of `user.txt`?
	1. `user.txt` - `THM{N00T_NO0T}`
3. Can we spot the admin password?
	1. `ChangeMeBaby1MoreTime`
4. Escalate your privileges to root. What is the contents of `root.txt`?
	1. `THM{Y0U_4R3_1337}`
