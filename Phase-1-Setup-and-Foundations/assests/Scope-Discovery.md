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
