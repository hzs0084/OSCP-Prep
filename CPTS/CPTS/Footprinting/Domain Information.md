
The goal is to gather information and try to understand the company's functionality and which technologies and structures are necessary for services to be offered successfully and efficiently. 

When passively gathering information, we should use third-party services to understand the company better. 

The first thing we should do is go through company's main website. We should read through the texts, keeping in mind what technologies and structures are needed for these services. 

We need to pay attention to what <u>we see</u> and <u>we do not see</u>

We see the services but not their functionality. These services are bound to certain technical aspected necessary to provide a service. 

- SSL Certificate
	- Certificates includes more than just a subdomain and this means that the certificate is used for several domains, and these are most likely still active. 
	- crt.sh: Useful for finding more subdomains.
		- Certificate Transparency is for monitoring and auditing the issuance of digital certificates. The security of encrypted internet traffic depends on the trust that certificates are only given out by the CA and that the certificate authority has not been compromised. 
		- CT makes public all issued certificates in the form of a distributed ledge, giving website owners and auditors the ability to detect and expose inappropriately issued certificates. 

We can identify the hosts directly accessible from the internet and not hosted by third-party providers. %% So how do we know what's from first part and what's from third party?%%

- Company Hosted Servers
	- Shodan can be used to find devices and systems on these domains. 
- DNS Records
	- <u>A</u> records: IP addresses
	- <u>MX</u> records: The mail server records
	- <u>NS</u> records: Shows with nameservers are used to resolve the FQDN to IP addresses. Most hosting providers use their own name servers, making it easier to identify the hosting provider. 
	- <u>TXT</u> records: The type of record containing verification keys for different third party providers and other security aspects of DNS, such as SPF, DMARC, and DKIM, which are responsible for verifying and confirming the origin of the emails sent. 

