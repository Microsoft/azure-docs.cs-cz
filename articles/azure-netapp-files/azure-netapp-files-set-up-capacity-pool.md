---
title: Nastavení fondu kapacity pro Azure NetApp Files | Microsoft Docs
description: Popisuje, jak nastavit fond kapacity tak, aby bylo možné v něm vytvářet svazky.
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
ms.openlocfilehash: 0e9203f5b4e2a9043e242b804c82017cf6fc3ee1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010799"
---
# <a name="set-up-a-capacity-pool"></a>Nastavení fondu kapacity
Nastavení fondu kapacity umožňuje vytvářet v něm svazky.  

## <a name="before-you-begin"></a>Než začnete 
Musíte už mít vytvořený účet NetApp.   

[Vytvoření účtu NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Kroky 

1. Přejděte do okna pro správu pro váš účet NetApp a pak v navigačním podokně vyberte **Fondy kapacity**.

2. Kliknutím na **+ Přidat fondy** vytvořte nový fond kapacity.   
    Zobrazí se okno Nový fond kapacity.

3. Zadejte pro nový fond kapacity následující informace:  
  * **Název**  
    Zadejte název pro fond kapacity.  
    Název fondu kapacity musí být pro každý účet NetApp jedinečný.

  * **Úroveň služeb**   
    Toto pole ukazuje cílový výkon fondu kapacity.  
    V současné době je k dispozici jenom úroveň služeb Premium. 

  *  **Velikost**     
      Zadejte velikost fondu kapacity, kterou kupujete.        
      Minimální velikost fondu kapacity je 4 TiB. Můžete vytvořit fond s velikostí, která je násobkem 4 TiB.   
      
      ![Nový fond kapacity](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Klikněte na **OK**.

## <a name="next-steps"></a>Další kroky 

1. [Vytvoření svazku pro Azure NetApp Files](azure-netapp-files-create-volumes.md)
2. [Konfigurace zásad exportu pro svazek (volitelné)](azure-netapp-files-configure-export-policy.md)

