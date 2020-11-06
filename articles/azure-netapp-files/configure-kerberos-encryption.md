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
ms.date: 11/05/2020
ms.author: b-juche
ms.openlocfilehash: f4b485e79bfa89fe293c99fc4e84fc8c0729396a
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331885"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Konfigurace šifrování protokolu Kerberos NFSv 4.1 pro Azure NetApp Files

Azure NetApp Files podporuje šifrování klienta NFS v režimech Kerberos (krb5, Krb5i a krb5p) pomocí šifrování AES-256. Tento článek popisuje požadované konfigurace pro použití svazku NFSv 4.1 s šifrováním protokolu Kerberos.

## <a name="considerations"></a>Co je potřeba vzít v úvahu

* NFSv 4.1 svazky šifrování protokolu Kerberos v současné době nepodporují Azure Active Directory Domain Services (AADDS). 

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

1.  Postupujte podle pokynů v tématu [vytvoření připojení ke službě Active Directory](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection).  

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

    Příklad: 

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

V této části se seznámíte s dopadem na výkon protokolu Kerberos v NFSv 4.1.

### <a name="available-security-options"></a>Dostupné možnosti zabezpečení 

Možnosti zabezpečení, které jsou aktuálně dostupné pro svazky NFSv 4.1, jsou následující: 

* **SEK = sys** používá k ověření operací systému souborů NFS místní identifikátory UID a GIDs AUTH_SYS v systému UNIX.
* **SEK = krb5** používá k ověřování uživatelů protokol Kerberos V5 namísto místních identifikátorů UID a GIDS systému UNIX.
* **SEK = Krb5i** používá protokol Kerberos V5 k ověřování uživatelů a provádí kontrolu integrity operací systému souborů NFS pomocí zabezpečených kontrolních součtů, které zabraňují manipulaci s daty.
* **SEK = krb5p** používá protokol Kerberos V5 pro ověřování uživatelů a kontrolu integrity. Šifruje provoz systému souborů NFS, aby nedocházelo ke sledování provozu. Tato možnost je nejbezpečnější nastavení, ale zahrnuje i nejvyšší nároky na výkon.

### <a name="performance-vectors-tested"></a>Testované vektory výkonu

Tato část popisuje jeden vliv na výkon různých možností na straně klienta `sec=*` .

* Dopad na výkon byl testován na dvou úrovních: nízká Concurrency (nízké zatížení) a vysoká souběžnost (horní meze vstupně-výstupních operací a propustnosti).  
* Byly testovány tři typy úloh:  
    * Nízká operace náhodným čtením/zápisem (pomocí FIO)
    * Sekvenční operace čtení/zápisu velkých operací (pomocí FIO)
    * Metadata vysoké zátěže generované aplikacemi, jako je git

### <a name="expected-performance-impact"></a>Očekávaný dopad na výkon 

Existují dvě oblasti výběru: světlo zatížení a horní mez. Následující seznamy popisují nastavení zabezpečení vlivu na výkon podle nastavení zabezpečení a scénáře podle scénáře. Všechna porovnání jsou provedena proti `sec=sys` parametru zabezpečení. Test byl proveden na jednom svazku s použitím jednoho klienta. 

Dopad na výkon krb5:

* Nízká souběžná (r/w):
    * Sekvenční latence zvýšené o 0,3 MS.
    * Náhodná vstupně-výstupní latence vzrostla 0,2 MS.
    * I/O latence metadat vzrostla 0,2 MS.
* Vysoká souběžnost (r/w): 
    * Krb5 neovlivnila maximální Sekvenční propustnost.
    * Maximální počet náhodných vstupně-výstupních operací se snížil o 30% pro úlohy čistého čtení s celkovým dopadem na hodnotu nula, protože se zatížení posune na čistý zápis. 
    * Maximální zatížení metadat se snížilo na 30%.

Dopad na výkon Krb5i: 

* Nízká souběžná (r/w):
    * Sekvenční latence zvýšené o 0,5 ms.
    * Náhodná vstupně-výstupní latence vzrostla 0,2 MS.
    * I/O latence metadat vzrostla 0,2 MS.
* Vysoká souběžnost (r/w): 
    * Maximální následná propustnost se snížila o 70% celkových bez ohledu na kombinaci úloh.
    * Maximální počet náhodných vstupně-výstupních operací se snížil o 50% pro úlohy čistého čtení s celkovým dopadem snížení na 25% při posunu zátěže na čistý zápis. 
    * Maximální zatížení metadat se snížilo na 30%.

Dopad na výkon krb5p:

* Nízká souběžná (r/w):
    * Sekvenční latence zvýšené o 0,8 ms.
    * Náhodná vstupně-výstupní latence vzrostla 0,2 MS.
    * I/O latence metadat vzrostla 0,2 MS.
* Vysoká souběžnost (r/w): 
    * Maximální následná propustnost se snížila o 85% celkových bez ohledu na kombinaci úloh. 
    * Maximální počet náhodných vstupně-výstupních operací poklesl o 65% pro úlohy čistého čtení s celkovým dopadem snížení na 43% při posunování zatížení na čistý zápis. 
    * Maximální zatížení metadat se snížilo na 30%.

## <a name="next-steps"></a>Další kroky  

* [Nejčastější dotazy týkající se Azure NetApp Files](azure-netapp-files-faqs.md)
* [Vytvoření svazku NFS pro službu Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Vytvoření připojení ke službě Active Directory](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)
* [Konfigurace klienta NFS pro Azure NetApp Files](configure-nfs-clients.md) 
