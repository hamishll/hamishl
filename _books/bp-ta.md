---
layout: post
title: Blue Prism Technical Architect
author: Exam Notes
cover: null.png
date:   2017-01-01 12:00:00
topic: business
starred: false
score: 11
---

# Virtualisation
1. Hypervisors 
    - type I: good because runs directly on hardware (VMWare vSphere)
    - type II: guests share resources (VMware Fusion, VirtualBox)
2. Suitability for virtualisation of each component
    - Database: NO!
    - App Server: Yes!
    - Runtime Resource: Yes! virtualise to ensure availability
    - Interactive Clients: Yes! should be identical to RR
3. Runtime Resources
    - Consistency: virtualisation ensures common build which negates environment differences
    - Security: hypervisor can be used to restrict users who can access
    - Control: virtualising allows you to scale up flexibly
4. Interactive Clients
    - Consistency: virtualisation ensures common build which negates environment differences
    - Control: virtualising allows you to scale up flexibly
    - Network: virtualising ensures client close to network, **which results in improved performance, especially for geographically separated Controllers**

5. User Environment Managers (UEM)
    - tools available which allow retention of user settings such as VMWare UE Manager

## Virtualisation Choices

1. Persistent VDI / HVD [Citrix XenDesktop (persistent), VMWare Horizon View (dedicated)] 
    - image persistent and user settings retained
    - Most suitable choice for RR and IC however consider patching and maintenance overhead
    - *Characteristics:*
        - ***OS Image is not refreshed***
        - ***User assigned same VM on logon***
        - *User personalisation settings retained*

2. Non-Persistent VDI / HVD [Citrix XenDesktop (non-persistent), VMWare Horizon View (floating)]
    - VM loaded from master image on each boot, usually coupled with a UEM
    - Consider how to maintain config changes, refresh of image may cause issues
    - *Characteristics:*
        - ***OS image may be refreshed on boot / scheduled basis***
        - ***User is not guaranteed to be allocated same VM each time***
        - *User Personalisation is lost unless coupled with UEM*
        - *Use of Login Agent is required*

3. Hosted Shared Desktop (HSD) or Presentation Virtualisation [Citrix XenApp]
    - User presented with desktop and "published" applications
    - resources are shared between all users
    - **applications streamed, which limits automation techniques to Surface Automation**
    - Q: which virtualizations need always surface automation  A: Hosted Shared Desktop / Presentation Virtualisation
    - not suitable for Runtime Resource or Login Agent
    - IC can't be published application
    - *Characteristics:*
        - ***User is allocated a "published" desktop or application from multi-tenant server***
        - ***System resources are shared***
        - *Sessions are not persistent*

4. Application Virtualisation and Layering [Citrix XenApp, VMWare App Volumes]
    - Not suitable for RR or Login Agent
    - applications streamed to user
    - decoupling application from underlying OS and any dependencies on registry and file system
    - care taken to check if application behaves same way as if installed on local machine, it might not!
    - Application Layering where the applications are no longer isolated from eah other
    - Consider how user customeisations required for a process will be delivered
    - *Characteristics:*
        - *Application is isolated from core image of VM, generally coupled with non-persistent VM*
        - *Application and UEM generally delivered via separate technologies*


    
 - what are esx and desktop providers  
 - Minimal citrix esx version and problem with login agent
 - microsoft virtualizaion together with xenapp what are the caracteristic
 - learn exactly virtualization name provider with vdi persisten and not and characteristic
 - what happen when using virtualization hsd /presentatoin and when using layering
 - xenapp good for rr ? --> NO


# ---------------------------------------------------------------------------

# Communications and Encryption
<!--
|===Communication===|=====Purpose====== |===Type=== | =====Encryption=============|
|-------------------|-------------------|-----------|-------------------------|
| IC -> AS         | Operating comms   | WCF       | Native, dependent on AD |
| AS -> RR         | Operating comms   | WCF       | Native, dependent on AD |
| AS -> RR         | Instructions      | TCP       | Certificates (opt)      |
| IC -> RR         | Instructions      | TCP       | Certificates (opt)      |
| AS -> DB         | Write to DB       | TCP       | Certificates (opt)      |
| RR -> Bus Apps   | Process operations| Various   | Various                 |
| User -> IC       | Controllers       | Various   | Various                 |
-->

1. TCP
    - used to give instructions (which is followed by WCF communication between RR and AS)
    - can be secured with certificates

2. WCF
    - used for operating communications, such as seeing status and retrieving process to be ran
    - encrypted natively, security dependent on AD
    - App Server and RR must be configured to `Use Secure Connection`
    - Legacy installations can use .NET remoting instead of WCF, where security is handled by Microsoft's SSPI
    - Types:
        - WCF: SOAP with Message Encryption and Windows Authentication
        - WCF: SOAP with Transport Encryption and Windows Authentication
        - ...

1. Default ports:
    - App Server listens for TCP traffic on 8199
    - Runtime Resource listens for TCP traffic on 8181

1. Enforcing certificates and authentication
    - Enforce certificates by selecting `Require secure inbound instructional connections`
    - Require user login or SSO by disabling `Allow anonymous public Runtime Resources`

 - how many types of wcf
 - how to design network and connectivity and remote control
 - characteristics connections which use remote

# Encryption schema 
 - study in detail - where can be the key saved - app server use it - recommended setup  
 - When Using FQDN (fully qualified domain name) or short name 
 - encryption key and config file uses
 - encryption schema when, how is used and where can be saved?
 - all table about encryption and sso and port  


# Certificates
 - To require certificates for DB, use `TrustServerCertificate=true, Encrypt=true`
 - Certificates setup in login agent in config file?
 - when to exactly use ssl
 - I have to tell to what file when connecting using ssl  --> Thumbprint?
 - ipsx ? secure ip 

