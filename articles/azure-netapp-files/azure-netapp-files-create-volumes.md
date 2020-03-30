---
title: Vytvoření svazku systému souborů NFS pro soubory Azure NetApp | Dokumenty společnosti Microsoft
description: Popisuje, jak vytvořit svazek systému souborů NFS pro soubory Azure NetApp.
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
ms.date: 12/01/2019
ms.author: b-juche
ms.openlocfilehash: 9e8817f802ca1d73ca0f6bfa2b32b1b14b37d7da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274083"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Vytvoření svazku NFS pro Azure NetApp Files

Azure NetApp Files podporuje nfs (NFSv3 a NFSv4.1) a SMBv3 svazky. Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu. Tento článek ukazuje, jak vytvořit svazek nfs. Pokud chcete vytvořit svazek SMB, přečtěte si informace [o vytvoření svazku SMB pro soubory Azure NetApp](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Než začnete 
Musíte mít už nastavený fond kapacity.   
[Nastavení fondu kapacit](azure-netapp-files-set-up-capacity-pool.md)   
Podsíť musí být delegována na soubory Azure NetApp.  
[Delegování podsítě do Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>Požadavky 

* Rozhodování o tom, kterou verzi systému nfs použít  
  NFSv3 může zpracovat širokou škálu případů použití a je běžně nasazen ve většině podnikových aplikací. Měli byste ověřit, jakou verzi (NFSv3 nebo NFSv4.1) vaše aplikace vyžaduje a vytvořit svazek pomocí příslušné verze. Pokud například používáte [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave), doporučujese zamykání souborů pomocí nfsv4.1 přes NFSv3. 

* Zabezpečení  
  Podpora bitů režimu UNIX (čtení, zápis a spouštění) je k dispozici pro nfsv3 a NFSv4.1. Přístup na kořenové úrovni je vyžadován v klientovi služby NFS pro připojení svazků nfs.

* Místní podpora uživatelů/skupin a LDAP pro NFSv4.1  
  V současné době nfsv4.1 podporuje přístup root ke svazkům pouze. Viz [Konfigurace výchozí domény NFSv4.1 pro soubory Azure NetApp](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Osvědčený postup

* Měli byste se ujistit, že používáte správné pokyny pro připojení svazku.  Viz [Připojení nebo odpojení svazku pro virtuální počítače se systémem Windows nebo Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* Klient systému souborů nfs by měl být ve stejné virtuální síti nebo partnerské virtuální síti jako svazek Souborů Azure NetApp. Připojení z vnějšku virtuální sítě je podporované; však zavede další latence a snížení celkového výkonu.

* Měli byste zajistit, aby klient systému souborů NFS byl aktuální a spouštěl nejnovější aktualizace operačního systému.

## <a name="create-an-nfs-volume"></a>Vytvoření svazku nfs

1.  Klepněte na okno **Svazky** z okna Kapacitní fondy. 

    ![Přechod na svazky](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Kliknutím na **+ Přidat svazek** vytvořte svazek.  
    Zobrazí se okno Vytvořit svazek.

3.  V okně Vytvořit svazek klikněte na **Vytvořit** a poskytněte informace pro následující pole:   
    * **Název svazku**      
        Zadejte název svazku, který vytváříte.   

        Název svazku musí být jedinečný v rámci každého fondu kapacity. Musí mít alespoň tři znaky dlouhé. Můžete použít libovolné alfanumerické znaky.   

        Jako název `default` svazku nelze použít.

    * **Kapacitní fond**  
        Určete fond kapacity, do kterého chcete svazek vytvořit.

    * **Kvóta**  
        Určuje velikost logického úložiště, které je přidělené svazku.  

        Pole **Dostupná kvóta** zobrazuje množství nevyužitého místa ve zvoleném fondu kapacity, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.  

    * **Virtuální síť**  
        Zadejte virtuální síť Azure (Vnet), ze které chcete ke svazku přistupovat.  

        Zadaný virtuální síť musí mít podsíť delegovanou na soubory Azure NetApp. Služba Azure NetApp Files je přístupná jenom ze stejné virtuální sítě nebo ze virtuální sítě, která je ve stejné oblasti jako svazek prostřednictvím partnerského vztahu virtuální sítě. Ke svazku můžete přistupovat také z místní sítě prostřednictvím expresní trasy.   

    * **Podsíť**  
        Zadejte podsíť, kterou chcete pro svazek použít.  
        Zadaná podsíť musí být delegována na soubory Azure NetApp. 
        
        Pokud jste nedelegovali podsíť, můžete na stránce Vytvořit svazek kliknout na **Vytvořit nový.** Potom na stránce Vytvořit podsíť zadejte informace o podsíti a vyberte **Microsoft.NetApp/svazky** delegovat podsíť pro soubory Azure NetApp. V každé virtuální síti lze delegovat jenom jednu podsíť na soubory Azure NetApp.   
 
        ![Vytvoření svazku](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Vytvoření podsítě](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klepněte na **položku Protokol**a proveďte následující akce:  
    * Jako typ protokolu pro svazek vyberte možnost **NFS.**   
    * Určete **cestu k souboru,** která bude použita k vytvoření cesty exportu pro nový svazek. Cesta pro export slouží pro připojení svazku a přístup k němu.

        Název cesty k souboru může obsahovat pouze písmena, číslice a pomlčky („-“). Musí být dlouhý 16 až 40 znaků. 

        Cesta k souboru musí být jedinečná v rámci každého předplatného a každé oblasti. 

    * Vyberte verzi systému nfs (**NFSv3** nebo **NFSv4.1**) pro svazek.  
    * Volitelně [můžete nakonfigurovat zásady exportu pro svazek služby NFS](azure-netapp-files-configure-export-policy.md).

    ![Zadat protokol nfs](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Kliknutím na **Zkontrolovat + Vytvořit** zkontrolujte podrobnosti svazku.  Potom klepnutím na **tlačítko Vytvořit** vytvořte svazek nfs.

    Vytvořený svazek se zobrazí na stránce Svazky. 
 
    Svazek dědí atributy předplatného, skupiny prostředků a umístění z fondu kapacity. Stav nasazení svazku můžete monitorovat na kartě Oznámení.


## <a name="next-steps"></a>Další kroky  

* [Konfigurace výchozí domény NFSv4.1 pro Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Připojování nebo odpojování svazku pro virtuální počítače s Windows nebo Linuxem](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Konfigurace zásad exportu pro svazek NFS](azure-netapp-files-configure-export-policy.md)
* [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Informace o integraci virtuálních sítí pro služby Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
