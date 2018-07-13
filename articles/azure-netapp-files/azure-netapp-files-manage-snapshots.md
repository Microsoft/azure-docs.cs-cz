---
title: Správa snímků pomocí souborů NetApp Azure | Dokumentace Microsoftu
description: Popisuje, jak vytvořit snímek na vyžádání pro svazek nebo obnovení ze snímku na nový svazek pomocí NetApp soubory Azure.
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
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 48cb88b9815ba723d93c18caf63f33b50eea850c
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009193"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Spravovat snímky pomocí NetApp soubory Azure
Soubory NetApp Azure můžete použít k vytvoření snímku na vyžádání pro svazek nebo obnovit ze snímku do nového svazku.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Vytvořit snímek na vyžádání pro svazek
Snímky můžete vytvořit pouze na vyžádání.  Zásady snímku se momentálně nepodporují.  
1.  V okně Správa svazků, klikněte na tlačítko **snímky**, pak klikněte na tlačítko **+ přidat snímek** se vytvořit snímek na vyžádání pro svazek.

2.  V okně Nový snímek zadejte název pro nový snímek, který vytváříte.   

3. Klikněte na **OK**. 


## <a name="restore-a-snapshot-to-a-new-volume"></a>Obnovení snímku na nový svazek
V současné době můžete obnovit snímek pouze do nového svazku. 
1. Přejděte **spravovat snímky** okna v okně svazek k zobrazení seznamu snímků. 
2. Vyberte snímek, který chcete obnovit.  
3. Klikněte pravým tlačítkem na název snímku a vyberte **obnovit na nový svazek** z nabídky.  

    ![Obnovení snímku do nového svazku](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. V okně Nový svazek zadejte informace pro nový svazek:  
    * **Jméno**   
        Zadejte název svazku, který vytváříte.  
        
        Název musí být jedinečný v rámci skupiny prostředků. Musí být minimálně 3 znaky.  Může použít jakékoli alfanumerické znaky.

    * **Cesta k souboru**     
        Zadejte cestu k souboru, který se použije k vytvoření cesty export pro nový svazek. Cesta pro export slouží k připojení a přístup ke svazku.   
        
        Cíl připojení je koncový bod IP adresy pro službu systému souborů NFS. Není automaticky vygenerován.   
        
        Název cesty souboru může obsahovat písmena, číslice a pomlčky ("-") pouze. Musí být mezi 16 až 40 znaků. 

    * **Kvóta**  
        Zadejte velikost logického úložiště, který je přidělen svazku.  

        **Dostupnou kvótu** pole zobrazuje množství nevyužité místo ve zvolené kapacitu fondu, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.

    *   **Virtuální síť**  
        Zadejte virtuální síť Azure (Vnet) ze kterého chcete pro přístup ke svazku. 
        
        Virtuální síť, kterou zadáte, musí mít souborů NetApp Azure nakonfigurovaný. Služba soubory Azure NetApp je přístupný pouze z jedné virtuální sítě, která je ve stejném umístění jako svazek.  

    ![Obnovit nový svazek](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
    
5. Klikněte na **OK**.   
    Nový svazek, ke kterému je obnovení snímku se zobrazí v okně svazky.

