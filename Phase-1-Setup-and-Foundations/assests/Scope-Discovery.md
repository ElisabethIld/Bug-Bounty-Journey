## WHOIS and Reverse WHOIS

`$ whois facebook.com` is not always available, as some organizations and individuals use a service called *domain privacy*.

Reverse WHOIS tool like **ViewDNS.info** - https://viewdns.info/reversewhois/

## IP Addresses

Find the IP address of a domain `$ nslookup facebook.com`

**Reverse IP** searches look for domains hosted on the same server, given an IP or domain. You can also use **ViewDNS.info** for this.

`NetRange` field. An *IP range* is a block of IP addresses that all belong to the same organization.

```
$ whois 157.240.2.35
NetRange: 157.240.0.0 - 157.240.255.255
CIDR: 157.240.0.0/16
NetName: THEFA-3 etc..
```

**Autonomous System Numbers (ASNs)** identify the owners of these networks. et. By checking if *two IP addresses* share *an ASN*, 
you can determine whether the IPs belong to the *same owner*.

We can deduce that any IP within the *157.240.2.21* to *157.240.2.34* range probably belongs to *Facebook*:

```
$ whois -h whois.cymru.com 157.240.2.20
AS | IP | AS Name
32934 | 157.240.2.20 | FACEBOOK, US

$ whois -h whois.cymru.com 157.240.2.27
AS | IP | AS Name
32934 | 157.240.2.27 | FACEBOOK, US

$ whois -h whois.cymru.com 157.240.2.35
AS | IP | AS Name
32934 | 157.240.2.35 | FACEBOOK, US
```

The `-h` flag in the `whois` command sets the **WHOIS server** to retrieve information from.

`whois.cymru.com` is a database that translates **IPs** to **ASNs**.

If the company has a dedicated IP range and doesn’t mark those addresses as out of scope, you could plan to attack every IP in that range.

## Certificate Parsing (SSL - Secure Sockets Layer)

Used to **encrypt** web traffic.

An **SSL** certificate’s *Subject Alternative Name* field.

Online databases like **crt.sh** - https://crt.sh/, **Censys**, and **Cert Spotter**.

The **crt.sh** website also has a useful utility that lets you retrieve the information in **JSON** format:
`https://crt.sh/?q=facebook.com&output=json`

<img width="558" height="661" alt="image" src="https://github.com/user-attachments/assets/cb2a0ba8-4e1f-4a9f-9799-fd2842ec1068" />

## Subdomain Enumeration

**Sublist3r**, **SubBrute**, **Amass**, and **Gobuster** can enumerate subdomains automatically.

Daniel Miessler’s *SecLists* at https://github.com/danielmiessler/SecLists/. 

**Commonspeak2** (https://github.com/assetnote/commonspeak2/) to generate wordlists based on the most current internet data.

Command to **remove duplicate** items from a set of two wordlists:
`sort -u wordlist1.txt wordlist2.txt`

`-u` - to return only **unique** items in the sorted list.

### Gobuster is a tool for brute-forcing.

 `-d` to specify the domain, `-w` to specify the wordlist

 ```gobuster dns -d target_domain -w wordlist```

**Altdns** (https://github.com/infosec-au/altdns/) - automating which discovers subdomains with names that are permutations of other subdomain names.

For example, if you’ve already learned that *example.com* uses **Jenkins**, you can check if *jenkins.example.com* is a valid subdomain.

Look for **subdomains** of **subdomains**: *dev.example.com*, you might find subdomains like *1.dev.example.com*.

Running enumeration tools recursively: **add the results** of your first run to your Known Domains list and **run the tool again**.


## Service Enumeration

- In *active scanning*, you **directly** engage with the *server*.

Tools like **Nmap** or **Masscan**

Reveals the **open ports** on *scanme.nmap.org* (target):

```$ nmap scanme.nmap.org```

- In *passive scanning*, you use third-party resources **without** interacting with the *server*.

 Is **stealthier** and helps attackers **avoid detection**.

**Censys**, **Project Sonar**, **Shodan** - a search engine that lets the user find machines connected to the internet.

If you run a **Shodan** search on *scanme.nmap.org*’s IP address, *45.33.32.156*, you get the result:

<img width="1890" height="711" alt="image" src="https://github.com/user-attachments/assets/a003c79d-fd6b-49e1-93db-e5275e8b6714" />

and provides *additional information* about the server.

## Directory Brute-Forcing

Directory brute-forcing can sometimes allow you to **directly take over a server**!

For example, a pathname that includes `phpmyadmin` usually means that the application is built with `PHP`.

**Dirsearch** or **Gobuster** use wordlists to construct URLs, and then request these URLs from a web server.

If the server responds with a **status code** in the `200` range, the directory or file **exists**. 

Examine `403` pages carefully to see if you can bypass the **protection to access** the content.

`-u` - specifies **the hostname** and `-e` - specifies **the file extension** to use when constructing URLs:

```$ ./dirsearch.py -u scanme.nmap.org -e php``` or

```$ dirsearch -u scanme.nmap.org -e php``` (for install `sudo apt install dirsearch`).
