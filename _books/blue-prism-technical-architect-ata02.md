---
layout: post
title: Blue Prism Technical Architect ATA02
author: HL
cover: null.png
date:   2017-01-01 12:00:00
topic: health
starred: false
score: 10
---

# Architect tips

## Virtualisation
 - type I hypervisor or II ? 
 	- type I: good because runs directly on hardware (VMWare vSphere)
 	- type II: guests share resources (VMware Fusion, VirtualBox)
 - Additional Considerations
 	- TBC
 - Suitability for virtualisation of each component
 	- Database: NO!
 	- App Server: Yes!
 	- Runtime Resource: Yes! virtualise to ensure availability
 	- Interactive Clients: Yes! should be identical to RR
 - Runtime Resources
 	- Consistency: virtualisation ensures common build which negates environment differences
 	- Security: hypervisor can be used to restrict users who can access
 	- Control: virtualising allows you to scale up flexibly
 - Interactive Clients
 	- Consistency: virtualisation ensures common build which negates environment differences
 	- Control: virtualising allows you to scale up flexibly
 	- Network: virtualising ensures client close to network, **which results in improved performance, especially for geographically separated Controllers**

 - Virtual Desktop Infrastructure (VDI) / Hosted Virtual Desktop (HVD)
  	- desktop machine on a separate physical server, usually a Type I Hypervisor
  	- dedicated system resources
 -	Hosted Shared Desktop (HSD) or Presentation Virtualisation ... Citrix!
 	- User presented with desktop and "published" applications
 	- resources are shared between all users
 	- applications streamed, which limits automation techniques to Surface Automation
 - Application Virtualisation
 	- applications streamed to user
 	- decoupling application from underlying OS and any dependencies on registry and file system
 	- care taken to check if application behaves same way as if installed on local machine, it might not!
 - Application Layering
 	- Application Virtualisation where the applications are no longer isolated from eah other
 - User Environment Managers (UEM)
  	- tools available which allow retention of user settings such as VMWare UE Manager
  - Persistent and Non-Persistent Desktops
  	- Non-Persistent: VM loaded from master image on each boot, usually coupled with a UEM
  	- Persistent: image persistent and user settings retained

 - which virtualizations need always surface automation
 - what are esx and desktop providers  
 - Minimal citrix esx version and problem with login agent
 
 - microsoft virtualizaion together with xenapp what are the caracteristic
 - learn exactly virtualization name provider with vdi persisten and not and characteristic
 - what happen when using virtualization hsd /presentatoin and when using layering

 - xenapp good for rr ?

## Connectivity and RDP
 - rdp good? 
 - how many types of wcf
 - how to design network and connectivity and remote control
 - characteristics connections which use remote
 - when was used .net remoting 


## active directory 
 - what happen with rr outside of active directory
 - active directory provide data or communication encryption? by default?
 - domain controller and forest AD = rr will be able to connect ? -> yes but not autenthificate

## SSO
 - what happen to communications when using sso
 - what give to Blue Prism when using sso?

## Encryption schema 
 - study in detail - where can be the key saved - app server use it - recommended setup  
 - When Using FQDN or short name 
 - encryption key and config file uses
 - encryption schema when, how is used and where can be saved?
 - all table about encryption and sso and port  
 
## Certificates
 - Certificates setup in login agent in config file?
 - when to exactly use ssl
 - I have to tell to what file when connecting using ssl
 - ipsx ? secure ip 
    
## Database
 - DB roles and ownership names
 - database near to ?
 - latency allowed where? 

## App Server and Login Agent
 - app server and login agent config file

## Unclassified

 - minimun requeriments hardware (minumun + Blue Prism )
 - snmp trap when to use and together with what?

# Per guide pages:
 - infrastructure 13 15 27 36, 37 **44! 45!** 48 49 50 53 56
 - table of connections and encryption 
 - **virtualization all pages! mostly the ones with the providers and characteristics (x ! ok)  11 ** 
 - database maintaining 3 **4!**
 - load balancer 10
 - login agent v5 12
 - login agent v6 8  9
 - installation 19 **47!** 65



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
