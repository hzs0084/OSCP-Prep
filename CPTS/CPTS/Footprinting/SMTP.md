The SMTP is a protocol for sending email in an IP network. 

SMTP is combined with IMAP or POP3 protocols to fetch emails and send emails. It is a client server model, where the client is another server. 

SMTP works unencrypted without further measures and transmits all commands, data, authentication information in plain text. 

It is wrapped around a SSL/TLS encryption for security, Ports to be wary of 465, 25, and 587. 

SMTP server itself prevents spam by using authentication mechanisms that allow only authorized users to send e-mails.

ESMTP with SMTP-Auth is the protocol extension that is being used. 

SMTP client, also known as Mail User Agent (MUA), converts it into a header and body and uploads both to the SMTP server. 

MTA has a Mail Transfer Agent (MTA), to send and receive emails. The MTA checks the em-mail for size and spam and then stores it. 

To reduce load from MTA, Mail Submission Agent(MSA), checks the validity, that is the origin of the email. The MSA is also called as a relay server. 

Open relay attack on SMTP servers can be caused due to incorrect configurations.

MTA searches the DNS for the IP address of the recipient mail server. As it gets arrived at the destination SMTP server. the data packets are reassembled to form a complete e-mail and the Mail delivery agent (MDA) transfers it to the recipients mailbox.

![[Pasted image 20260204080604.png]]

SMTP has two disadvantages inherently:

1. Sending an email using SMTP does not return a usable delivery confirmation (the sender actually received the email to their inbox). The specifications of the protocol provide for this type of notification but it is not specified by default. Usually, only an English-language error message including the header of the undelivered message is returned.
2. Users are not authenticated when a connection is established, and the sender of an email is therefore unreliable. SMPT relays are often misused to send spam. Attacks like Mail spoofing. DKIM (DomainKeys) and the Sender Policy Framework (SPF) are responsible for suspicious emails being rejected or being moved to quarantine. 

ESMPT has been developed and it uses TLS which is done after EHL0 commnad by sending STARTTLS. 

STARTTLS encrypts the connection and then it is safe to use AUTH PLAIN securely. 

### Default Configuration



```shell-session
hzs0084@htb[/htb]$ cat /etc/postfix/main.cf | grep -v "#" | sed -r "/^\s*$/d"

smtpd_banner = ESMTP Server 
biff = no
append_dot_mydomain = no
readme_directory = no
compatibility_level = 2
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
myhostname = mail1.inlanefreight.htb
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
smtp_generic_maps = hash:/etc/postfix/generic
mydestination = $myhostname, localhost 
masquerade_domains = $myhostname
mynetworks = 127.0.0.0/8 10.129.0.0/16
mailbox_size_limit = 0
recipient_delimiter = +
smtp_bind_address = 0.0.0.0
inet_protocols = ipv4
smtpd_helo_restrictions = reject_invalid_hostname
home_mailbox = /home/postfix
```


Some commands that we can use when connected to the SMTP server 

|**Command**|**Description**|
|---|---|
|`AUTH PLAIN`|AUTH is a service extension used to authenticate the client.|
|`HELO`|The client logs in with its computer name and thus starts the session.|
|`MAIL FROM`|The client names the email sender.|
|`RCPT TO`|The client names the email recipient.|
|`DATA`|The client initiates the transmission of the email.|
|`RSET`|The client aborts the initiated transmission but keeps the connection between client and server.|
|`VRFY`|The client checks if a mailbox is available for message transfer.|
|`EXPN`|The client also checks if a mailbox is available for messaging with this command.|
|`NOOP`|The client requests a response from the server to prevent disconnection due to time-out.|
|`QUIT`|The client terminates the session.|
 A list of all SMTP response codes can be found [here](https://serversmtp.com/smtp-error/).


Senders can use relay server to send emails from a a SMPT server that is known and verified. 

### Dangerous Configuration

Open Relay configuration 

```shell-session
mynetworks = 0.0.0.0/0
```

With this setting, this SMTP server can send fake emails and thus initialize communication between multiple parties. Another attack possibility would be to spoof the email and read it.

### Walkthrough

Enumerated the IP with nmap

Found nothing from -sV scan

Used telnet ip 25 to grab the banner

Then I had to download a footprint-wordlist.zip file to look for the username that exists on the server

I tried Metasploit, SMTP enum