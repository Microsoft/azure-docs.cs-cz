---
title: Vytvoření svazku s duálním protokolem (NFSv3 a SMB) pro Azure NetApp Files | Microsoft Docs
description: Popisuje, jak vytvořit svazek, který používá duální protokol NFSv3 a SMB s podporou mapování uživatelů LDAP.
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
ms.openlocfilehash: ce65d6f1806965a55a91117725d2232d4d6460bd
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449640"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Vytvoření svazku s duálním protokolem (NFSv3 a protokolu SMB) pro Azure NetApp Files

Azure NetApp Files podporuje vytváření svazků pomocí systému souborů NFS (NFSv3 a NFSv 4.1), SMBv3 nebo duálního protokolu. V tomto článku se dozvíte, jak vytvořit svazek, který využívá duální protokol NFSv3 a SMB s podporou mapování uživatelů LDAP.  


## <a name="before-you-begin"></a>Než začnete 

* Musíte mít už nastavený fond kapacity.  
    Viz [nastavení fondu kapacit](azure-netapp-files-set-up-capacity-pool.md).   
* Podsíť musí být delegovaná na Azure NetApp Files.  
    Viz [delegování podsítě na Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Požadavky

* Ujistěte se, že splňujete [požadavky pro připojení ke službě Active Directory](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* Na serveru DNS vytvořte zónu zpětného vyhledávání a přidejte do této zóny zpětného vyhledávání záznam ukazatele (PTR) hostitelského počítače služby AD. V opačném případě se vytvoření svazku se dvěma protokoly nezdaří.
* Zajistěte, aby byl klient systému souborů NFS aktuální a běžel nejnovější aktualizace operačního systému.
* Ujistěte se, že je server služby Active Directory (AD) LDAP v provozu a funguje ve službě AD. To se provádí instalací a konfigurací role [Služba AD LDS (Active Directory Lightweight Directory Services) (AD LDS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) na počítači AD.
* Ujistěte se, že certifikační autorita (CA) je ve službě AD vytvořená pomocí role [služby AD CS (Active Directory Certificate Services)](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) , která generuje a exportuje certifikát kořenové certifikační autority podepsané svým držitelem.   

## <a name="create-a-dual-protocol-volume"></a>Vytvoření svazku se dvěma protokoly

1.  V okně fondy kapacit klikněte na okno **svazky** . Kliknutím na **+ Přidat svazek** vytvořte svazek. 

    ![Přejít na svazky](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  V okně vytvořit svazek klikněte na **vytvořit**a zadejte informace pro následující pole na kartě základy:   
    * **Název svazku**      
        Zadejte název svazku, který vytváříte.   

        Název svazku musí být v rámci každého fondu kapacity jedinečný. Musí mít aspoň tři znaky dlouhé. Můžete použít jakékoli alfanumerické znaky.   

        Nemůžete použít `default` nebo `bin` jako název svazku.

    * **Fond kapacit**  
        Zadejte fond kapacit, ve kterém chcete vytvořit svazek.

    * **Kvóta**  
        Určuje velikost logického úložiště, které je přidělené svazku.  

        Pole **Dostupná kvóta** zobrazuje množství nevyužitého místa ve zvoleném fondu kapacity, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.  

    * **Propustnost (MiB/S)**   
        Pokud je svazek vytvořený v manuálním fondu kapacity QoS, určete propustnost, kterou pro svazek požadujete.   

        Pokud se svazek vytvoří ve fondu kapacity auto QoS, hodnota zobrazená v tomto poli je (propustnost × úroveň služby).   

    * **Virtuální síť**  
        Zadejte službu Azure Virtual Network (VNet), ze které chcete získat přístup ke svazku.  

        Virtuální síť, kterou zadáte, musí mít přidělenou podsíť Azure NetApp Files. Služba Azure NetApp Files se dá použít jenom ze stejné virtuální sítě nebo z virtuální sítě, která se nachází ve stejné oblasti jako svazek prostřednictvím partnerského vztahu virtuálních sítí. Ke svazku z místní sítě se můžete dostat i přes Express Route.   

    * **Podsíť**  
        Zadejte podsíť, kterou chcete použít pro svazek.  
        Podsíť, kterou zadáte, musí být delegovaná na Azure NetApp Files. 
        
        Pokud jste nedelegovanou podsíť, můžete na stránce vytvořit svazek kliknout na **vytvořit novou** . Pak na stránce vytvořit podsíť zadejte informace o podsíti a vyberte možnost **Microsoft. NetApp/** Volumes pro delegování podsítě pro Azure NetApp Files. V každé virtuální síti je možné delegovat jenom jednu podsíť na Azure NetApp Files.   
 
        ![Vytvoření svazku](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Vytvoření podsítě](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Pokud chcete pro svazek použít existující zásadu snímku, rozbalte ji kliknutím na **Zobrazit Upřesnit oddíl** , určete, jestli chcete cestu k snímku skrýt, a v rozevírací nabídce vyberte zásadu snímku. 

        Informace o vytváření zásad snímku najdete v tématu [Správa zásad snímků](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Zobrazit rozšířený výběr](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Klikněte na **protokol**a pak proveďte následující akce:  
    * Jako typ protokolu pro svazek vyberte **duální protokol (NFSv3 a SMB)** .   

    * V rozevíracím seznamu vyberte připojení **služby Active Directory** .  
    Služba Active Directory, kterou použijete, musí mít certifikát kořenové certifikační autority serveru. 

    * Zadejte **cestu svazku** pro svazek.   
    Tato cesta svazku je název sdíleného svazku. Název musí začínat abecedním znakem a musí být jedinečný v rámci každého předplatného a každé oblasti.  

    * Zadejte **styl zabezpečení** , který se má použít: systém souborů NTFS (výchozí) nebo UNIX.

    * Volitelně můžete [nakonfigurovat zásadu exportu pro svazek](azure-netapp-files-configure-export-policy.md).

    ![Zadat duální protokol](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Kliknutím na tlačítko **zkontrolovat + vytvořit** zkontrolujte podrobnosti o svazku. Pak kliknutím na **vytvořit** vytvořte svazek.

    Svazek, který jste vytvořili, se zobrazí na stránce svazky. 
 
    Svazek dědí atributy předplatného, skupiny prostředků a umístění z fondu kapacity. Stav nasazení svazku můžete monitorovat na kartě Oznámení.

## <a name="upload-active-directory-certificate-authority-public-root-certificate"></a>Odeslat veřejný kořenový certifikát certifikační autority služby Active Directory  

1.  Postupujte podle pokynů k instalaci a konfiguraci [certifikační](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) autority a přidat certifikační autoritu. 

2.  Podle pokynů v [části zobrazení certifikátů pomocí modulu snap-](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) in MMC použijte modul snap-in konzoly MMC a nástroj Správce certifikátů.  
    Pomocí modulu snap-in Správce certifikátů vyhledejte kořenový nebo vydávající certifikát pro místní zařízení. V jednom z následujících nastavení byste měli spustit příkazy modulu snap-in Správa certifikátů:  
    * Klient se systémem Windows, který se připojil k doméně a má nainstalovaný kořenový certifikát 
    * Další počítač v doméně obsahující kořenový certifikát  

3. Exportujte kořenový certifikát.  
    Zajistěte, aby byl certifikát exportován v kódování X. 509 kódované pomocí Base-64 (. Formát CER): 

    ![Průvodce exportem certifikátu](../media/azure-netapp-files/certificate-export-wizard.png)

4. Přejděte na účet NetApp svazku se dvěma protokoly, klikněte na **připojení služby Active Directory**a nahrajte certifikát kořenové certifikační autority pomocí okna **připojit se ke službě Active Directory** :  

    ![Certifikát kořenové certifikační autority serveru](../media/azure-netapp-files/server-root-ca-certificate.png)

    Zajistěte, aby název certifikační autority mohl přeložit služba DNS. Tento název je pole "vystavitelný" nebo "Issuer" na certifikátu:  

    ![Informace o certifikátu](../media/azure-netapp-files/certificate-information.png)

## <a name="manage-ldap-posix-attributes"></a>Správa atributů LDAP POSIX

Pomocí modulu snap-in konzoly MMC Uživatelé a počítače služby Active Directory můžete spravovat atributy POSIX, jako je UID, domovský adresář a další hodnoty.  Následující příklad ukazuje Editor atributů služby Active Directory:  

![Editor atributů služby Active Directory](../media/azure-netapp-files/active-directory-attribute-editor.png) 

Pro uživatele LDAP a skupiny LDAP musíte nastavit následující atributy: 
* Požadované atributy pro uživatele LDAP:   
    `uid`: Alice, `uidNumber` : 139, `gidNumber` : 555, `objectClass` : posixAccount
* Požadované atributy pro skupiny LDAP:   
    `objectClass`: "POSIX", `gidNumber` : 555

## <a name="configure-the-nfs-client"></a>Konfigurace klienta NFS 

Postupujte podle pokynů v části [Konfigurace klienta NFS pro Azure NetApp Files](configure-nfs-clients.md) ke konfiguraci klienta NFS.  

## <a name="next-steps"></a>Další kroky  

* [Nejčastější dotazy týkající se duálního protokolu](azure-netapp-files-faqs.md#dual-protocol-faqs)
* [Konfigurace klienta NFS pro Azure NetApp Files](configure-nfs-clients.md) 
