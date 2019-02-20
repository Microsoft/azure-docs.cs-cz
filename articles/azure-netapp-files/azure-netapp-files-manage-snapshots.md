---
title: Správa snímků pomocí souborů NetApp Azure | Dokumentace Microsoftu
description: Popisuje, jak vytvářet snímky pro svazek nebo obnovení ze snímku na nový svazek pomocí NetApp soubory Azure.
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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 3c69cb076b3b23cd5149e05f1b6ee9ae1ba170a6
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430195"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Spravovat snímky pomocí NetApp soubory Azure

Soubory NetApp Azure můžete použít k vytvoření snímku na vyžádání pro svazek nebo obnovit ze snímku do nového svazku.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Vytvořit snímek na vyžádání pro svazek

Snímky můžete vytvořit pouze na vyžádání. Zásady snímku se momentálně nepodporují.

1.  V okně svazku klikněte na tlačítko **snímky**.

    ![Přejděte na snímky](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Klikněte na tlačítko **+ přidat snímek** se vytvořit snímek na vyžádání pro svazek.

    ![Přidat snímek](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  V okně Nový snímek zadejte název pro nový snímek, který vytváříte.   

    ![Nový snímek](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Klikněte na **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Obnovení snímku na nový svazek

V současné době můžete obnovit snímek pouze do nového svazku. 
1. Přejděte **spravovat snímky** okna v okně svazek k zobrazení seznamu snímků. 
2. Vyberte snímek, který chcete obnovit.  
3. Klikněte pravým tlačítkem na název snímku a vyberte **obnovit na nový svazek** z nabídky.  

    ![Obnovení snímku do nového svazku](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. V okně Nový svazek zadejte informace pro nový svazek:  
    * **Název**   
        Zadejte název svazku, který vytváříte.  
        
        Název musí být v rámci skupiny prostředků jedinečný. Musí být alespoň tři znaky.  Může používat libovolné alfanumerické znaky.

    * **Cesta k souboru**     
        Zadejte cestu k souboru, která se použije k vytvoření cesty pro export nového svazku. Cesta pro export slouží pro připojení svazku a přístup k němu.   
        
        Cíl připojení je koncový bod IP adresy služby NFS. Generuje se automaticky.   
        
        Název cesty k souboru může obsahovat pouze písmena, číslice a pomlčky („-“). Musí být dlouhý 16 až 40 znaků. 

    * **Kvóta**  
        Určuje velikost logického úložiště, které je přidělené svazku.  

        Pole **Dostupná kvóta** zobrazuje množství nevyužitého místa ve zvoleném fondu kapacity, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.

    *   **Virtuální síť**  
        Zadejte virtuální síť Azure (Vnet), ze které chcete ke svazku přistupovat.  
        Virtuální síť, kterou zadáte, musí mít podsíť delegovat do služby soubory Azure NetApp. Soubory Azure NetApp můžete přistupovat pouze z stejné virtuální síti nebo virtuální síti, která je ve stejné oblasti jako svazek prostřednictvím partnerského vztahu virtuální sítě. Přístup ke svazku z vaší místní sítě prostřednictvím Expressroute. 

    * **Podsíť**  
        Zadejte podsíť, kterou chcete použít pro daný svazek.  
        Podsítě, které jste zadali, je potřeba delegovat do služby soubory Azure NetApp. Můžete vytvořit novou podsíť tak, že vyberete **vytvořit nový** pod polem podsítě.  
<!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
-->

5. Klikněte na **OK**.   
    Nový svazek, ke kterému je obnovení snímku se zobrazí v okně svazky.

## <a name="next-steps"></a>Další postup

[Vysvětlení hierarchii úložiště souborů Azure NetApp](azure-netapp-files-understand-storage-hierarchy.md)