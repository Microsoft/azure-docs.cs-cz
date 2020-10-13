---
title: Konfigurace klienta NFS pro Azure NetApp Files | Microsoft Docs
description: Popisuje postup konfigurace klientů systému souborů NFS pro použití s Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: b2e597ff8fc761b66de6228063c471933a364144
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449653"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Konfigurace klienta NFS pro Azure NetApp Files

Konfigurace klienta NFS, která je popsaná v tomto článku, je součástí instalace, když [nakonfigurujete šifrování protokolu Kerberos nfsv 4.1](configure-kerberos-encryption.md) nebo [vytvoříte svazek se dvěma protokoly](create-volumes-dual-protocol.md). K použití s Azure NetApp Files je k dispozici celá řada distribucí systému Linux. Tento článek popisuje konfigurace pro dvě z běžně používaných prostředí: RHEL 8 a Ubuntu 18,04. 

Bez ohledu na to, jaký systém Linux používáte, jsou potřeba následující konfigurace:
* Nakonfigurujte klienta NTP, aby nedocházelo k problémům s časovým zkosením.
* Nakonfigurujte záznamy DNS klienta Linux pro překlad IP adres.  
    Tato konfigurace zahrnuje záznam "A" (dopředného) a záznam PTR (obrácený). 
* V části připojení k doméně vytvořte účet počítače v cílové službě Active Directory (který se vytvoří během příkazu spojení sféry). 
    > [!NOTE] 
    > `$SERVICEACCOUNT`Proměnná použitá v následujících příkazech by měla být uživatelským účtem s oprávněním nebo delegováním k vytvoření účtu počítače v cílové organizační jednotce.
* Umožněte klientovi připojit svazky systému souborů NFS a další relevantní nástroje pro monitorování.

## <a name="rhel-8-configuration"></a>Konfigurace RHEL 8 

1. Instalovat balíčky:   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. Konfigurace klienta NTP:  
    RHEL 8 používá standardně `chrony` .  Podle pokynů pro konfiguraci v [části použití sady Chrony ke konfiguraci programu NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

3. Připojte se k doméně služby Active Directory:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Konfigurace Ubuntu 
Tato část popisuje konfiguraci Ubuntu pro klienty NFS.  

### <a name="if-you-are-using-nfsv41-kerberos-encryption"></a>Pokud používáte šifrování protokolu Kerberos NFSv 4.1 

1. Instalovat balíčky:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Nakonfigurujte klienta NTP.  
    Ubuntu 18,04 používá `chrony` standardně.  Podle pokynů pro konfiguraci v [Ubuntu Bionic: použití Chrony ke konfiguraci NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Připojte se k Doména služby Active Directory:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

### <a name="if-you-are-using-dual-protocol"></a>Pokud používáte duální protokol  

1. Spuštěním následujícího příkazu upgradujte nainstalované balíčky:  
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    Příklad:   

    `base dc=hariscus,dc=com` `uri ldap://10.20.0.4:389/`
    `ldap_version 3`
    `rootbinddn cn=admin,cn=Users,dc=hariscus,dc=com`
    `pam_password ad`
 
2. Spusťte následující příkaz pro restartování a povolení služby:   
    `sudo systemctl restart nscd && sudo systemctl enable nscd`

V následujícím příkladu se dotazuje serveru služby AD LDAP z klienta LDAP Ubuntu na uživatele LDAP `ldapu1` :   

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="next-steps"></a>Další kroky  

* [Vytvoření svazku NFS pro službu Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Vytvoření svazku se dvěma protokoly pro Azure NetApp Files](create-volumes-dual-protocol.md)

