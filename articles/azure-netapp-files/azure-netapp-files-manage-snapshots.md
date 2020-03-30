---
title: Správa snímků pomocí souborů Azure NetApp | Dokumenty společnosti Microsoft
description: Popisuje, jak vytvořit snímky pro svazek nebo obnovit ze snímku na nový svazek pomocí souborů Azure NetApp.
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
ms.date: 03/03/2020
ms.author: b-juche
ms.openlocfilehash: 48055a774808aea86452e8410b7e717f5019d172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267905"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Správa snímků pomocí souborů Azure NetApp

Soubory Azure NetApp můžete ručně vytvořit snímek na vyžádání pro svazek nebo obnovit ze snímku na nový svazek. Služba Azure NetApp Files automaticky nevytváří snímky svazků.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Vytvoření snímku na vyžádání pro svazek

Snímky můžete vytvářet pouze na vyžádání. Funkce Snímek není aktuálně podporována.

1.  V okně Svazek klepněte na **položku Snímky**.

    ![Přechod na snímky](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Kliknutím **na + Přidat snímek** vytvořte snímek na vyžádání pro svazek.

    ![Přidat snímek](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  V okně Nový snímek zadejte název pro nový snímek, který vytváříte.   

    ![Nový snímek](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Klikněte na tlačítko **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Obnovení snímku na nový svazek

V současné době můžete obnovit snímek pouze na nový svazek. 
1. Přejděte do okna **Spravovat snímky** z okna svazku a zobrazte seznam snímků. 
2. Vyberte snímek, který chcete obnovit.  
3. Klikněte pravým tlačítkem myši na název snímku a z možnosti nabídky vyberte **Obnovit nový svazek.**  

    ![Obnovení snímku na nový svazek](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. V okně Nový svazek zadejte informace o novém svazku:  
    * **Jméno**   
        Zadejte název svazku, který vytváříte.  
        
        Název musí být v rámci skupiny prostředků jedinečný. Musí mít alespoň tři znaky dlouhé.  Může používat libovolné alfanumerické znaky.

    * **Cesta k souboru**     
        Zadejte cestu k souboru, která se použije k vytvoření cesty pro export nového svazku. Cesta pro export slouží pro připojení svazku a přístup k němu.   
        
        Cíl připojení je koncový bod IP adresy služby NFS. Generuje se automaticky.   
        
        Název cesty k souboru může obsahovat pouze písmena, číslice a pomlčky („-“). Musí být dlouhý 16 až 40 znaků. 

    * **Kvóta**  
        Určuje velikost logického úložiště, které je přidělené svazku.  

        Pole **Dostupná kvóta** zobrazuje množství nevyužitého místa ve zvoleném fondu kapacity, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.

    *   **Virtuální síť**  
        Zadejte virtuální síť Azure (Vnet), ze které chcete ke svazku přistupovat.  
        Zadaný virtuální síť musí mít podsíť delegovanou na soubory Azure NetApp. K souborům Azure NetApp můžete přistupovat jenom ze stejné sítě virtuálnísítě nebo z virtuální sítě, která je ve stejné oblasti jako svazek prostřednictvím partnerského vztahu virtuální sítě. Ke svazku můžete přistupovat z místní sítě prostřednictvím expresní trasy. 

    * **Podsíť**  
        Zadejte podsíť, kterou chcete pro svazek použít.  
        Zadaná podsíť musí být delegována na službu Azure NetApp Files. Novou podsíť můžete vytvořit tak, že v poli Podsíť vyberete **Vytvořit nový.**  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Klikněte na tlačítko **OK**.   
    Nový svazek, na který je snímek obnoven, se zobrazí v okně Svazky.

## <a name="next-steps"></a>Další kroky

[Vysvětlení hierarchie úložiště služby Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
