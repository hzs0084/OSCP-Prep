NFS is network file systems but it is different from SMB as it is used only between Linux and Unix systems. 

NFS is based on the Open Network Computing Remote Procedure Call exposed on TCP and UDP ports 111 and uses External Data Representation

The NFS protocol has no mechanism for authentication or authorization. 

NFS should only be used with the authentication method in trusted networks. The authentication is done via UNIX UID/GID and group memberships. 

### Default configuration

`/etc/exports` - contains a table of physical file systems on an NFS server that is accessible by the clients. 

The default exports file contains examples of configuring NFS shares. 

First, the folder is specified and made available to others, and then the rights they have on this NFS share are connected to a host or a subnet. 

|**Option**|**Description**|
|---|---|
|`rw`|Read and write permissions.|
|`ro`|Read only permissions.|
|`sync`|Synchronous data transfer. (A bit slower)|
|`async`|Asynchronous data transfer. (A bit faster)|
|`secure`|Ports above 1024 will not be used.|
|`insecure`|Ports above 1024 will be used.|
|`no_subtree_check`|This option disables the checking of subdirectory trees.|
|`root_squash`|Assigns all permissions to files of root UID/GID 0 to the UID/GID of anonymous, which prevents `root` from accessing files on an NFS mount.|
### Dangerous Settings

|**Option**|**Description**|
|---|---|
|`rw`|Read and write permissions.|
|`insecure`|Ports above 1024 will be used.|
|`nohide`|If another file system was mounted below an exported directory, this directory is exported by its own exports entry.|
|`no_root_squash`|All files created by root are kept with the UID/GID 0.|

TCP 111 and 2049 are essential to NFS

### Show Available NFS Shares

```shell-session
hzs0084@htb[/htb]$ showmount -e 10.129.14.128

Export list for 10.129.14.128:
/mnt/nfs 10.129.14.0/24
```

### Mounting NFS Share

```shell-session
hzs0084@htb[/htb]$ mkdir target-NFS
hzs0084@htb[/htb]$ sudo mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock
hzs0084@htb[/htb]$ cd target-NFS
hzs0084@htb[/htb]$ tree .

.
└── mnt
    └── nfs
        ├── id_rsa
        ├── id_rsa.pub
        └── nfs.share

2 directories, 3 files
```

Use this as an opportunity to access the rights and the usernames and groups to whom the shown and viewable files belong. 

Once we have the usernames, group names, UIDs, and GUIDs, we can create them on our system and adapt them to the NFS share to view and modify the files. 

## Challenge Walkthrough

For the first flag, I was able to mount the share directly under the local target directly. \

For enumeration, I used nmap scripts through NSE

Found the first flag when i mounted the nfs folder

I couldn't mount the second folder and ran into a permission issue so I just defined the target ip and mounted the entire dir 

that worked and i found the second flag

