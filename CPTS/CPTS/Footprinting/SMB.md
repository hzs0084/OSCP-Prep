
SMB protocol enables the client to talk to the other participants in the same network, which allows the participants to access files or services that are shared with it on the network.

SMB uses TCP for the three-way handshake between the client and server before a connection is established. 

Access rights are defined by ACLs. Attributes such as execute, read, and full access are given for individual users or user groups. The ACLs are defined based on the shares and therefore do not correspond to the rights assigned locally on the server. 

Looks at the rights given on the shares and not that were assigned locally on the server. 


### Samba

An alternative implementation of the SMB server is Samba. Which is developed for Unix-based OS. 

Samba implements Common Internet File Systems(CIFS) network protocol. A dialect of SMB, the original was created by Microsoft. 

Connections occur over TCP ports 137, 138, and 139. 

CIFS operates over TCP port 445 exclusively. 

SMB 2 and SMB 3 offer more improvements and are preferred in modern infrastructure. Older version SMB 1 may be used in specific environments. 

In a network, each host participates in the same workgroup. This workgroup is used to identify an arbitrary collection of computers and their resources on an SMB network. 

There can be multiple workgroups on the network at any given time. 

When a machine foes online, each host reservers it's hostname on the network or NetBIOS Name server assigns it one. It has been enhanced to WINS (Windows Internet Name Service)

This is done resolve the name of the machine to an IP. 

### Configuration Settings

|**Setting**|**Description**|
|---|---|
|`[sharename]`|The name of the network share.|
|`workgroup = WORKGROUP/DOMAIN`|Workgroup that will appear when clients query.|
|`path = /path/here/`|The directory to which user is to be given access.|
|`server string = STRING`|The string that will show up when a connection is initiated.|
|`unix password sync = yes`|Synchronize the UNIX password with the SMB password?|
|`usershare allow guests = yes`|Allow non-authenticated users to access defined share?|
|`map to guest = bad user`|What to do when a user login request doesn't match a valid UNIX user?|
|`browseable = yes`|Should this share be shown in the list of available shares?|
|`guest ok = yes`|Allow connecting to the service without using a password?|
|`read only = yes`|Allow users to read files only?|
|`create mask = 0700`|What permissions need to be set for newly created files?|

The global settings and two shares in the configuration file, the `[printers]` and `[print$]` are intended for printers. 

The `[global settings]` are the configuration setting for all shares in SMB. But, these can be overwritten for individual shares. 

### Dangerous Settings

|**Setting**|**Description**|
|---|---|
|`browseable = yes`|Allow listing available shares in the current share?|
|`read only = no`|Forbid the creation and modification of files?|
|`writable = yes`|Allow users to create and modify files?|
|`guest ok = yes`|Allow connecting to the service without using a password?|
|`enable privileges = yes`|Honor privileges assigned to specific SID?|
|`create mask = 0777`|What permissions must be assigned to the newly created files?|
|`directory mask = 0777`|What permissions must be assigned to the newly created directories?|
|`logon script = script.sh`|What script needs to be executed on the user's login?|
|`magic script = script.sh`|Which script should be executed when the script gets closed?|
|`magic output = script.out`|Where the output of the magic script needs to be stored?|
### SMBClient - Connecting to the share

```shell-session
smbclient -N -L //10.129.14.128
```

-N: login as anonymous
-L: Display a list of server's shares

Output:

```shell-session
hzs0084@htb[/htb]$ smbclient -N -L //10.129.14.128

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        home            Disk      INFREIGHT Samba
        dev             Disk      DEVenv
        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)
SMB1 disabled -- no workgroup available
```

Smbclient allows us to execute local system commands using an exclamation mark at the beginning `!<cmd>` without interrupting the connections

`smbstatus` to check connections. 

### RPC Client

`rpcclient -U "" <ip.addr>` 

The rpcclient offers requests with which specific functions can be executed on the SMB server to get information. 

| **Query**                 | **Description**                                                    |
| ------------------------- | ------------------------------------------------------------------ |
| `srvinfo`                 | Server information.                                                |
| `enumdomains`             | Enumerate all domains that are deployed in the network.            |
| `querydominfo`            | Provides domain, server, and user information of deployed domains. |
| `netshareenumall`         | Enumerates all available shares.                                   |
| `netsharegetinfo <share>` | Provides information about a specific share.                       |
| `enumdomusers`            | Enumerates all domain users.                                       |
| `queryuser <RID>`         | Provides information about a specific user.                        |

#### Brute Forcing User RIDs

Using `enumdomusers` and `queryuser` we can find more information about the users and users are more likely to be an attack vector than processes that are set in place. 

Querying groups can also help us understand how group perms are set up and whom to attack. 

An example:

```shell-session
hzs0084@htb[/htb]$ for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done

        User Name   :   sambauser
        user_rid :      0x1f5
        group_rid:      0x201
		
        User Name   :   mrb3n
        user_rid :      0x3e8
        group_rid:      0x201
		
        User Name   :   cry0l1t3
        user_rid :      0x3e9
        group_rid:      0x201
```

An alternative to this would be a Python script from [Impacket](https://github.com/SecureAuthCorp/impacket) called [samrdump.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/samrdump.py).

[SMBMap](https://github.com/ShawnDEvans/smbmap) and [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) tools are also widely used and helpful for the enumeration of SMB services.

We should use more than two tools for enumeration as we don't know how the tool is programmed and we may not receive the complete information

[enum4linux-ng](https://github.com/cddmp/enum4linux-ng), which is based on an older tool, enum4linux. This tool automates many of the queries, but not all, and can return a large amount of information.

