---
title: "What Windows Server Core is capable of 💠"
date: 2024-04-02
hero: winser.webp
description: An overview of Windows Server Core roles and features
theme: Toha
---


Windows Server is the version of Windows favoured by businesses for their on-premises infrastructure. It offers various solutions, or *roles*, that are not (always) found in the home versions of Windows. This article aims to overview essential roles on Windows Server Core.

</br>

# Active Directory (AD)

</br>

<p align="center">
  {{< img src="/posts/technique/windows-server-features/active-directory.webp" align="center" alt="Active Directory logo" width="500" >}}
</p>

</br>

What would an infrastructure be without a directory? Active Directory does exactly that: 
- Centralise users
- Group users and assign roles
- Manage access rights to resources
- Manage computers and devices connected to the network

Active Directory manages the company's *domain*, and users can connect to it using credentials.

It incorporates the following services, which are often confused with Active Directory itself.

</br>

### Active Directory Domain Services (ADDS)

THE killer feature of Active Directory. ADDS is the main directory manager :
- Provides authentication services 
- Manages network authorisations
- Prioritises components
- Replicates data

This is known as a Domain Controller (DC).

It is because of its functionality that ADDS is often mistaken for AD. However, although essential, ADDS is a component of AD and not a separate service.

</br>

### Active Directory Lightweight Directory Services (ADLDS)

ADLDS is a lighter version of ADDS. It doesn't have ADDS' features, but is simpler and quicker to access. It isn't a Domain Controller (DC), as it is a lightweight standalone directory. 

It is generally used in applications, as it provides a quick and simple directory. 

</br>

### Active Directory Certificate Services (ADCS)

We are talking here about PKI (Public Key Infrastructure), which is certificate management. ADCS enables you to :
- Generate certificates
- Use these certificates to encrypt and sign data
- Authenticate requests

Without ADCS, the entire communication system wouldn't be secured in a Windows infrastructure.

</br>

### Active Directory Federation Services (ADFS)

By enabling Single Sign-On (SSO), ADFS allows an user to connect to several services using a single account. It's a bit like the "Sign in with Google" button you see on websites, but on an enterprise scale.
- Single sign-on to internal and external services
- Acts as a proxy between AD and the application

</br>

### Active Directory Rights Management Services (ADRMS)

Introduced with Windows Server 2008 R2, AD RMS protects sensitive information. Even if this data is shared outside the domain (for example, an Excel financial report or a business strategy PowerPoint), this role enables administrators to set up access restrictions to these documents.

AD RMS is a right-granting security service. It enables you to:
- Protect data by setting up access authorisations (Word, Outlook, etc.)
- Set up document templates, like Confidential or Secret
- Continuously protect documents in transit over the network using security tools (firewalls, access controls, etc.)

</br>
</br>

# Server features

You might have noticed that Windows Server is often used... for servers. Here are a few roles that can be added to a Windows server.

</br>

<p align="center">
  {{< img src="/posts/technique/windows-server-features/server-manager.webp" align="center" alt="Server Manager screenshot" width="500" >}}
  <p style="text-align: center;"><i>Server Manager screenshot to add roles to a Windows Server <a href="https://www.server-world.info/en/note?os=Windows_Server_2022&p=active_directory&f=1">(source)</a></i></p>
</p>

</br>

### Dynamic Host Configuration Protocol (DHCP)

Core service of the Internet Protocol (IP), DHCP is used to assign private IP addresses to devices connected to a private network. It is an essential service for avoiding IP address conflicts. 

To illustrate my point, if you open your command line and type 
```bash
# Windows
ipconfig /all
# Linux/Mac
ifconfig 
```

You'll see all your 24 smartphones IP addresses connected to your Wi-Fi, as well as the IP address of the DHCP server.

So it's not a service specific to Windows Server, but mandatory for an on-premises infrastructure.

</br>

### Domain Name System (DNS)

IP addresses are essential, but it's better addressing servers with a domain name. If you like my splendid `timothechau.vet` domain, it's used to refers to the server's public IP address that hosts my website. You can install the DNS role on Windows Server as well, to assign domain names for machines and servers in your network.

There might be a printer connected to your network that uses a domain name (for example `printer.floor01.lan`). Even if it has a private IP assigned by the DHCP server, it can also have a domain name, for an easy access.

</br>

### File Services

File and Storage Services lets you share folders and files in your network. 

In the file browser, you may have a few folders in the local network. This is what File Services does.

If you log-out the network, these folders will no longer be accessible.

You can also set up DFS (Distributed File System) to have redundant file sharing, and decrease recovery time in the event of a server failure.

</br>

### Print and Document Services

Used to manage printers. 

Unlike File Services, it connects printers to the network, not folders and files. It can also uses the DNS role to assign a domain name for printers.

</br>

### Hyper-V Server

</br>

<p align="center">
  {{< img src="/posts/technique/windows-server-features/hyper-v.webp" align="center" alt="Screenshot of a VM running on Hyper-V" width="500" >}}
  <p style="text-align: center;"><i>A VM inside a VM inside an Hyper-V host <a href="https://share-danielfeau.com/es/introducci%C3%B3n-a-hyper-v-en-windows-10/">(source)</a></i></p>
</p>

</br>

Microsoft's hypervisor par excellence. Hyper-V lets you create virtual machines (VM). 

