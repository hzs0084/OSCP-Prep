
|**Server Type**|**Description**|
|---|---|
|`DNS Root Server`|The root servers of the DNS are responsible for the top-level domains (`TLD`). As the last instance, they are only requested if the name server does not respond. Thus, a root server is a central interface between users and content on the Internet, as it links domain and IP address. The [Internet Corporation for Assigned Names and Numbers](https://www.icann.org/) (`ICANN`) coordinates the work of the root name servers. There are `13` such root servers around the globe.|
|`Authoritative Nameserver`|Authoritative name servers hold authority for a particular zone. They only answer queries from their area of responsibility, and their information is binding. If an authoritative name server cannot answer a client's query, the root name server takes over at that point. Based on the country, company, etc., authoritative nameservers provide answers to recursive DNS nameservers, assisting in finding the specific web server(s).|
|`Non-authoritative Nameserver`|Non-authoritative name servers are not responsible for a particular DNS zone. Instead, they collect information on specific DNS zones themselves, which is done using recursive or iterative DNS querying.|
|`Caching DNS Server`|Caching DNS servers cache information from other name servers for a specified period. The authoritative name server determines the duration of this storage.|
|`Forwarding Server`|Forwarding servers perform only one function: they forward DNS queries to another DNS server.|
|`Resolver`|Resolvers are not authoritative DNS servers but perform name resolution locally in the computer or router.|


![[Pasted image 20260202163637.png]]

Different `DNS records` are used for the DNS queries, which all have various tasks. Moreover, separate entries exist for different functions since we can set up mail servers and other servers for a domain.

|**DNS Record**|**Description**|
|---|---|
|`A`|Returns an IPv4 address of the requested domain as a result.|
|`AAAA`|Returns an IPv6 address of the requested domain.|
|`MX`|Returns the responsible mail servers as a result.|
|`NS`|Returns the DNS servers (nameservers) of the domain.|
|`TXT`|This record can contain various information. The all-rounder can be used, e.g., to validate the Google Search Console or validate SSL certificates. In addition, SPF and DMARC entries are set to validate mail traffic and protect it from spam.|
|`CNAME`|This record serves as an alias for another domain name. If you want the domain www.hackthebox.eu to point to the same IP as hackthebox.eu, you would create an A record for hackthebox.eu and a CNAME record for www.hackthebox.eu.|
|`PTR`|The PTR record works the other way around (reverse lookup). It converts IP addresses into valid domain names.|
|`SOA`|Provides information about the corresponding DNS zone and email address of the administrative contact.|

The `SOA` record is located in a domain's zone file and specifies who is responsible for the operation of the domain and how DNS information for the domain is managed.

### Default Config

There are different types of configuration types of DNS. Some of the important ones are

1. Local DNS Config Files
2. Zone files
3. Reverse Name Resolution Files

Bind9 is often used on Linux based distributions. The local config file `named.conf` is divided into two entries zone entries for the individual domains and general settings. 

- named.conf.local
- named.conf.options
- named.conf.log

Global options are general and affect all zones. A zone option only affects the zone to which it is assigned. Options that are not listed end up with default values. If an option is both global and zone specific then the zone option takes precedence. 

Local DNS Configuration

`/etc/bind/named.conf.local` - In this file we can define different zones. These zones are divided into individual files, which are intended for one domain only. Exceptions are ISP and public DNS servers. 

A zone file is a text file that describes a DNS zone with the BIND file format. There must be precisely one SOA record and at least one NS record. 

For the `Fully Qualified Domain Name(FQDN)` to be resolved from the IP address, the DNS server must have a reverse lookup file. 

The PTR records are responsible for the reverse translation of IP addresses into names. 

### Dangerous Settings

| **Option**        | **Description**                                                                |
| ----------------- | ------------------------------------------------------------------------------ |
| `allow-query`     | Defines which hosts are allowed to send requests to the DNS server.            |
| `allow-recursion` | Defines which hosts are allowed to send recursive requests to the DNS server.  |
| `allow-transfer`  | Defines which hosts are allowed to receive zone transfers from the DNS server. |
| `zone-statistics` | Collects statistical data of zones.                                            |

#### Subdomain Brute Forcing

  DNS

```shell-session
hzs0084@htb[/htb]$ for sub in $(cat /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @10.129.14.128 | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done

ns.inlanefreight.htb.   604800  IN      A       10.129.34.136
mail1.inlanefreight.htb. 604800 IN      A       10.129.18.201
app.inlanefreight.htb.  604800  IN      A       10.129.18.15
```

### Walkthrough

So i made a couple of mistakes but

When the first question asked

 Interact with the target DNS using its IP address and enumerate the FQDN of it for the "inlanefreight.htb" domain.

I was just staring at those records but it wanted me to look at the NS record of the DNS

That's were the FQDN resides

Identify if its possible to perform a zone transfer and submit the TXT record as the answer. (Format: HTB{...})

Found this flag with the  `dig axfr internal.inlanefreight.htb @10.129.14.122`

HTB{DN5_z0N3_7r4N5F3r_iskdufhcnlu34}

What is the IPv4 address of the hostname DC1?

10.129.34.16 - Found this out during the axfr process


What is the FQDN of the host where the last octet ends with "x.x.x.203"?

win2k.dev.inlanefreight.htb

This one baffled me a bit and I had to look it up because I ran dnsenum but couldn't find anything quick, the bruteforce took forever

I was on the right idea of bruteforcing it but i should have used a different hostlist

dnsenum --dnsserver 10.129.14.122 --enum -p 0 -s 0 -o subdomains.txt -f /usr/share/wordlists/seclists/Discovery/DNS/fierce-hostlist.txt dev.inlanefreight.htb

