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
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 42e475f4da2102bb8b010daec6e6451ba7f13848
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011088"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Vytvoření svazku pro Azure NetApp Files

Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu.  Ve fondu kapacity můžete vytvořit víc svazků, ale celková spotřeba kapacity svazků nesmí překročit velikost fondu. 

## <a name="before-you-begin"></a>Než začnete 
Musíte mít už nastavený fond kapacity.  

[Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)

## <a name="steps"></a>Kroky 
1.  Klikněte na okno **Svazky** v okně Spravovat fondy kapacity. 
2.  Kliknutím na **+ Přidat svazek** vytvořte svazek.  
    Zobrazí se okno Nový svazek.

3.  V okně Nový svazek klikněte na **Vytvořit** a zadejte informace pro následující pole:   
    * **Název**      
        Zadejte název svazku, který vytváříte.   
        Název musí být v rámci skupiny prostředků jedinečný. Musí být dlouhý aspoň 3 znaky.  Může používat libovolné alfanumerické znaky.

    * **Cesta k souboru**  
        Zadejte cestu k souboru, která se použije k vytvoření cesty pro export nového svazku. Cesta pro export slouží pro připojení svazku a přístup k němu.   
        Cíl připojení je koncový bod IP adresy služby NFS. Generuje se automaticky.    
        Název cesty k souboru může obsahovat pouze písmena, číslice a pomlčky („-“). Musí být dlouhý 16 až 40 znaků.  

    * **Kvóta**  
        Určuje velikost logického úložiště, které je přidělené svazku.  
        Pole **Dostupná kvóta** zobrazuje množství nevyužitého místa ve zvoleném fondu kapacity, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.  

    * **Virtuální síť**  
        Zadejte virtuální síť Azure (Vnet), ze které chcete ke svazku přistupovat. Zadaná virtuální síť musí mít nakonfigurovanou službu Azure NetApp Files. Ke službě Azure NetApp Files je možné přistupovat pouze z virtuální sítě, která je ve stejném umístění jako svazek.   

    ![Nový svazek](../media/azure-netapp-files/azure-netapp-files-new-volume.png)

4.  Klikněte na **OK**. 
 
Svazek dědí atributy předplatného, skupiny prostředků a umístění z fondu kapacity. Stav nasazení svazku můžete monitorovat na kartě Oznámení.

## <a name="next-steps"></a>Další kroky  
[Konfigurace zásad exportu pro svazek (volitelné)](azure-netapp-files-configure-export-policy.md)