# ---------------------------------------------------------------------------

# Active Directory

1. Benefits
 - A common AD network allows native encryption of Blue Prism communications
 - Access rights can be managed centrally across groups of users
 - User access can be configured to use SSO 

1. Benefits of AD for Runtime Resources
 - Enforces security policies
 - Enfocres GPO policies
 - Provides auditability
 - Simplifies access

## Process for AD

1. Select to use AD authentication when creating your database through the wizard
 - You will need to select the Domain to be used
 - You will need to select the Security Groups to be used

2. Configure the AD Security Groups
 - eg: Devs, Testers, Release Managers, Controllers

3. Associate each Blue Prism role with each AD Security Group
 - eg: Devs, Testers, Release Managers, Controllers


 - what happen with rr outside of active directory
 - active directory provide data or communication encryption? by default?
 - domain controller and forest AD = rr will be able to connect ? -> yes but not authenticate


# SSO
 - what happen to communications when using sso  --> Encrypted?
 - what give to Blue Prism when using sso?

# ---------------------------------------------------------------------------

# Provisioning a Database

1. Guidance
    - Set appropriate initial file sizes
    - Set appropriate growth limits
    - Enable Instant File Initializations
    - Turn off auto-shrink
    - Turn on auto-growth

2. Roles
    - All blue prism roles prefixed with "bpa_", eg: bpa_ExecuteSP_DataSource_bpSystem
    - Creating, Configuring and Upgrading database permissions are not defined by blue prism. The following roles are required by your DBA, with `sysadmin` or:
    - `dbowner` and `dbcreator` = Create Database
    - `dbowner` = Configure Database
    - `dbowner` = Upgrade Database

1. Virtualisation
    - Typically not appropriate, as resources are usually contended for

1. Communications
 - **Database should be near to App Server as latency is critical due to volume of operations**
 - latency allowed where? 
   

 
# Maintaining a Database

1. Avoid Shrink Operations 
    - as may cause fragmentation

2. Backups
    - Blue Prism supports both *Simple* and *Full* SQL recovery
    - If using *Full*, take regular transaction log backups to provide free space within the log files

3. General Maintenance should be used to manage:
    - available disk space
    - disk fragmentation which may reduce performance, use **ALTER INDEX** to rebuild
    - paging file should be appropriately sized
    - event viewer should be checked for errors 
    - verify backups to ensure they have been completed without error
    - verify data integrity with **DBCC CHECKDB**
    - ensure statistics up-to-date by turning on **Auto Create Statistics** and **Auto Update Statistics**

4. In-product maintenance
    - Archiving can be scheduled within product
    - Command line can be used instead: 
        - SSO:  `AutomateC.exe /archive /sso /age 3m`
        - Native: `AutomateC.exe /archive /user "user""pass" /age 3m`
        - optionally add `/delete`



# ---------------------------------------------------------------------------

# Remote Access Tools
1. Not suitable as:
    - the OS is aware it is being connected to, which can interrupt operations
    - when a user authenticates it disconnects other users

# Load Balancing

# App Server and Login Agent
 - app server and login agent config file


# Login Agent

1. Mandatory Security Policies
 - Do not require CTRL + ALT + DEL
 - Do not require accepting on-screen message
 - Do not require a lock screen

2. Installation
 - Specify Connection Name, i.e. 'BluePrism-Production'
 - Require SSO or User arguments if anonymous logins are disabled
 - Config file can have arguments:
-      port: 8181
-      sslcert: {Thumbprint}
-      user: {username}, {password}
-      sso
 - Add RR to Startup so handover occurs once logged in
 - Retrieve credential *Windows Logoi: HOSTNAME*
 - Trigger Login Agent in schedules

# Unclassified

 - minimun requeriments hardware (minumun + Blue Prism) ... 2GB RAM for RR and IC, 4GB RAM for App Server
 - snmp trap when to use and together with what?


# Per guide pages:
 - infrastructure 13 15 27 36, 37 **44! 45!** 48 49 50 53 56
 - [DONE] table of connections and encryption 
 - [DONE] **virtualization all pages! mostly the ones with the providers and characteristics (x ! ok)  11 ** 
 - [DONE] database maintaining 3 **4!**
 - load balancer 10
 - [DONE] login agent v6 8  9
 - installation 19 **47!** 65


# Questions:

1. What should be considered when configuring high availability and disaster recovery scenarios for availability of Business Processes managed by Blue Prism? (Select 2)
 - [CORRECT] Resource pools and Active Queues can be used to distribute work to Runtime Resources which are online
 - [CORRECT] Using a load balancer and 2 application servers will ensure high availability of an automated process
 - [WRONG] The Runtime Resources are "stateful” devices, therefore any failure or maintenance event should also be taken into account in the process design and scheduling methodology
 - [WRONG] Use the setting within a Blue Prism Process to force a process to fail over to an alternative Runtime Resource in the event of a failure


# ---------------------------------------------------------------------------

# Installer tips:
Basically study the installer guide very well
db sysdamin when to use ? 
roles in db - datawrite datareader- dbcreator - dbupgrade - config
a lot of certifcate setup question - and option how to use in each component
order of installation script of DB
permision sql to write , update,
connectivity between component, options and how is done with certificate 
encryp database
encrypt each component
have clear what is database per environment and different combination with app server and environment
database per environment
when is written the asociation with the domain in the setup - before after of bpa roles?
what option can be done in tab of bpserver.exe
what happen in system configuration if allow anonymous or require secure connections is activated
snmp trap what is useful 
BPDiagnostics.exe what is used
why disable option of start a personal runtime reosrce .. in setiings
