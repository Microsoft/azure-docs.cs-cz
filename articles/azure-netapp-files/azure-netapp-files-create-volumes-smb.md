---
title: Vytvoření svazku SMB pro Azure NetApp Files | Microsoft Docs
description: Popisuje postup vytvoření svazku SMB pro Azure NetApp Files.
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
ms.date: 10/02/2019
ms.author: b-juche
ms.openlocfilehash: bd00c04ecfc211ae4ed410e886c0fe6553bea241
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827512"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Vytvoření svazku SMB pro Azure NetApp Files

Azure NetApp Files podporuje svazky NFS a SMBv3. Kapacita svazku se počítá podle zřízené kapacity fondu. V tomto článku se dozvíte, jak vytvořit svazek SMBv3. Pokud chcete vytvořit svazek NFS, přečtěte si téma [vytvoření svazku NFS pro Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Než začnete 
Musíte mít již nastavený fond kapacit.   
[Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)   
Podsíť musí být delegovaná na Azure NetApp Files.  
[Delegování podsítě na Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Požadavky na připojení služby Active Directory

 Před vytvořením svazku SMB musíte vytvořit připojení ke službě Active Directory. Požadavky na připojení služby Active Directory jsou následující: 

* Účet správce, který použijete, musí být schopný vytvářet účty počítačů v cestě organizační jednotky (OU), kterou zadáte.  

* V příslušném serveru služby Windows Active Directory (AD) musí být otevřeny správné porty.  
    Požadované porty jsou následující: 

    |     Služba           |     Přístavní     |     Protokol     |
    |-----------------------|--------------|------------------|
    |    Webové služby AD    |    9389      |    PROTOKOLU           |
    |    NÁZV                |    53        |    PROTOKOLU           |
    |    NÁZV                |    53        |    UDP           |
    |    ICMPv4             |    Není k dispozici       |    Odpověď na ozvěnu    |
    |    Sdílené           |    464       |    PROTOKOLU           |
    |    Sdílené           |    464       |    UDP           |
    |    Sdílené           |    88        |    PROTOKOLU           |
    |    Sdílené           |    88        |    UDP           |
    |    ADRESÁŘOVÝ               |    389       |    PROTOKOLU           |
    |    ADRESÁŘOVÝ               |    389       |    UDP           |
    |    ADRESÁŘOVÝ               |    3268      |    PROTOKOLU           |
    |    Název pro rozhraní NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    PROTOKOLU           |
    |    SAM/LSA            |    445       |    UDP           |
    |    protokol Secure LDAP        |    636       |    PROTOKOLU           |
    |    protokol Secure LDAP        |    3269      |    PROTOKOLU           |
    |    W32Time            |    123       |    UDP           |

* Topologie lokality pro cílovou Active Directory Domain Services musí splňovat osvědčené postupy, zejména v případě, že je nasazená síť Azure Azure NetApp Files.  

    Adresní prostor pro virtuální síť, ve které je nasazený Azure NetApp Files, musí být přidán do nové nebo existující lokality služby Active Directory (kde je řadič domény dosažitelný pomocí Azure NetApp Files nachází). 

* Zadané servery DNS musí být dosažitelné z [delegované podsítě](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) Azure NetApp Files.  

    V tématu [pokyny pro Azure NetApp Files plánování sítě](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) pro podporované topologie sítě.

    Skupiny zabezpečení sítě (skupin zabezpečení sítě) a brány firewall musí mít vhodně nakonfigurovaná pravidla umožňující požadavky na provoz služby Active Directory a DNS. 

* Azure NetApp Files delegovaná podsíť musí mít přístup ke všem řadičům domény Active Directory Domain Services (v doméně), včetně všech místních a vzdálených řadičů domény. V opačném případě může dojít k přerušení služeb.  

    Pokud máte řadiče domény, které jsou nedostupné prostřednictvím Azure NetApp Files delegované podsítě, můžete odeslat žádost o podporu Azure a změnit obor z **globálních** (výchozích) na **lokalita**.  Azure NetApp Files musí komunikovat jenom s řadiči domény v lokalitě, ve které se nachází Azure NetApp Files delegovaný adresní prostor podsítě.

    Viz [návrh topologie lokality](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) o lokalitách a službách Active Directory. 

## <a name="create-an-active-directory-connection"></a>Vytvoření připojení ke službě Active Directory

1. Z účtu NetApp klikněte na **připojení služby Active Directory**a pak klikněte na **připojit**.  

    ![Připojení ke službě Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. V okně připojit se ke službě Active Directory zadejte následující informace:

    * **Primární DNS**  
        Toto je služba DNS potřebná pro operace připojení k doméně služby Active Directory a ověřování SMB. 
    * **Sekundární DNS**   
        Toto je sekundární server DNS pro zajištění redundantních názvových služeb. 
    * **Domain**  
        Toto je název domény vašeho Active Directory Domain Services, ke které se chcete připojit.
    * **Předpona serveru SMB (účet počítače)**  
        Toto je předpona názvů pro účet počítače ve službě Active Directory, kterou Azure NetApp Files použít pro vytváření nových účtů.

        Pokud například standardní názvový server, který vaše organizace používá pro souborové servery, je NAS-01, NAS-02..., NAS-045, zadejte jako předponu "NAS". 

        Služba vytvoří ve službě Active Directory další účty počítačů podle potřeby.

    * **Cesta organizační jednotky**  
        Jedná se o cestu protokolu LDAP pro organizační jednotku (OU), kde budou vytvořeny účty počítačů serveru SMB. To znamená OU = druhá úroveň, OU = First Level. 

        Pokud používáte Azure NetApp Files s Azure Active Directory Domain Services, cesta k organizační jednotce je `OU=AADDC Computers`, když konfigurujete službu Active Directory pro svůj účet NetApp.
        
    * Přihlašovací údaje, včetně **uživatelského jména** a **hesla**

    ![Připojit ke službě Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Klikněte na tlačítko **připojit**.  

    Zobrazí se připojení služby Active Directory, které jste vytvořili.

    ![Připojení ke službě Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Přidat svazek SMB

1. V okně fondy kapacit klikněte na okno **svazky** . 

    ![Přejít na svazky](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kliknutím na **+ Přidat svazek** vytvořte svazek.  
    Zobrazí se okno vytvořit svazek.

3. V okně vytvořit svazek klikněte na **vytvořit** a zadejte informace pro následující pole:   
    * **Název svazku**      
        Zadejte název svazku, který vytváříte.   

        Název svazku musí být v rámci každého fondu kapacity jedinečný. Musí mít aspoň tři znaky dlouhé. Můžete použít jakékoli alfanumerické znaky.   

        Jako název svazku nelze použít `default`.

    * **Fond kapacit**  
        Zadejte fond kapacit, ve kterém chcete vytvořit svazek.

    * **Přidělení**  
        Zadejte velikost logického úložiště, které je přiděleno svazku.  

        V poli **dostupná kvóta** se zobrazuje množství nevyužitého místa ve zvoleném fondu kapacity, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.  

    * **Virtuální síť**  
        Zadejte službu Azure Virtual Network (VNet), ze které chcete získat přístup ke svazku.  

        Virtuální síť, kterou zadáte, musí mít přidělenou podsíť Azure NetApp Files. Služba Azure NetApp Files se dá použít jenom ze stejné virtuální sítě nebo z virtuální sítě, která se nachází ve stejné oblasti jako svazek prostřednictvím partnerského vztahu virtuálních sítí. Ke svazku z místní sítě se můžete dostat i přes Express Route.   

    * **Podsíť**  
        Zadejte podsíť, kterou chcete použít pro svazek.  
        Podsíť, kterou zadáte, musí být delegovaná na Azure NetApp Files. 
        
        Pokud jste nedelegovanou podsíť, můžete na stránce vytvořit svazek kliknout na **vytvořit novou** . Pak na stránce vytvořit podsíť zadejte informace o podsíti a vyberte možnost **Microsoft. NetApp/** Volumes pro delegování podsítě pro Azure NetApp Files. V každé virtuální síti je možné delegovat jenom jednu podsíť na Azure NetApp Files.   
 
        ![Vytvoření svazku](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Vytvořit podsíť](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klikněte na **protokol** a vyplňte následující informace:  
    * Jako typ protokolu pro svazek vyberte **SMB** . 
    * V rozevíracím seznamu vyberte připojení ke **službě Active Directory** .
    * Zadejte název sdíleného svazku do pole **název sdílené složky**.

    ![Zadat protokol SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Kliknutím na tlačítko **zkontrolovat + vytvořit** zkontrolujte podrobnosti o svazku.  Pak kliknutím na **vytvořit** vytvořte svazek SMB.

    Svazek, který jste vytvořili, se zobrazí na stránce svazky. 
 
    Svazek zdědí předplatné, skupinu prostředků, atributy umístění z fondu kapacity. Pokud chcete monitorovat stav nasazení svazku, můžete použít kartu oznámení.

## <a name="next-steps"></a>Další kroky  

* [Připojení nebo odpojení svazku pro virtuální počítače se systémem Windows nebo Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Omezení prostředků pro Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Nejčastější dotazy k protokolu SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Informace o integraci virtuální sítě pro služby Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Instalace nové doménové struktury služby Active Directory pomocí Azure CLI](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
