
It was created to monitor network devices. In addition to that it can also be used to handle configuration tasks and change settings remotely. 

Some examples of SNMP enabled hardware are routers, switches, servers, IoT devices and many more. 

SNMP transmits control commands using agents over UDP port `161`

If a specific event occurs on the server side an SNMP `traps` are sent to client without explicitly requesting it. These data packets are sent from the SNMP server over UDP port `162`

For the SNMP client and server to exchange the respective values, the available SNMP objects must have unique addresses known on both sides. 

%% How does the initial exchange becomes known? How are the address spaces generated and then the type of communication takes places to ensure that the address isn't spoofed? %%

`Management Information Base (MIB)` was created to ensure that SNMP can work across different manufactures with different client-server combinations. 

MIB is an independent format for storing information. SNMP objects are listed in a tree hierarchy and is stored as a text file in MIB. 

MIBs do not contain data but they explain where to find which information and what is looks like, which returns values for the specific OID, or which data type is used. 

These MIB files are written in `Abstract Syntax Notation One (ASN.1)`

It contains at least one `Object Identifier (OID)` which in addition to the necessary unique address and a name also provides information about the type, access rights, and a description of the respective object. 

## OID 

OID represents a node in a hierarchical namespace. The longer the chain, the more specific information. Many nodes in the OID tree contain nothing but references to those below them. 

## SNMPv1

SNMPv1 is still in use in many small networks. It supports retrieving information from network devices, allow configuration changes, provides traps, which are notifications of events. 

SNMPv1 has `no built in authentication` and also `does not support encryption`

## SNMPv2

Two different versions, `v2c` and `c` means community based SNMP. 

In terms of security it has additional functions but communication is still in plain text and no built-in encryption. 

## SNMPv3


Security features like `authentiction` and `encryption` (via `pre-shared key`) of the data. Complexity of the protocol has also significantly increased.

Community strings can be seen as passwords that are used to determine whether the requested information can be viewed or not. 

## Default Configuration

The default configuration of the SNMP daemon defines the basic settings for the service, which include the IP addresses, ports, MIB, OIDs, authentication, and community strings.

```shell-session

hzs0084@htb[/htb]$ cat /etc/snmp/snmpd.conf | grep -v "#" | sed -r '/^\s*$/d' 

sysLocation Sitting on the Dock of the Bay 
sysContact Me <me@example.org> 
sysServices 72 
master agentx 
agentaddress 127.0.0.1,[::1] 
view systemonly included .1.3.6.1.2.1.1 
view systemonly included .1.3.6.1.2.1.25.1 
rocommunity public default -V systemonly 
rocommunity6 public default -V systemonly 
rouser authPrivUser authpriv -V systemonly

```

## Dangerous Settings

Some dangerous settings that the administrator can make with SNMP are:

|**Settings**|**Description**|
|---|---|
|`rwuser noauth`|Provides access to the full OID tree without authentication.|
|`rwcommunity <community string> <IPv4 address>`|Provides access to the full OID tree regardless of where the requests were sent from.|
|`rwcommunity6 <community string> <IPv6 address>`|Same access as with `rwcommunity` with the difference of using IPv6.|

## Footprinting the Service

For footprinting SNMP, we can use tools like `snmpwalk`, `onesixtyone`, and `braa`. `Snmpwalk` is used to query the OIDs with their information. `Onesixtyone` can be used to brute-force the names of the community strings since they can be named arbitrarily by the administrator. Since these community strings can be bound to any source, identifying the existing community strings can take quite some time.

#### SNMPwalk

`hzs0084@htb[/htb]$ snmpwalk -v2c -c public 10.129.14.128`


Once we know the community string and the SNMP service that does not require authentication (versions 1, 2c), we can query internal system information

If we do not know the community string, we can use `onesixtyone` and `SecLists` wordlists to identify these community strings.


#### OneSixtyOne

`hzs0084@htb[/htb]$ sudo apt install onesixtyone 

`hzs0084@htb[/htb]$ onesixtyone -c /opt/useful/seclists/Discovery/SNMP/snmp.txt 

`10.129.14.128 Scanning 1 hosts, 3220 communities 10.129.14.128 [public] Linux htb 5.11.0-37-generic #41~20.04.2-Ubuntu SMP Fri Sep 24 09:06:38 UTC 2021 x86_64`

Using [crunch](https://secf00tprint.github.io/blog/passwords/crunch/advanced/en) to create custom wordlists.

![[Pasted image 20260327152834.png]]

## Walkthrough

SNMPWalk command give me all the information and the flag


