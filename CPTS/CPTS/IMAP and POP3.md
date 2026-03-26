IMAP isa. network protocol to manage emails on a remote server. It allows synchronization of local email clients with the mailbox on the server. 

POP3 only provides the capabilities to list, retrieve, and deleting emails as functions at the email server. %% Does this happen at the client level or the server level?? %%

IMAP is used for additional functionalities. It is text based and has extended functions like browsing emails directly on the server. %% Web client? %%

Commands are in *ASCII* format over port 143. 

Access is given after authentication to the server. 

The ability to send several commands in succession without waiting for confirmation from the server. Identifiers with those commands are sent so that the server can identify where the responses should be sent. 

SMTP for sending emails, and by copying the sent emails into an IMPA folder. All clients that connect to the server and have access to all sent mails that were sent, regardless of the computer from which they were sent. 

IMAP gives the capability to also create personal folders and folder structures in the mailbox. 

IMAP works unencrypted and transmits commands, emails, or usernames and passwords in plain text. 

Email servers now require an encrypted IMAP session to ensure better security in email traffic to prevent unauthorized access to mailboxes. SSL/TLS is usually used for this purpose. Either configured on the standard port `143` or the alternative `993`

## Default Configuration

#### IMAP Commands

|**Command**|**Description**|
|---|---|
|`1 LOGIN username password`|User's login.|
|`1 LIST "" *`|Lists all directories.|
|`1 CREATE "INBOX"`|Creates a mailbox with a specified name.|
|`1 DELETE "INBOX"`|Deletes a mailbox.|
|`1 RENAME "ToRead" "Important"`|Renames a mailbox.|
|`1 LSUB "" *`|Returns a subset of names from the set of names that the User has declared as being `active` or `subscribed`.|
|`1 SELECT INBOX`|Selects a mailbox so that messages in the mailbox can be accessed.|
|`1 UNSELECT INBOX`|Exits the selected mailbox.|
|`1 FETCH <ID> all`|Retrieves data associated with a message in the mailbox.|
|`1 CLOSE`|Removes all messages with the `Deleted` flag set.|
|`1 LOGOUT`|Closes the connection with the IMAP server.|

#### POP3 Commands

|**Command**|**Description**|
|---|---|
|`USER username`|Identifies the user.|
|`PASS password`|Authentication of the user using its password.|
|`STAT`|Requests the number of saved emails from the server.|
|`LIST`|Requests from the server the number and size of all emails.|
|`RETR id`|Requests the server to deliver the requested email by ID.|
|`DELE id`|Requests the server to delete the requested email by ID.|
|`CAPA`|Requests the server to display the server capabilities.|
|`RSET`|Requests the server to reset the transmitted information.|
|`QUIT`|Closes the connection with the POP3 server.|


## Dangerous Settings

Configuration options that were improperly configured could allow to leak more information by debugging the executed commands on the services or logging in as anonymous, similar to the FTP service. 

| **Setting**               | **Description**                                                                           |
| ------------------------- | ----------------------------------------------------------------------------------------- |
| `auth_debug`              | Enables all authentication debug logging.                                                 |
| `auth_debug_passwords`    | This setting adjusts log verbosity, the submitted passwords, and the scheme gets logged.  |
| `auth_verbose`            | Logs unsuccessful authentication attempts and their reasons.                              |
| `auth_verbose_passwords`  | Passwords used for authentication are logged and can also be truncated.                   |
| `auth_anonymous_username` | This specifies the username to be used when logging in with the ANONYMOUS SASL mechanism. |

## Footprinting the service

By default, ports `110` and `995` are used for POP3, and ports `143` and `993` are used for IMAP. The higher ports (`993` and `995`) use TLS/SSL to encrypt the communication between the client and server.

an attacker could log in to the mail server and read or even send the individual messages.

#### cURL

```shellsession
hzs0084@htb[/htb]$ curl -k 'imaps://10.129.14.128' --user user:p4ssw0rd 

* LIST (\HasNoChildren) "." Important 
* LIST (\HasNoChildren) "." INBOX
```

Using `-v` as verbose, you can see how the connection is made. 

We can figure out the type of TLS encryption and further details of the SSL certificate, and even the banner, which will often contain the version of the mail server that is being used in the connection. 

To interact with the IMAP or POP3 server over SSL, we can use `openssl`, as well as `ncat`.

## Walkthrough

![[Pasted image 20260326152503.png]]


![[Pasted image 20260326152518.png]]


Connect to the mailbox through IMAP because that's where emails get synced not POP3

that's the mistake that i made 

Used this guide from reddit to understand connecting with IMAP better - [https://www.atmail.com/blog/imap-101-manual-imap-sessions/](https://www.atmail.com/blog/imap-101-manual-imap-sessions/ "https://www.atmail.com/blog/imap-101-manual-imap-sessions/")


![[Pasted image 20260326152845.png]]

![[Pasted image 20260326153003.png]]

![[Pasted image 20260326152750.png]]

after interacting, select the folder that you want to be inside and look for emails

didn't know the UID so i started searching for it

then started searching the first one and there it was 

fetched it using rfc 822 but i don't know what that is

