---
title: Vytvoření svazku SMB pro Azure NetApp Files | Microsoft Docs
description: V tomto článku se dozvíte, jak vytvořit SMB3 svazek v Azure NetApp Files. Seznamte se s požadavky na připojení služby Active Directory a doménové služby.
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
ms.date: 12/01/2020
ms.author: b-juche
ms.openlocfilehash: 7c5f18d33efa1838fccab3935054ea19227bc06c
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102848"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Vytvoření svazku SMB pro Azure NetApp Files

Azure NetApp Files podporuje vytváření svazků pomocí systému souborů NFS (NFSv3 a NFSv 4.1), SMB3 nebo duálního protokolu (NFSv3 a SMB). Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu. V tomto článku se dozvíte, jak vytvořit svazek SMB3.

## <a name="before-you-begin"></a>Než začnete 
Musíte mít už nastavený fond kapacity.   
[Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)   
Podsíť musí být delegovaná na Azure NetApp Files.  
[Delegování podsítě na službu Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Požadavky na připojení Active Directory

 Před vytvořením svazku SMB musíte vytvořit připojení ke službě Active Directory. Požadavky na připojení služby Active Directory jsou následující: 

* Účet správce, který použijete, musí mít možnost vytvářet účty počítačů v cestě organizační jednotky (OU), kterou zadáte.  

* V příslušném serveru služby Windows Active Directory (AD) musí být otevřeny správné porty.  
    Požadované porty jsou následující: 

    |     Služba           |     Port     |     Protokol     |
    |-----------------------|--------------|------------------|
    |    Webové služby AD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    –       |    Odpověď na ozvěnu    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    Název pro rozhraní NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    W32Time            |    123       |    UDP           |

* Topologie lokality pro cílovou Active Directory Domain Services musí splňovat pokyny, zejména v případě, že je nainstalovaná Azure NetApp Files virtuální síť Azure.  

    Adresní prostor pro virtuální síť, ve které je nasazený Azure NetApp Files, musí být přidán do nové nebo existující lokality služby Active Directory (kde řadič domény dosažitelný pomocí Azure NetApp Files je). 

* Zadané servery DNS musí být dosažitelné z [delegované podsítě](./azure-netapp-files-delegate-subnet.md) Azure NetApp Files.  

    V tématu [pokyny pro Azure NetApp Files plánování sítě](./azure-netapp-files-network-topologies.md) pro podporované topologie sítě.

    Skupiny zabezpečení sítě (skupin zabezpečení sítě) a brány firewall musí mít vhodně nakonfigurovaná pravidla umožňující požadavky na provoz služby Active Directory a DNS. 

* Azure NetApp Files delegovaná podsíť musí mít přístup ke všem řadičům domény Active Directory Domain Services (v doméně), včetně všech místních a vzdálených řadičů domény. V opačném případě může dojít k přerušení služeb.  

    Pokud máte řadiče domény, které jsou Azure NetApp Files delegovanou podsítí nedosažitelné, můžete během vytváření připojení Active Directory určit lokalitu služby Active Directory.  Azure NetApp Files musí komunikovat pouze s řadiči domény v lokalitě, kde je Azure NetApp Files delegovaný adresní prostor podsítě.

    Viz [návrh topologie lokality](/windows-server/identity/ad-ds/plan/designing-the-site-topology) o lokalitách a službách Active Directory. 
    
* Šifrování AES pro ověřování AD lze povolit zaškrtnutím políčka **šifrování AES** v okně připojit se [ke službě Active Directory](#create-an-active-directory-connection) . Azure NetApp Files podporuje typy šifrování DES, Kerberos AES 128 a Kerberos AES 256 (z nejméně zabezpečeného na nejbezpečnější). Pokud povolíte šifrování AES, musí mít přihlašovací údaje uživatele použité pro připojení ke službě Active Directory nejvyšší odpovídající možnost účtu, která bude odpovídat funkcím povoleným pro vaši službu Active Directory.    

    Pokud má vaše služba Active Directory například jenom možnost AES-128, musíte pro přihlašovací údaje uživatele povolit možnost účet AES-128. Pokud má služba Active Directory schopnost AES-256, musíte povolit možnost účtu AES-256 (která také podporuje AES-128). Pokud vaše služba Active Directory nemá žádnou možnost šifrování protokolu Kerberos, Azure NetApp Files ve výchozím nastavení používá algoritmus DES.  

    Možnosti účtu můžete povolit ve vlastnostech modulu snap-in Uživatelé a počítače služby Active Directory konzoly MMC (Microsoft Management Console):   

    ![Modul snap-in Uživatelé a počítače služby Active Directory](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files podporuje [podepisování LDAP](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server), které umožňuje zabezpečený přenos dat protokolu LDAP mezi službou Azure NetApp Files a cílovými [řadiči domény služby Active Directory](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Pokud budete postupovat podle pokynů v poradenské službě Microsoft Advisor [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) pro podepisování LDAP, měli byste povolit funkci podepisování ldap v Azure NetApp Files kontrolou pole **podepisování LDAP** v okně [připojit se ke službě Active Directory](#create-an-active-directory-connection) . 

    Samotná konfigurace [vazby kanálu LDAP](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) nemá žádný vliv na službu Azure NetApp Files. Pokud ale použijete vazbu kanálu LDAP i zabezpečený protokol LDAP (například LDAPs nebo `start_tls` ), vytvoření svazku SMB se nezdaří.

Další informace o službě AD najdete v tématu Azure NetApp Files [nejčastějších dotazů k protokolu SMB](./azure-netapp-files-faqs.md#smb-faqs) . 

## <a name="decide-which-domain-services-to-use"></a>Rozhodněte, které doménové služby se mají použít. 

Azure NetApp Files pro připojení AD podporuje [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (přidávání) i Azure Active Directory Domain Services (AADDS).  Před vytvořením připojení AD se musíte rozhodnout, jestli chcete použít příkaz Přidat nebo AADDS.  

Další informace najdete v tématu [porovnání samoobslužně spravovaných Active Directory Domain Services, Azure Active Directory a spravovaných Azure Active Directory Domain Services](../active-directory-domain-services/compare-identity-solutions.md). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Pro Azure NetApp Files můžete použít preferované obory [lokality a služby Active Directory](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) . Tato možnost umožňuje čtení a zápisy do Active Directory Domain Services (přidá) řadiče domény, které jsou [přístupné Azure NetApp Files](azure-netapp-files-network-topologies.md). Zabrání taky službě v komunikaci s řadiči domény, které nejsou ve specifikovaných lokalitách a lokalitách služby Active Directory. 

Pokud chcete najít název vaší lokality, když použijete příkaz Přidat, můžete kontaktovat skupinu pro správu ve vaší organizaci, která je zodpovědná za Active Directory Domain Services. Následující příklad ukazuje modul plug-in lokality a služby Active Directory, kde se zobrazuje název lokality: 

![Lokality a služby Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Když nakonfigurujete připojení AD pro Azure NetApp Files, zadáte název lokality v oboru pro pole **název lokality služby Active Directory** .

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Informace o konfiguraci a pokyny pro Azure Active Directory Domain Services (AADDS) najdete v [dokumentaci k Azure AD Domain Services](../active-directory-domain-services/index.yml).

Pro Azure NetApp Files se vztahují další požadavky na AADDS: 

* Ujistěte se, že virtuální síť nebo podsíť, ve které je AADDS nasazená, jsou ve stejné oblasti Azure jako nasazení Azure NetApp Files.
* Pokud použijete jinou virtuální síť v oblasti, ve které je nasazený Azure NetApp Files, měli byste vytvořit partnerský vztah mezi dvěma virtuální sítěy.
* Azure NetApp Files podporuje `user` a `resource forest` typy.
* Pro typ synchronizace můžete vybrat `All` nebo `Scoped` .   
    Pokud vyberete `Scoped` , ujistěte se, že je pro přístup ke sdíleným složkám SMB vybraná správná skupina Azure AD.  Pokud si nejste jistí, můžete použít `All` Typ synchronizace.
* Je potřeba použít SKU Enterprise nebo Premium. Standardní SKU se nepodporuje.

Při vytváření připojení ke službě Active Directory si všimněte následujících specifických možností pro AADDS:

* V nabídce AADDS najdete informace pro **primární DNS**, **sekundární DNS** a **název domény DNS služby AD** .  
Pro servery DNS se ke konfiguraci připojení služby Active Directory použijí dvě IP adresy. 
* **Cesta k organizační jednotce** je `OU=AADDC Computers` .  
Toto nastavení se konfiguruje v **připojeních služby Active Directory** pod **účtem NetApp**:

  ![Cesta organizační jednotky](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* Přihlašovací údaje pro **uživatelské jméno** může být libovolný uživatel, který je členem skupiny Azure **AD DC Administrators**.


## <a name="create-an-active-directory-connection"></a>Vytvoření připojení ke službě Active Directory

1. Z účtu NetApp klikněte na **připojení služby Active Directory** a pak klikněte na **připojit**.  

    ![Připojení ke službě Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. V okně připojit se ke službě Active Directory zadejte následující informace na základě doménových služeb, které chcete použít:  

    Informace specifické pro používané doménové služby najdete v tématu určení, [které doménové služby se mají použít](#decide-which-domain-services-to-use). 

    * **Primární DNS**  
        Toto je služba DNS potřebná pro operace připojení k doméně služby Active Directory a ověřování SMB. 
    * **Sekundární DNS**   
        Toto je sekundární server DNS pro zajištění redundantních názvových služeb. 
    * **Název domény DNS služby Active Directory**  
        Toto je název domény vašeho Active Directory Domain Services, ke které se chcete připojit.
    * **Název lokality služby Active Directory**  
        Jedná se o název lokality, na kterou bude zjišťování řadiče domény omezeno. Tato stránka by se měla shodovat s názvem webu v části lokality a služby Active Directory.
    * **Předpona serveru SMB (účet počítače)**  
        Toto je předpona názvů pro účet počítače ve službě Active Directory, kterou Azure NetApp Files použít pro vytváření nových účtů.

        Pokud například standardní názvový server, který vaše organizace používá pro souborové servery, je NAS-01, NAS-02..., NAS-045, zadejte jako předponu "NAS". 

        Služba vytvoří ve službě Active Directory další účty počítačů podle potřeby.

        > [!IMPORTANT] 
        > Přejmenování předpony serveru SMB po vytvoření připojení ke službě Active Directory je rušivé. Po přejmenování předpony serveru SMB budete muset znovu připojit existující sdílené složky SMB.

    * **Cesta organizační jednotky**  
        Jedná se o cestu protokolu LDAP pro organizační jednotku (OU), kde budou vytvořeny účty počítačů serveru SMB. To znamená OU = druhá úroveň, OU = First Level. 

        Pokud používáte Azure NetApp Files s Azure Active Directory Domain Services, cesta k organizační jednotce je, `OU=AADDC Computers` když pro svůj účet NetApp nakonfigurujete službu Active Directory.

    ![Připojit ke službě Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **Šifrování AES**   
        Zaškrtnutím tohoto políčka povolíte šifrování AES pro svazek SMB. Požadavky najdete v tématu [požadavky na připojení ke službě Active Directory](#requirements-for-active-directory-connections) . 

        ![Šifrování AES pro Active Directory](../media/azure-netapp-files/active-directory-aes-encryption.png)

        Funkce **šifrování AES** je momentálně ve verzi Preview. Pokud tuto funkci používáte poprvé, zaregistrujte funkci před jejím použitím: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Ověřte stav registrace funkce: 

        > [!NOTE]
        > **RegistrationState** může být ve `Registering` stavu až 60 minut, než se změní na `Registered` . Než budete pokračovat, počkejte, než se stav **zaregistruje** .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        Můžete také použít [příkazy rozhraní příkazového řádku Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` a `az feature show` zaregistrovat funkci a zobrazit stav registrace. 

    * **Podepisování LDAP**   
        Zaškrtnutím tohoto políčka povolíte podepisování LDAP. Tato funkce umožňuje zabezpečené vyhledávání LDAP mezi službou Azure NetApp Files a uživatelem zadanými [Active Directory Domain Services řadiči domény](/windows/win32/ad/active-directory-domain-services). Další informace najdete v tématu [ADV190023 | Návod Microsoftu pro povolení vazby kanálu LDAP a podepisování LDAP](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![Podepisování LDAP služby Active Directory](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        Funkce **podepisování LDAP** je aktuálně ve verzi Preview. Pokud tuto funkci používáte poprvé, zaregistrujte funkci před jejím použitím: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Ověřte stav registrace funkce: 

        > [!NOTE]
        > **RegistrationState** může být ve `Registering` stavu až 60 minut, než se změní na `Registered` . Než budete pokračovat, počkejte, než se stav **zaregistruje** .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        Můžete také použít [příkazy rozhraní příkazového řádku Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` a `az feature show` zaregistrovat funkci a zobrazit stav registrace. 

     * **Uživatelé zásad zálohování**  
        Můžete zahrnout další účty, které vyžadují zvýšená oprávnění k účtu počítače vytvořenému pro použití s Azure NetApp Files. U zadaných účtů bude povoleno změnit oprávnění systému souborů NTFS na úrovni souboru nebo složky. Můžete například zadat účet neprivilegované služby, který se používá k migraci dat do sdílené složky SMB v Azure NetApp Files.  

        ![Uživatelé zásad zálohování služby Active Directory](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        Funkce **Uživatelé zásad zálohování** je teď ve verzi Preview. Pokud tuto funkci používáte poprvé, zaregistrujte funkci před jejím použitím: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Ověřte stav registrace funkce: 

        > [!NOTE]
        > **RegistrationState** může být ve `Registering` stavu až 60 minut, než se změní na `Registered` . Než budete pokračovat, počkejte, než se stav **zaregistruje** .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Můžete také použít [příkazy rozhraní příkazového řádku Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` a `az feature show` zaregistrovat funkci a zobrazit stav registrace. 

    * Přihlašovací údaje, včetně **uživatelského jména** a **hesla**

        ![Přihlašovací údaje služby Active Directory](../media/azure-netapp-files/active-directory-credentials.png)

3. Klikněte na **Připojit**.  

    Zobrazí se připojení služby Active Directory, které jste vytvořili.

    ![Vytvořená připojení služby Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Přidat svazek SMB

1. V okně fondy kapacit klikněte na okno **svazky** . 

    ![Přejít na svazky](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kliknutím na **+ Přidat svazek** vytvořte svazek.  
    Zobrazí se okno vytvořit svazek.

3. V okně vytvořit svazek klikněte na **vytvořit** a zadejte informace pro následující pole na kartě základy:   
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

4. Klikněte na **protokol** a vyplňte následující informace:  
    * Jako typ protokolu pro svazek vyberte **SMB** . 
    * V rozevíracím seznamu vyberte připojení ke **službě Active Directory** .
    * Zadejte název sdíleného svazku do pole  **název sdílené složky**.

    ![Zadat protokol SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Kliknutím na tlačítko **zkontrolovat + vytvořit** zkontrolujte podrobnosti o svazku.  Pak kliknutím na **vytvořit** vytvořte svazek SMB.

    Svazek, který jste vytvořili, se zobrazí na stránce svazky. 
 
    Svazek dědí atributy předplatného, skupiny prostředků a umístění z fondu kapacity. Stav nasazení svazku můžete monitorovat na kartě Oznámení.

## <a name="control-access-to-an-smb-volume"></a>Řízení přístupu ke svazku SMB  

Přístup ke svazku SMB je spravovaný prostřednictvím oprávnění.  

### <a name="share-permissions"></a>Oprávnění ke sdílení  

Ve výchozím nastavení má nový svazek oprávnění ke sdílení **všech uživatelů a úplné řízení** . Členové skupiny Domain Admins mohou změnit oprávnění ke sdílení pomocí správy počítače v účtu počítače, který se používá pro Azure NetApp Files svazek.

![](../media/azure-netapp-files/smb-mount-path.png) 
 ![ Oprávnění k nastavení sdílené složky pro cestu pro připojení SMB](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>Oprávnění k souborům a složkám NTFS  

Oprávnění pro soubor nebo složku můžete nastavit pomocí karty **zabezpečení** vlastností objektu v klientovi Windows SMB.
 
![Nastavení oprávnění k souborům a složkám](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Další kroky  

* [Připojení nebo odpojení svazku pro virtuální počítače s Windows nebo Linuxem](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Nejčastější dotazy k protokolu SMB](./azure-netapp-files-faqs.md#smb-faqs)
* [Řešení potíží se svazky SMB nebo Dual-Protocol](troubleshoot-dual-protocol-volumes.md)
* [Informace o integraci virtuální sítě pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)
* [Instalace nové doménové struktury služby Active Directory pomocí Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
