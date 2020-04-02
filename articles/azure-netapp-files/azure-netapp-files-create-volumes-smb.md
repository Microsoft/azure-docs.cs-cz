---
title: Vytvoření svazku SMB pro soubory Azure NetApp | Dokumenty společnosti Microsoft
description: Popisuje, jak vytvořit svazek SMB pro soubory Azure NetApp.
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
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: b-juche
ms.openlocfilehash: 9ad9e13667791c38a8bf8be01919bcdbd0032102
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519580"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Vytvoření svazku SMB pro Azure NetApp Files

Azure NetApp Files podporuje svazky NFS a SMBv3. Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu. Tento článek ukazuje, jak vytvořit svazek SMBv3. Pokud chcete vytvořit svazek systému souborů NFS, přečtěte si informace [o vytvoření svazku systému souborů služby NFS pro soubory Azure NetApp](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Než začnete 
Musíte mít už nastavený fond kapacity.   
[Nastavení fondu kapacit](azure-netapp-files-set-up-capacity-pool.md)   
Podsíť musí být delegována na soubory Azure NetApp.  
[Delegování podsítě do Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Požadavky na připojení služby Active Directory

 Před vytvořením svazku SMB je třeba vytvořit připojení služby Active Directory. Požadavky na připojení služby Active Directory jsou následující: 

* Účet správce, který používáte, musí mít možnost vytvářet účty počítačů v cestě organizační jednotky ,, kterou zadáte.  

* Na příslušném serveru služby Windows Active Directory (AD) musí být otevřeny správné porty.  
    Požadované porty jsou následující: 

    |     Služba           |     Port     |     Protocol (Protokol)     |
    |-----------------------|--------------|------------------|
    |    Webové služby služby ad    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    Není dostupné.       |    Odpověď ozvěny    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    Název rozhraní NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32čas            |    123       |    UDP           |

* Topologie webu pro cílovou službu Active Directory Domain Services musí dodržovat osvědčené postupy, zejména virtuální síť Azure, kde se nasazují soubory Azure NetApp.  

    Adresní prostor pro virtuální síť, ve které se nasazují soubory Azure NetApp, musí být přidán do nové nebo existující lokality služby Active Directory (kde je řadič domény dosažitelný soubory Azure NetApp). 

* Zadané servery DNS musí být dosažitelné z [delegované podsítě](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) souborů Azure NetApp.  

    Informace o plánování sítě Azure NetApp Files pro podporované topologie sítě najdete v tématu [Pokyny pro plánování sítě Souborů Azure NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)

    Skupiny zabezpečení sítě (NSG) a brány firewall musí mít vhodně nakonfigurovaná pravidla, která umožní požadavky na přenosy služby Active Directory a DNS. 

* Delegovaná podsíť Soubory Azure NetApp musí být schopná oslovit všechny řadiče domény služby Active Directory Domain Services (ADDS) v doméně, včetně všech místních a vzdálených řadičů domény. V opačném případě může dojít k přerušení služby.  

    Pokud máte řadiče domény, které jsou nedostupné delegovanou podsítí Soubory Azure NetApp, můžete při vytváření připojení služby Active Directory určit web služby Active Directory.  Soubory Azure NetApp musí komunikovat pouze s řadiči domény v lokalitě, kde je adresní prostor delegované podsítě Azure NetApp.

    Viz [Návrh topologie webu](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) o webech a službách AD. 
    
* Azure NetApp Files podporuje typy šifrování DES, Kerberos AES 128 a Kerberos AES 256 (od nejméně zabezpečených po nejbezpečnější). Pověření uživatele používaná pro připojení ke službě Active Directory musí mít povolenou nejvyšší odpovídající možnost účtu, která odpovídá možnostem povoleným pro službu Active Directory.   

    Pokud má například služba Active Directory pouze funkci AES-128, musíte pro pověření uživatele povolit možnost účtu AES-128. Pokud má vaše služba Active Directory funkci AES-256, musíte povolit možnost účtu AES-256 (která také podporuje AES-128). Pokud vaše služba Active Directory nemá žádné možnosti šifrování protokolu Kerberos, soubory Azure NetApp ve výchozím nastavení používají des.  

    Možnosti účtu můžete povolit ve vlastnostech konzoly MMC uživatelů a počítačů služby Active Directory:   

    ![Konzola MMC uživatelů a počítačů služby Active Directory](../media/azure-netapp-files/ad-users-computers-mmc.png)

Přečtěte si, že se informace o dalších informacích služby AD vyjádřite k souborům [SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) azure netapp. 

## <a name="decide-which-domain-services-to-use"></a>Rozhodněte se, které služby domény použít 

Soubory Azure NetApp podporují [služby Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) i služby Azure Active Directory Domain Services (AADDS) pro připojení služby AD.  Před vytvořením připojení ad, musíte se rozhodnout, zda chcete použít přidá nebo aadds.  

Další informace naleznete [v tématu Porovnání samoobslužných služeb Active Directory Domain Services, Služby Azure Active Directory a spravované služby Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Pro soubory Azure NetApp můžete použít upřednostňovaný obor [lokalit a služeb Active Directory.](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) Tato možnost umožňuje čtení a zápisy do řadičů domény služby Active Directory Domain Services (ADDS), které jsou [přístupné soubory Azure NetApp](azure-netapp-files-network-topologies.md). Zabrání také službě v komunikaci s řadiči domény, které nejsou v zadané lokalitě a síti Služby Active Directory. 

Chcete-li při použití funkce PŘIDÁ Vyhledat název webu, můžete se obrátit na skupinu pro správu ve vaší organizaci, která je zodpovědná za službu Active Directory Domain Services. Následující příklad ukazuje plugin pro sítě a služby Active Directory, ve kterém je zobrazen název webu: 

![Lokality a služby Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

Při konfiguraci připojení služby AD pro soubory Azure NetApp zadáte název webu v oboru pro pole **Název webu služby AD.**

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Konfigurace a pokyny služby Azure Active Directory Domain Services (AADDS) najdete [v dokumentaci ke službě Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

Další aadds aspekty platí pro soubory Azure NetApp: 

* Ujistěte se, že virtuální síť nebo podsíť, kde se nasazuje AADDS, je ve stejné oblasti Azure jako nasazení souborů Azure NetApp.
* Pokud používáte jinou virtuální síť v oblasti, kde se nasazují soubory Azure NetApp, měli byste vytvořit partnerský vztah mezi dvěma virtuálními sítěmi.
* Azure NetApp `user` Files `resource forest` podporuje a typy.
* Pro typ synchronizace můžete `All` `Scoped`vybrat nebo .   
    Pokud vyberete `Scoped`, ujistěte se, že je vybrána správná skupina Azure AD pro přístup ke sdíleným položkám SMB.  Pokud si nejste jisti, `All` můžete použít typ synchronizace.
* Je vyžadováno použití sku Enterprise nebo Premium. Standardní skladová položka není podporována.

Při vytváření připojení služby Active Directory si poznamenejte následující specifika pro aadds:

* Informace pro **primární DNS**, Sekundární DNS a Název domény DNS **služby** **AD** naleznete v nabídce AADDS.  
Pro servery DNS budou ke konfiguraci připojení služby Active Directory použity dvě adresy IP. 
* **Cesta organizační jednotky** `OU=AADDC Computers`je .  
Toto nastavení je konfigurováno v **části Připojení služby Active Directory** v části Účet **NetApp**:

  ![Cesta organizační jednotky](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **Uživatelské jméno** pověření může být libovolný uživatel, který je členem skupiny Azure AD **řadiče domény .**


## <a name="create-an-active-directory-connection"></a>Vytvoření připojení služby Active Directory

1. V účtu NetApp klepněte na **položku Připojení služby Active Directory**a potom klepněte na tlačítko **Připojit se**.  

    ![Připojení služby Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. V okně Připojit službu Active Directory zadejte následující informace založené na službě Domain Services, kterou chcete použít:  

    Informace specifické pro služby domény, které používáte, naleznete [v tématu Rozhodnutí, které služby domény chcete používat](#decide-which-domain-services-to-use). 

    * **Primární DNS**  
        Jedná se o službu DNS, která je vyžadována pro operace připojení domény služby Active Directory a ověřování SMB. 
    * **Sekundární DNS**   
        Toto je sekundární server DNS pro zajištění redundantních jmenných služeb. 
    * **Název domény služby AD DNS**  
        Toto je název domény služby Active Directory Domain Services, ke které se chcete připojit.
    * **Název webu ad**  
        Jedná se o název webu, na který bude zjišťování řadiče domény omezeno.
    * **Předpona serveru SMB (účet počítače)**  
        Toto je předpona pojmenování pro účet počítače ve službě Active Directory, kterou budou soubory Azure NetApp používat pro vytváření nových účtů.

        Pokud je například standard pojmenování, který vaše organizace používá pro souborové servery, NAS-01, NAS-02..., NAS-045, zadejte pro předponu "NAS". 

        Služba vytvoří další účty počítače ve službě Active Directory podle potřeby.

    * **Cesta organizační jednotky**  
        Toto je cesta LDAP pro organizační jednotku (OU), kde budou vytvořeny účty serverového počítače SMB. To znamená OU = druhá úroveň, OU = první úroveň. 

        Pokud používáte soubory Azure NetApp se službou Azure Active `OU=AADDC Computers` Directory Domain Services, cesta organizační jednotky je při konfiguraci služby Active Directory pro váš účet NetApp.
        
    * Přihlašovací údaje, včetně **uživatelského jména** a **hesla**

    ![Připojení ke službě Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Klikněte na **Připojit**.  

    Zobrazí se připojení služby Active Directory, které jste vytvořili.

    ![Připojení služby Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> Po uložení připojení služby Active Directory můžete upravit pole uživatelského jména a hesla. Po uložení připojení nelze upravovat žádné další hodnoty. Pokud potřebujete změnit jiné hodnoty, musíte nejprve odstranit všechny nasazené svazky SMB a potom odstranit a znovu vytvořit připojení služby Active Directory.

## <a name="add-an-smb-volume"></a>Přidání svazku SMB

1. Klepněte na okno **Svazky** z okna Kapacitní fondy. 

    ![Přechod na svazky](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kliknutím na **+ Přidat svazek** vytvořte svazek.  
    Zobrazí se okno Vytvořit svazek.

3. V okně Vytvořit svazek klikněte na **Vytvořit** a poskytněte informace pro následující pole:   
    * **Název svazku**      
        Zadejte název svazku, který vytváříte.   

        Název svazku musí být jedinečný v rámci každého fondu kapacity. Musí mít alespoň tři znaky dlouhé. Můžete použít libovolné alfanumerické znaky.   

        Nelze použít `default` jako název svazku.

    * **Kapacitní fond**  
        Určete fond kapacity, do kterého chcete svazek vytvořit.

    * **Kvóta**  
        Určuje velikost logického úložiště, které je přidělené svazku.  

        Pole **Dostupná kvóta** zobrazuje množství nevyužitého místa ve zvoleném fondu kapacity, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.  

    * **Virtuální síť**  
        Zadejte virtuální síť Azure (VNet), ze které chcete získat přístup ke svazku.  

        Zadanou virtuální síť musí mít podsíť delegovanou na soubory Azure NetApp. Služba Azure NetApp Files je přístupná jenom ze stejné virtuální sítě nebo z virtuální sítě, která je ve stejné oblasti jako svazek prostřednictvím partnerského vztahu virtuální sítě. Ke svazku můžete přistupovat také z místní sítě prostřednictvím expresní trasy.   

    * **Podsíť**  
        Zadejte podsíť, kterou chcete pro svazek použít.  
        Zadaná podsíť musí být delegována na soubory Azure NetApp. 
        
        Pokud jste nedelegovali podsíť, můžete na stránce Vytvořit svazek kliknout na **Vytvořit nový.** Potom na stránce Vytvořit podsíť zadejte informace o podsíti a vyberte **Microsoft.NetApp/svazky** delegovat podsíť pro soubory Azure NetApp. V každé virtuální síti lze delegovat jenom jednu podsíť na soubory Azure NetApp.   
 
        ![Vytvoření svazku](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Vytvoření podsítě](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klepněte na tlačítko **Protokol** a vyplňte následující informace:  
    * Jako **SMB** typ protokolu pro svazek vyberte smb. 
    * V rozevíracím seznamu vyberte připojení **služby Active Directory.**
    * Zadejte název sdíleného svazku v **názvu sdílené položky**.

    ![Zadat protokol SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Kliknutím na **Zkontrolovat + Vytvořit** zkontrolujte podrobnosti svazku.  Pak kliknutím na **Vytvořit** vytvořte svazek SMB.

    Vytvořený svazek se zobrazí na stránce Svazky. 
 
    Svazek dědí atributy předplatného, skupiny prostředků a umístění z fondu kapacity. Stav nasazení svazku můžete monitorovat na kartě Oznámení.

## <a name="next-steps"></a>Další kroky  

* [Připojování nebo odpojování svazku pro virtuální počítače s Windows nebo Linuxem](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Nejčastější dotazy k SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Informace o integraci virtuálních sítí pro služby Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Instalace nové doménové struktury služby Active Directory pomocí rozhraní příkazového příkazu Azure](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
