---
title: Konfigurace šifrování protokolu Kerberos NFSv 4.1 pro Azure NetApp Files | Microsoft Docs
description: V této části najdete popis postupu konfigurace šifrování protokolu Kerberos NFSv 4.1 pro Azure NetApp Files a vlivu na výkon.
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
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: 6ff87d046c60f588e133010895ec3e7ce08cb71f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740558"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Konfigurace šifrování Kerberos NFSv4.1 pro Azure NetApp Files

Azure NetApp Files podporuje šifrování klienta NFS v režimech Kerberos (krb5, Krb5i a krb5p) pomocí šifrování AES-256. Tento článek popisuje požadované konfigurace pro použití svazku NFSv 4.1 s šifrováním protokolu Kerberos.

## <a name="requirements"></a>Požadavky

Pro šifrování klientů NFSv 4.1 se platí následující požadavky: 

* Připojení Active Directory Domain Services (služba AD DS) pro usnadnění lístku protokolu Kerberos 
* Vytvoření záznamu DNS a/PTR pro IP adresy klienta i serveru Azure NetApp Files NFS
* Klient Linux  
    Tento článek poskytuje pokyny pro klienty RHEL a Ubuntu.  Ostatní klienti budou pracovat s podobnými kroky konfigurace. 
* Přístup k serveru NTP  
    Můžete použít jeden z běžně používaných řadičů domény Doména služby Active Directory Controller (AD DC).

## <a name="create-an-nfs-kerberos-volume"></a>Vytvoření svazku protokolu Kerberos pro systém souborů NFS

1.  Pokud chcete vytvořit svazek NFSv 4.1, postupujte podle kroků v části [vytvoření svazku NFS pro Azure NetApp Files](azure-netapp-files-create-volumes.md) .   

    Na stránce vytvořit svazek nastavte verzi NFS na **nfsv 4.1** a nastavte protokol Kerberos na **povoleno**.

    > [!IMPORTANT] 
    > Po vytvoření svazku již nelze změnit výběr povolení protokolu Kerberos.

    ![Vytvoření svazku protokolu Kerberos NFSv 4.1](../media/azure-netapp-files/create-kerberos-volume.png)  

2. Vyberte možnost **Exportovat zásadu** , která bude odpovídat požadované úrovni přístupu a možnosti zabezpečení (Kerberos 5, Kerberos 5i nebo Kerberos 5p) pro daný svazek.   

    Dopad na výkon protokolu Kerberos najdete v tématu [dopad na výkon protokolu Kerberos v nfsv 4.1](#kerberos_performance).  

    Můžete také upravit metody zabezpečení protokolu Kerberos pro daný svazek kliknutím na Exportovat zásadu v navigačním podokně Azure NetApp Files.

3.  Kliknutím na tlačítko **zkontrolovat + vytvořit** vytvořte svazek nfsv 4.1.

## <a name="configure-the-azure-portal"></a>Nakonfigurovat Azure Portal 

1.  Postupujte podle pokynů v tématu [vytvoření připojení ke službě Active Directory](create-active-directory-connections.md).  

    Protokol Kerberos vyžaduje, abyste ve službě Active Directory vytvořili aspoň jeden účet počítače. Informace o účtu, které poskytnete, se používají k vytváření účtů pro svazky Kerberos protokolu SMB *i* nfsv 4.1. Tento počítač je vytvořen automaticky během vytváření svazku.

2.  V části **sféra protokolu Kerberos** zadejte **název serveru AD** a **IP** adresu služby KDC.

    Server AD a IP adresa služby KDC můžou být na stejném serveru. Tyto informace slouží k vytvoření účtu počítače hlavního názvu služby, který používá Azure NetApp Files. Po vytvoření účtu počítače Azure NetApp Files použije záznamy DNS serveru k vyhledání dalších serverů služby KDC podle potřeby. 

    ![Sféra protokolu Kerberos](../media/azure-netapp-files/kerberos-realm.png)
 
3.  Kliknutím na **připojit** uložte konfiguraci.

## <a name="configure-active-directory-connection"></a>Konfigurace připojení ke službě Active Directory 

Konfigurace protokolu Kerberos pro NFSv 4.1 vytvoří dva účty počítačů ve službě Active Directory:
* Účet počítače pro sdílené složky SMB
* Počítačový účet pro NFSv 4.1 – Tento účet můžete identifikovat pomocí předpony `NFS-` . 

Po vytvoření prvního svazku protokolu Kerberos NFSv 4.1 nastavte typ šifrování pro účet počítače pomocí následujícího příkazu PowerShellu:

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>Konfigurace klienta NFS 

Postupujte podle pokynů v části [Konfigurace klienta NFS pro Azure NetApp Files](configure-nfs-clients.md) ke konfiguraci klienta NFS.  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>Připojení svazku systému souborů NFS protokolu Kerberos

1. Na stránce **svazky** vyberte svazek NFS, který chcete připojit.

2. Pokud chcete zobrazit pokyny, vyberte ze svazku **pokyny k připojení** .

    Například: 

    ![Pokyny pro připojení ke svazkům protokolu Kerberos](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. Vytvořte adresář (přípojný bod) pro nový svazek.  

4. Nastavte výchozí typ šifrování na AES 256 pro účet počítače:  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * Tento příkaz musíte spustit pouze jednou pro každý účet počítače.
    * Tento příkaz můžete spustit z řadiče domény nebo z počítače s nainstalovanými [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) . 
    * `$NFSCOMPUTERACCOUNT`Proměnná je účet počítače vytvořený ve službě Active Directory při nasazení svazku Kerberos. Jedná se o účet s předponou `NFS-` . 
    * `$ANFSERVICEACCOUNT`Proměnná je uživatelský účet služby Active Directory bez oprávnění s delegovanými ovládacími prvky v rámci organizační jednotky, ve které byl účet počítače vytvořen. 

5. Připojte svazek na hostiteli: 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * `$ANFEXPORT`Proměnná je cesta, která se `host:/export` nachází v pokynech k připojení.
    * `$ANFMOUNTPOINT`Proměnná je uživatelem vytvořená složka v hostiteli systému Linux.

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>Dopad na výkon protokolu Kerberos v NFSv 4.1 

Měli byste pochopit možnosti zabezpečení, které jsou k dispozici pro svazky NFSv 4.1, testované vektory výkonu a očekávaný dopad na výkon protokolu Kerberos. Podrobnosti najdete v podrobnostech o [dopadu protokolu Kerberos na svazcích nfsv 4.1](performance-impact-kerberos.md) .  

## <a name="next-steps"></a>Další kroky  

* [Dopad na výkon protokolu Kerberos na svazcích NFSv 4.1](performance-impact-kerberos.md)
* [Řešení potíží se svazkem NFSv 4.1 protokolu Kerberos](troubleshoot-nfsv41-kerberos-volumes.md)
* [Nejčastější dotazy týkající se Azure NetApp Files](azure-netapp-files-faqs.md)
* [Vytvoření svazku NFS pro službu Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Vytvoření připojení ke službě Active Directory](create-active-directory-connections.md)
* [Konfigurace klienta NFS pro Azure NetApp Files](configure-nfs-clients.md) 
