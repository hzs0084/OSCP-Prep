
FTP runs within the application layer of the TCP/IP stack. It's on the same layer as HTTP or POP. 

In an FTP connection, two channels are opened. First, the client and server establish a control channel through TCP port 21. 

Both communicating participants establish the data channel via TCP port 20. This channel is used exclusively for data transmission, and the protocol watches for errors during this process. If a connection is broken off during transaction, the transport can be resumed after re-establishing contact. 

FTP is a clear-text protocol that can be sniffed if conditions on the network are right. There's also a possibility that the server offers anonymous FTP. 

### TFTP

TFTP is simpler than FTP and performs file transfers between client and server processes. It <u>does not</u> provide user authentication and other valuable features supported by FTP. 

TFTP uses UDP making it an unreliable protocol. 

TFTP does not have directory listing functionality. 

### Default Configuration

vsFTPD is one of the most used FTP servers on linux based distributions. 

The default configuration can be found in `/etc/vsftpd.conf` 

`/etc/ftpusers` contains the name of the users that <u>DO NOT</u> permissions to log in to the FTP service. 

### Dangerous Settings

| **Setting**                    | **Description**                                                                    |
| ------------------------------ | ---------------------------------------------------------------------------------- |
| `anonymous_enable=YES`         | Allowing anonymous login?                                                          |
| `anon_upload_enable=YES`       | Allowing anonymous to upload files?                                                |
| `anon_mkdir_write_enable=YES`  | Allowing anonymous to create new directories?                                      |
| `no_anon_password=YES`         | Do not ask anonymous for password?                                                 |
| `anon_root=/home/username/ftp` | Directory for anonymous.                                                           |
| `write_enable=YES`             | Allow the usage of FTP commands: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE, and SITE? |


|**Setting**|**Description**|
|---|---|
|`dirmessage_enable=YES`|Show a message when they first enter a new directory?|
|`chown_uploads=YES`|Change ownership of anonymously uploaded files?|
|`chown_username=username`|User who is given ownership of anonymously uploaded files.|
|`local_enable=YES`|Enable local users to login?|
|`chroot_local_user=YES`|Place local users into their home directory?|
|`chroot_list_enable=YES`|Use a list of local users that will be placed in their home directory?|

|**Setting**|**Description**|
|---|---|
|`hide_ids=YES`|All user and group information in directory listings will be displayed as "ftp".|
|`ls_recurse_enable=YES`|Allows the use of recurse listings.|

#### Download All Available Files on FTP

```shell-session
wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136
```

We can also interact with the service using net cat and telnet

```shell-session
nc -nv 10.129.14.136 21
```

```shell-session
telnet 10.129.14.136 21
```

OpenSSL

```shell-session
 openssl s_client -connect 10.129.14.136:21 -starttls ftp
```


