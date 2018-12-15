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
ms.openlocfilehash: 55a1d16ce1617ecf7bc28c7c62de8557ceeea311
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53412907"
---
# <a name="set-up-a-capacity-pool"></a>Nastavení fondu kapacity
Nastavení fondu kapacity umožňuje vytvářet v něm svazky.  

## <a name="before-you-begin"></a>Před zahájením 
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

## <a name="next-steps"></a>Další postup 

[Delegát podsítě do služby soubory Azure NetApp](azure-netapp-files-delegate-subnet.md)