It's like having a virtual computer inside your computer. 

The VM is the basis for cloud resources. This Hyper-V role, added with Windows Server 2008, has [not been greatly updated since 2019](https://www.altaro.com/hyper-v/end-of-hyper-v-server/). Indeed, Microsoft is pushing for the adoption of its Azure cloud services, a long-term strategy.

You have many alternatives, such as (the recently bought) VMware, or VirtualBox for more personal use.

</br>

### Host Guardian Service (HGS)

HGS is used to secure Hyper-V virtual machines. Here's how it works:

* The organisation uses generation 2 VMs known as *shielded*, because they 
  1. are BitLocker-encrypted
  2. virtualize a Trusted Platform Module (TPM) chip
  3. run on trusted hosts 

</br>

* HGS ensures the reliability of Hyper-V hosts for starting up *shielded* VMs
  1. The HGS role is assigned to dedicated servers
  2. The Hyper-V hosts are registered in the HGS cluster 
  3. HGS ensures that hosts are trusted (TPM 2.0 chip, Just Enough Administration...)

</br>

<p align="center">
  {{< img src="/posts/technique/windows-server-features/hgs.webp" align="center" alt="Diagram with 3 HGS nodes, 3 domain controller nodes and Hyper-V hosts" width="500" >}}
  <p style="text-align: center;"><i>Diagram showcasing how HGS ensures Hyper-V hosts safety</i></p>
</p>

</br>

### Remote Desktop Services (RDS)

Windows Server servers with this role can use the Remote Desktop Services (RDS) platform to serve applications (RemoteApps) and virtual desktops. It enables users (inside or outside the network) to connect remotely to a server. This is Microsoft's VDI (Virtual Desktop Infrastructure) solution.

RDS offers these services:
- Remote Desktop Session Host: for hosting sessions; it uses the Remote Desktop Protocol (RDP) to do so
- Remote Desktop Web Access: for accessing intranet applications via a browser
- Remote Desktop Connection Broker: for redirecting connections
- Remote Desktop Gateway: to secure connections
- Remote Desktop Licensing: for managing licences

</br>

### Remote Access

Remote Access lets you connect to an internal network from outside. 

There are multiple ways to do this :
1. VPN (Virtual Private Network) : an encrypted tunnel using a protocol with which you are probably familiar (OpenVPN, L2TP/IPsec, SSTP, Wireguard, etc.)
2. DirectAccess : an alternative to traditional VPNs that works more transparently
3. Web Application Proxy : a proxy for internal web applications accessed from the outside
4. Routing service : a routing service to connect networks together (not for your average user)

</br>

### Web Server Internet Information Services (IIS)

Last but not least: IIS, Microsoft's web server, is used to host websites.

[According to Wappalyzer in 2024](https://www.wappalyzer.com/technologies/web-servers/), 4.8% of websites run on IIS, far behind the open-source solutions Apache and Nginx.

</br>
</br>

# Some other roles

</br>

### Windows Server Update Services (WSUS)

WSUS manages Windows Server updates. 

When you manage a large infrastructure, it can be hard to update Windows on this scale. WSUS makes it possible to centralise updates and install them on several servers.

</br>

### Device Health Attestation

This service allows you to check the health of devices connected to the network.

It focuses on security (TPM chips) and logs. The *assessment* function allows you to check the infrastructure and its performance.

</br>

<p align="center">
  {{< img src="/posts/technique/windows-server-features/device-health-assessment.webp" alt="Device Health assessment" align="center" width="500" >}}
  <p style="text-align: center;"><i>A <a href="https://www.oscc.be/sccm/configmgr/tp/intune/hybrid/aad/azure/windows%2010/TP-1706-DHA">Tom Degreef</a> assessment</i></p>
</p>

</br>

### Volume Activation Services (VAMT)

VAMT lets you activate Windows licences at scale. 

If you've ever tinkered with your personal computer, you've probably heard of KMS (Key Management Service), which lets you activate Windows without going through the Microsoft site. 

In particular, VAMT validate activation keys (Windows, Office) to pools of computers using KMS.

</br>

### Windows Server Essentials Experience

Lastly, Windows Server Essentials Experience is a set of basic services for small businesses.

A bit like ADLDS is to AD, you can go without Windows Server Essentials Experience. Rather than having all the features, this role groups together the essential ones. This feature is reserved for infrastructures with a maximum of
- 25 users
- 50 devices

</br>
</br>

# Conclusion

By now you've realised that Windows Server isn't designed to be installed on just one computer, and that no two Windows Server computers look alike.

It is possible to centralize management for your *on-premises* infrastructure, or use every role in just one server. But the whole point of Active Directory is to federate devices into a domain.

If a Windows Server computer were to fail, an organisation can distribute roles evenly, to keep its infrastructure resilient. In this way, fail-over to healthy servers would be automatic.

Thanks to [Microsoft](https://learn.microsoft.com/en-us/windows-server/administration/server-core/server-core-roles-and-services) for their article listing all the roles (and to Copilot, which came in very handy).

The initiative for this article was largely inspired by a Windows Server course given by [Thibault Gibard](https://akril.net), which I wanted to write down from memory into an article.

---

"Multi-tasking" banner generated by [DALL•E](https://labs.openai.com)
Translated using [DeepL](https://www.deepl.com/translator)