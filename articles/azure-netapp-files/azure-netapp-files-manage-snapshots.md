---
title: Správa snímků pomocí Azure NetApp Files | Microsoft Docs
description: Popisuje postup vytvoření snímků pro svazek nebo obnovení ze snímku na nový svazek pomocí Azure NetApp Files.
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
ms.date: 03/03/2020
ms.author: b-juche
ms.openlocfilehash: ed13c61646bd2a6672b613964507d291a69a6821
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483597"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Správa snímků s využitím služby Azure NetApp Files

Můžete použít Azure NetApp Files k ručnímu vytvoření snímku na vyžádání pro svazek nebo obnovení ze snímku na nový svazek. Služba Azure NetApp Files nevytváří automaticky snímky svazků.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Vytvoření snímku na vyžádání pro svazek

Snímky můžete vytvářet pouze na vyžádání. Zásady snímků se momentálně nepodporují.

1.  V okně svazek klikněte na **snímky**.

    ![Přejít na snímky](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Kliknutím na **+ Přidat snímek** vytvoříte snímek na vyžádání pro svazek.

    ![Přidat snímek](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  V okně Nový snímek zadejte název nového snímku, který vytváříte.   

    ![Nový snímek](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Klikněte na **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Obnovení snímku na nový svazek

V současné době můžete snímek obnovit pouze na nový svazek. 
1. V okně Volume ( **Spravovat snímky** ) v okně hlasitosti zobrazte seznam snímků. 
2. Vyberte snímek, který chcete obnovit.  
3. Klikněte pravým tlačítkem myši na název snímku a z možnosti nabídky vyberte **obnovit do nového svazku** .  

    ![Obnovit snímek na nový svazek](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. V okně Nový svazek zadejte informace o novém svazku:  
    * **Jméno**   
        Zadejte název svazku, který vytváříte.  
        
        Název musí být v rámci skupiny prostředků jedinečný. Musí mít aspoň tři znaky dlouhé.  Může používat libovolné alfanumerické znaky.

    * **Cesta k souboru**     
        Zadejte cestu k souboru, která se použije k vytvoření cesty pro export nového svazku. Cesta pro export slouží pro připojení svazku a přístup k němu.   
        
        Cíl připojení je koncový bod IP adresy služby NFS. Generuje se automaticky.   
        
        Název cesty k souboru může obsahovat pouze písmena, číslice a pomlčky („-“). Musí být dlouhý 16 až 40 znaků. 

    * **Kvóta**  
        Určuje velikost logického úložiště, které je přidělené svazku.  

        Pole **Dostupná kvóta** zobrazuje množství nevyužitého místa ve zvoleném fondu kapacity, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.

    *   **Virtuální síť**  
        Zadejte virtuální síť Azure (Vnet), ze které chcete ke svazku přistupovat.  
        Virtuální síť, kterou zadáte, musí mít přidělenou podsíť Azure NetApp Files. K Azure NetApp Files můžete přistupovat jenom ze stejné virtuální sítě nebo z virtuální sítě, která se nachází ve stejné oblasti jako svazek prostřednictvím partnerského vztahu virtuálních sítí. Ke svazku z místní sítě můžete přistupovat prostřednictvím expresní trasy. 

    * **Podsíť**  
        Zadejte podsíť, kterou chcete použít pro svazek.  
        Podsíť, kterou zadáte, musí být delegovaná na službu Azure NetApp Files. Novou podsíť můžete vytvořit tak, že vyberete **vytvořit novou** v poli podsíť.  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Klikněte na **OK**.   
    Nový svazek, ke kterému se snímek obnoví, se zobrazí v okně svazky.

## <a name="next-steps"></a>Další kroky

[Vysvětlení hierarchie úložiště služby Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
