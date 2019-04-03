---
title: Vytvoření svazku pro Azure NetApp Files | Microsoft Docs
description: Popisuje vytvoření svazku pro Azure NetApp Files.
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
ms.topic: how-to-article
ms.date: 3/17/2019
ms.author: b-juche
ms.openlocfilehash: c2dd8b636223e0484e36eed9fcc616dc6f19cf3a
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877971"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Vytvoření svazku pro Azure NetApp Files

Každá kapacita fondu může mít maximálně 500 svazky. Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu. Služba soubory Azure NetApp podporuje svazky systému souborů NFS a SMBv3. 

## <a name="before-you-begin"></a>Před zahájením 
Musíte mít už nastavený fond kapacity.   
[Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)   
Podsíť je potřeba delegovat do služby soubory Azure NetApp.  
[Delegování podsítě do Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>Vytvoření svazku systému souborů NFS

1.  Klikněte na tlačítko **svazky** okna v okně kapacity fondů. 

    ![Přejděte na svazky](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Kliknutím na **+ Přidat svazek** vytvořte svazek.  
    Vytvoření, zobrazí se okno svazku.

3.  V části Vytvoření svazku okna, klikněte na tlačítko **vytvořit** a zadejte informace pro následující pole:   
    * **Název svazku**      
        Zadejte název svazku, který vytváříte.   

        Název musí být v rámci skupiny prostředků jedinečný. Musí být alespoň tři znaky.  Můžete použít libovolný alfanumerické znaky.

    * **Fond kapacit**  
        Zadejte kapacity fondu, ve kterém chcete svazek, který se má vytvořit.

    * **Kvóta**  
        Určuje velikost logického úložiště, které je přidělené svazku.  

        Pole **Dostupná kvóta** zobrazuje množství nevyužitého místa ve zvoleném fondu kapacity, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.  

    * **Virtuální síť**  
        Zadejte virtuální síť Azure (Vnet), ze které chcete ke svazku přistupovat.  

        Virtuální síť, kterou zadáte, musí mít podsíť delegovat do služby soubory Azure NetApp. Služba soubory Azure NetApp přístupná pouze ze stejné virtuální síti nebo z virtuální sítě, která je ve stejné oblasti jako svazek prostřednictvím partnerského vztahu virtuální sítě. Svazek se můžete dostat taky z vaší místní sítě prostřednictvím Expressroute.   

    * **Podsíť**  
        Zadejte podsíť, kterou chcete použít pro daný svazek.  
        Podsítě, které jste zadali, je potřeba delegovat do služby soubory Azure NetApp. 
        
        Pokud jste ještě nedelegovali podsítě, můžete kliknout na **vytvořit nový** na vytvořit svazek stránky. Na stránce vytvořit podsítě, pak zadejte informace o podsíti a vyberte **Microsoft.NetApp/volumes** delegovat podsíť pro soubory Azure NetApp. V každé virtuální síti je možné jenom jednu podsíť delegovat do služby soubory Azure NetApp.   
 
        ![Vytvoření svazku](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Vytvoření podsítě](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klikněte na tlačítko **protokol**a pak vyberte **systému souborů NFS** jako typ protokolu pro daný svazek.   
    * Zadejte **cesta k souboru** , který se použije k vytvoření cesty export pro nový svazek. Cesta pro export slouží pro připojení svazku a přístup k němu.

        Název cesty k souboru může obsahovat pouze písmena, číslice a pomlčky („-“). Musí být dlouhý 16 až 40 znaků.  

    * Volitelně můžete [konfigurace export zásad pro svazek systému souborů NFS](azure-netapp-files-configure-export-policy.md)

    ![Zadat protokol NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Klikněte na tlačítko **zkontrolovat a vytvořit** zobrazíte podrobnosti o svazku.  Pak klikněte na tlačítko **vytvořit** vytvořit svazek systému souborů NFS.

    Na stránce svazky se zobrazí na svazek, který jste vytvořili. 
 
    Svazek dědí atributy předplatného, skupiny prostředků a umístění z fondu kapacity. Stav nasazení svazku můžete monitorovat na kartě Oznámení.

## <a name="create-an-smb-volume"></a>Vytvoření svazku SMB

Služba soubory Azure NetApp podporuje SMBv3 svazky. Je potřeba vytvořit připojení služby Active Directory před přidáním svazku SMB. 

### <a name="create-an-active-directory-connection"></a>Vytvoření připojení k službě Active Directory

1. Z vašeho účtu NetApp, klikněte na tlačítko **připojení služby Active Directory**, pak klikněte na tlačítko **připojit**.  

    ![Připojení služby Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. V okně připojit k Active Directory zadejte následující informace:

    * **Primární DNS**   
        Toto je IP adresa řadiče domény pro preferované Active Directory Domain Services pro použití s Azure NetApp Files. 
    * **Sekundární DNS**  
        Toto je IP adresa řadiče domény pro sekundární Active Directory Domain Services pro použití s Azure NetApp Files. 
    * **Domain (Doména)**  
        Toto je název domény Active Directory Domain Services, který chcete připojit.
    * **Předpona protokolu SMB serveru (účet počítače)**  
        Toto je předponu názvu účtu počítače ve službě Active Directory, který bude používat soubory Azure NetApp pro vytváření nových účtů.

        Například pokud je standardní pojmenování, který vaše organizace používá pro souborové servery NAS-01,..., NAS-02 NAS 045 a potom by zadejte "NAS" pro předponu. 

        Služba vytvoří účty další počítače ve službě Active Directory podle potřeby.

    * **Cesta k organizační jednotky**  
        Toto je cesta protokolu LDAP pro organizační jednotku (OU), kde se vytvoří účty počítače serveru protokolu SMB. To znamená, OU = druhou úroveň, OU = první úroveň. 
    * Přihlašovací údaje, včetně vašich **uživatelské jméno** a **heslo**

    ![Připojit k Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Klikněte na **Připojit**.  

    Zobrazí se připojení služby Active Directory, kterou jste vytvořili.

    ![Připojení služby Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

### <a name="add-an-smb-volume"></a>Přidání svazku SMB

1. Klikněte na tlačítko **svazky** okna v okně kapacity fondů. 

    ![Přejděte na svazky](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kliknutím na **+ Přidat svazek** vytvořte svazek.  
    Vytvoření, zobrazí se okno svazku.

3. V části Vytvoření svazku okna, klikněte na tlačítko **vytvořit** a zadejte informace pro následující pole:   
    * **Název svazku**      
        Zadejte název svazku, který vytváříte.   

        Název musí být v rámci skupiny prostředků jedinečný. Musí být alespoň tři znaky.  Můžete použít libovolný alfanumerické znaky.

    * **Cesta k souboru**  
        Zadejte cestu k souboru, která se použije k vytvoření cesty pro export nového svazku. Cesta pro export slouží pro připojení svazku a přístup k němu.   
     
        Název cesty k souboru může obsahovat pouze písmena, číslice a pomlčky („-“). Musí být dlouhý 16 až 40 znaků.  

    * **Fond kapacit**  
        Zadejte kapacity fondu, ve kterém chcete svazek, který se má vytvořit.

    * **Kvóta**  
        Určuje velikost logického úložiště, které je přidělené svazku.  

        Pole **Dostupná kvóta** zobrazuje množství nevyužitého místa ve zvoleném fondu kapacity, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.  

    * **Virtuální síť**  
        Zadejte virtuální síť Azure (Vnet), ze které chcete ke svazku přistupovat.  

        Virtuální síť, kterou zadáte, musí mít podsíť delegovat do služby soubory Azure NetApp. Služba soubory Azure NetApp přístupná pouze ze stejné virtuální síti nebo z virtuální sítě, která je ve stejné oblasti jako svazek prostřednictvím partnerského vztahu virtuální sítě. Svazek se můžete dostat taky z vaší místní sítě prostřednictvím Expressroute.   

    * **Podsíť**  
        Zadejte podsíť, kterou chcete použít pro daný svazek.  
        Podsítě, které jste zadali, je potřeba delegovat do služby soubory Azure NetApp. 
        
        Pokud jste ještě nedelegovali podsítě, můžete kliknout na **vytvořit nový** na vytvořit svazek stránky. Na stránce vytvořit podsítě, pak zadejte informace o podsíti a vyberte **Microsoft.NetApp/volumes** delegovat podsíť pro soubory Azure NetApp. V každé virtuální síti je možné jenom jednu podsíť delegovat do služby soubory Azure NetApp.   
 
        ![Vytvoření svazku](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Vytvoření podsítě](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klikněte na tlačítko **protokol** a zadejte následující informace:  
    * Vyberte **SMB** jako typ protokolu pro daný svazek. 
    * Vyberte vaše **služby Active Directory** připojení z rozevíracího seznamu.
    * Zadejte název sdíleného svazku v **název sdílené složky**.

    ![Zadejte protokol SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Klikněte na tlačítko **zkontrolovat a vytvořit** zobrazíte podrobnosti o svazku.  Pak klikněte na tlačítko **vytvořit** k vytvoření svazku SMB.

    Na stránce svazky se zobrazí na svazek, který jste vytvořili. 
 
    Svazek dědí atributy předplatného, skupiny prostředků a umístění z fondu kapacity. Stav nasazení svazku můžete monitorovat na kartě Oznámení.

## <a name="next-steps"></a>Další postup  

* [Připojování nebo odpojování svazku pro virtuální počítače s Windows nebo Linuxem](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Konfigurace zásad exportu pro svazek NFS](azure-netapp-files-configure-export-policy.md)
* [Další informace o integraci virtuální sítě pro služby Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
