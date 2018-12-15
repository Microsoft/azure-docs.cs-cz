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
ms.topic: get-started-article
ms.date: 11/13/2018
ms.author: b-juche
ms.openlocfilehash: 8fc76c33055f7131444a073d2f8560e136c0701d
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413237"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Vytvoření svazku pro Azure NetApp Files

Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu.  Ve fondu kapacity můžete vytvořit víc svazků, ale celková spotřeba kapacity svazků nesmí překročit velikost fondu. 

## <a name="before-you-begin"></a>Před zahájením 
Musíte mít už nastavený fond kapacity.   
[Nastavení kapacity fondu](azure-netapp-files-set-up-capacity-pool.md)   
Podsíť je potřeba delegovat do služby soubory Azure NetApp.  
[Delegát podsítě do služby soubory Azure NetApp](azure-netapp-files-delegate-subnet.md)


## <a name="steps"></a>Kroky 
1.  Klikněte na okno **Svazky** v okně Spravovat fondy kapacity. 
2.  Kliknutím na **+ Přidat svazek** vytvořte svazek.  
    Zobrazí se okno Nový svazek.

3.  V okně Nový svazek klikněte na **Vytvořit** a zadejte informace pro následující pole:   
    * **Název**      
        Zadejte název svazku, který vytváříte.   

        Název musí být v rámci skupiny prostředků jedinečný. Musí být alespoň tři znaky.  Může používat libovolné alfanumerické znaky.

    * **Cesta k souboru**  
        Zadejte cestu k souboru, která se použije k vytvoření cesty pro export nového svazku. Cesta pro export slouží pro připojení svazku a přístup k němu.   
     
        Název cesty k souboru může obsahovat pouze písmena, číslice a pomlčky („-“). Musí být dlouhý 16 až 40 znaků.  

    * **Kvóta**  
        Určuje velikost logického úložiště, které je přidělené svazku.  

        Pole **Dostupná kvóta** zobrazuje množství nevyužitého místa ve zvoleném fondu kapacity, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.  

    * **Virtuální síť**  
        Zadejte virtuální síť Azure (Vnet), ze které chcete ke svazku přistupovat.  

        Virtuální síť, kterou zadáte, musí mít podsíť delegovat do služby soubory Azure NetApp. Služba soubory Azure NetApp přístupná pouze ze stejné virtuální síti nebo z virtuální sítě, která je ve stejné oblasti jako svazek prostřednictvím partnerského vztahu virtuální sítě. Svazek se můžete dostat taky z vaší místní sítě prostřednictvím Expressroute.   

    * **Podsíť**  
        Zadejte podsíť, kterou chcete použít pro daný svazek.  
        Podsítě, které jste zadali, je potřeba delegovat do služby soubory Azure NetApp. 
        
        Pokud jste ještě nedelegovali podsítě, můžete kliknout na **vytvořit nový** na vytvořit svazek stránky. Na stránce vytvořit podsítě, pak zadejte informace o podsíti a vyberte **Microsoft.NetApp/volumes** delegovat podsíť pro soubory Azure NetApp.    
 
        ![Nový svazek](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Vytvoření podsítě](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)


4.  Klikněte na **OK**. 
 
Svazek dědí atributy předplatného, skupiny prostředků a umístění z fondu kapacity. Stav nasazení svazku můžete monitorovat na kartě Oznámení.

## <a name="next-steps"></a>Další postup  
* [Konfigurace zásad exportu pro svazek (volitelné)](azure-netapp-files-configure-export-policy.md)
* [Další informace o integraci virtuální sítě pro služby Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

