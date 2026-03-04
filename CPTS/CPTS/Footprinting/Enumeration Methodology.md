
The enumeration process is divided into three different levels:

1. Infrastructure-based enumeration
2. Host-based enumeration
3. OS-based enumeration

![[Pasted image 20260108092816.png]]


| Layer                  | Description                                                                                           | Information Categories                                                                        |
| ---------------------- | ----------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| 1. Internet Presence   | Identification of internet presence and externally accessible infrastructure.                         | Domains, Subdomains, vHosts, ASN, Netblocks, IP Addresses, Cloud Instances, Security Measuers |
| 2. Gateway             | Identify the possible security measures to protect the company's external and internal infrastructure | Firewalls, DMZ, IPS/IDS, EDR, Proxies, NAC, Network Segmentation, VPN, Cloudflare             |
| 3. Accessible Services | Identify accessible interfaces and services that are hosted externally or internally.                 | Service Type, Functionality, Configuration, Port, Version, Interface                          |
| 4. Processes           | Identify the internal processes, sources, and destinations associated with the services.              | PID, Processed Data, Tasks, Source, Destination                                               |
| 5. Privileges          | Identification of the internal permissions and privileges to the accessible services.                 | Groups, Users, Permissions, Restrictions, Environment                                         |
| 6. OS Setup            | Identification of the internal components and systems setup                                           | OS type, Patch level, Network Config, OS Environment, Configuration files, sensitive.         |

*For the same of simplicity, the human aspect and the information that can be obtained by employees using OSINT is not considered from the Internet Presence*

#### Layer No. 1: Internet Presence

The goal of this layer is to identify all possible target systems and interfaces that can be tested. 

#### Layer No. 2: Gateway

The goal is to understand what we are dealing with and what we have to watch out for.

#### Layer No. 3: Accessible Services

The layer aims to understand the reason and functionality of the target system and gain the necessary knowledge to communicate with it and exploit it for our purposes effectively. 

#### Layer No. 4: Processes

The goal here is to understand these factors and identify the dependencies between them.

#### Layer No. 5: Privileges

Each service runs through a specific user in a particular group with permissions and privileges defined by the administrator of the system. 

It is crucial to identify these privileges as they can provide us with functions that admins overlook, and understand what is and is not possible with them.

#### Layer No. 6: OS Setup

The goal here is to see how the admins manage the systems and what sensitive internal information we can glean from them. 


