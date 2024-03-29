---
title: "Les différentes facettes de Windows Server Core 💠"
date: 2024-03-29
hero: winser.webp
description: Simple explication des différents rôles de Windows Server Core
theme: Toha
tags: ["Traduit", "Windows Server", "Microsoft"]
---

[*Read this article in english*](https://timothechau.vet/en/posts/technique/windows-server-features)

Windows Server, la version de Windows plébicitée par les entreprises pour leur infrastructure *on-premises* (le contraire du Cloud, c'est-à-dire une infrastructure hébergée localement). Elle propose différentes features que l'on ne retrouve pas dans les versions traditionnelles de Windows. Voici un petit tour d'horizon des rôles essentiels sur Windows Server Core.

</br>

# Active Directory (AD)

</br>

<p align="center">
  <img src="active-directory.webp" alt="Logo Active Directory" width="500"/>
</p>

</br>

Que serait une infra sans annuaire ? Active Directory sert exactement à ça : 
- Centraliser les utilisateurs
- Grouper les utilisateurs en rôles
- Gérer les droits d'accès aux ressources
- Gérer les ordinateurs et appareils connectés au réseau

Active Directory gère le *domaine* de l'entreprise, et les utilisateurs peuvent s'y connecter grâce à un identifiant.

Il intègre différents services, qui sont souvent confondus avec Active Directory lui-même, que voici.

</br>

### Active Directory Domain Services (ADDS)

LA killer feature d'Active Directory. ADDS est, en gros, le service de gestion d'annuaire, inclus dans AD :
- Propose les services d'authentification 
- Gère les autorisations sur le réseau
- Hiérarchise les composants

C'est d'ailleurs par ses fonctionnalités qu'on méprend souvent ADDS pour AD. Cependant, bien qu'essentiel, ADDS est une composante d'AD et non un service distinct.

</br>

### Active Directory Certificate Services (ADCS)

On parle ici de PKI (Public Key Infrastructure), c'est à dire la gestion des certificats. ADCS permet de :
- Générer des certificats
- Permettre, grâce à ces certificats, le chiffrement et la signature des données
- Authentifier des demandes

C'est avec un PKI comme ADCS qu'on peut avoir une vraie sécurité des communications

</br>

### Active Directory Federation Services (ADFS)

En permettant l'authentification unique (SSO pour Single Sign-On), ADFS permet à un utilisateur de se connecter à plusieurs services grâce à un seul compte. C'est un peu comme le bouton "Se connecter avec Google" qu'on voit un peu partout, mais à l'échelle d'une entreprise.
- Authentification sur des services internes et externes
- Agit comme un proxy entre AD et l'application

</br>

### Active Directory Lightweight Directory Services (ADLDS)

Très peu recommandé mais pourtant là, ADLDS est un annuaire léger sans les fonctionnalités d'ADDS. Donc oui, on peut l'utiliser à la place d'ADDS, mais vous avez très peu d'intérêt à le faire.

L'avantage est qu'il est utilisé par les applications pour accéder à un annuaire simple et rapide.

</br>

### Active Directory Rights Management Services (ADRMS)

Il s'agit d'un service de sécurité, notamment pour les droits. Il permet de :
- Protéger les données (Word, Outlook...)
- Permettre à certains utilisateurs d'avoir accès à un document confidentiel

</br>
</br>

# Fonctionnalités de serveur

Ça ne vous aura pas échappé que Windows Server est utilisé... pour des serveurs. Voici quelques fonctionnalités qui peuvent être ajoutées à un serveur Windows.

</br>

### Dynamic Host Configuration Protocol (DHCP)

À la base du protocole internet, DHCP permet de distribuer des adresses IP privées aux appareils connectés au réseau. C'est un service essentiel pour éviter les conflits d'adresses IP. 

Par exemple, si vous allez sur votre ligne de commande et que vous tapez 
```bash
# Windows
ipconfig /all
# Linux/Mac
ifconfig 
```

Vous aurez l'adresse IP du serveur DHCP. Bon, il faudra chercher un peu si vous avez 36 lampes connectées chez vous, mais je vous assure qu'elle y est ! 

Ce n'est donc pas nécessairement un service propre à Windows Server, mais dans une infrastructure *on-premises* il est essentiel.

</br>

### Domain Name System (DNS)

Les adresses IP c'est sympa, mais on est habitués à accéder à des serveurs en accédant à un nom de domaine. Un peu comme mon splendide domaine `timothechau.vet`, il est possible d'installer ce composant sur Windows Server pour avoir l'équivalent en interne.

Vous remarquerez peut-être que l'imprimante du bureau en réseau est connectée avec un nom domaine (par exemple imprimante.etage01.lan) et non avec son adresse IP (même si elle en a une). C'est grâce à DNS.

</br>

### File Services

File and Storage Services vous permet de partager des fichiers en interne. 

Dans le navigateur de fichiers, vous pouvez ajouter un emplacement réseau local.

Il ne sera plus disponible une fois que vous quitter le réseau.

Vous pouvez aussi mettre en place DFS (Distributed File System) pour avoir un partage de fichiers redondant.

</br>

### Print and Document Services

Permet de gérer les imprimantes. 

Contrairement à File Services, il connecte en réseau les imprimantes et non les dossiers et fichiers.

</br>

### Hyper-V Server

</br>

<p align="center">
  <img src="hyper-v.webp" alt="Capture d'écran d'une VM tournant avec Hyper-V" width="500"/>
  <p style="text-align: center;"><i><a href="https://share-danielfeau.com/es/introducci%C3%B3n-a-hyper-v-en-windows-10/">Exemple tiré du site share-danielfeau.com</a></i></p>
</p>

</br>

L'hyperviseur par excellence de Microsoft. Hyper-V permet de créer des machines virtuelles, et donc de virtualiser des serveurs. 

C'est comme si vous aviez un ordinateur dans votre ordinateur. 

Sachant que c'est la base des ressources cloud, ce service remontant à Windows Server 2008 n'a [pas été grandement mis à jour depuis 2019](https://www.altaro.com/hyper-v/end-of-hyper-v-server/). C'est pourquoi Microsoft pousse à l'adoption de ses services cloud Azure, une stratégie sur le long terme.

Vous avez de nombreuses alternatives, comme (le récemment racheté) VMware, ou encore VirtualBox pour une utilisation plus personnelle.

</br>

### Service Guardian hôte (HGS)

HGS permet de sécuriser les machines virtuelles. 

L'idée est de centraliser la gestion des VMs tournant sur Hyper-V et de les sécuriser avec des clés de chiffrement lors du démarrage.

Cela restreint largement l'accès aux VMs, mais permet d'en sécuriser le contenu au maximum.

</br>

### Remote Desktop Services (RDS)

RDS utilise le protocole RDP (Remote Desktop Protocol) pour permettre à un utilisateur de se connecter à un serveur à distance.

C'est très pratique si votre application tombe en panne et que vous voulez qu'un technicien y jette un œil.

</br>

### Remote Access

Remote Access permet de se connecter à un réseau interne depuis l'extérieur. 

Il existe plusieurs moyens de faire ça :
1. VPN (Virtual Private Network) : un tunnel chiffré qui utilise un protocole (OpenVPN, L2TP/IPsec, SSTP, Wireguard...)
2. DirectAccess : une alternative aux traditionnels VPNs, qui agit de manière + transparente
3. Web Application Proxy : un proxy pour les applications internes accédées depuis l'extérieur
4. Routing service : un service de routage pour connecter des réseaux entre eux (donc pas pour un collaborateur)

</br>

### Web Server Internet Information Services (IIS)

Le plus intéressant pour la fin : IIS, le serveur web de Microsoft, permet d'héberger des sites web.

[Selon Wappalyzer en 2024](https://www.wappalyzer.com/technologies/web-servers/), 4,8% des sites web tournent sur IIS, loin derrière les solutions open-source Apache et Nginx.

</br>
</br>

# Les autres rôles

Il en reste quelques-uns, que je vais lister ici

</br>

### Windows Server Update Services (WSUS)

WSUS permet de gérer les mises à jour de Windows Server. 

Quand on gère une grosse infrastructure, il est parfois compliqué de mettre à jour Windows à cette échelle. WSUS permet de centraliser les mises à jour, et de les déployer sur les serveurs.

</br>

### Device Health Attestation

Ce service permet de vérifier la santé des appareils connectés au réseau.

Il se concentre sur la sécurité (puces TPM), et les logs.

</br>

### Volume Activation Services (VAMT)

VAMT permet d'activer les licences Windows en volume. 

Si vous avez déjà bidouillé votre ordinateur personnel, vous avez probablement entendu parler de KMS (Key Management Service), qui permet d'activer Windows sans passer par le site de Microsoft. 

VAMT permet notamment d'attribuer les clés d'activation à des groupes d'ordinateurs (Windows, Office) en utilisant KMS.

</br>

### Windows Server Essentials Experience

C'est le dernier promis : Windows Server Essentials Experience est un ensemble de services pour les petites entreprises.

Un peu comme ADLDS avec AD, il est possible de se passer de Windows Server Essentials Experience, mais il est pratique pour les petites entreprises.

</br>
</br>

# Conclusion et remerciements

Vous avez maintenant compris que Windows Server n'est pas fait pour être installé que sur un seul ordinateur. 

Il est possible de gérer son infra *on-premises* de manière centralisée, et de tout gérer depuis un seul endroit. Mais tout l'intérêt d'Active Directory réside dans sa fédération des appareils dans un domaine.

Si un ordinateur avec Windows Server venait à tomber en panne, votre organisation a sûrement déjà prévu une bonne répartition des rôles. Ainsi, un autre serveur prendrait le relais, et votre infra ne serait pas impactée.

Merci à [Microsoft](https://learn.microsoft.com/en-us/windows-server/administration/server-core/server-core-roles-and-services) pour leur article listant tous les rôles (et pour Copilot qui m'aura bien servi).

L'initiative de cet article est largement inspirée d'une formation de [Thibault Gibard](https://akril.net) sur Windows Server, que je voulais retranscrire de mémoire dans un article sympa.

---

Bannière "Multi-tasking" générée par [DALL•E](https://labs.openai.com)