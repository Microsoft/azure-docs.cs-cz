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
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 8f50b2ad34c705c8d3831d8243f136c41d750dc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60691078"
---
# <a name="set-up-a-capacity-pool"></a>Nastavení fondu kapacity

Nastavení fondu kapacity umožňuje vytvářet v něm svazky.  

## <a name="before-you-begin"></a>Než začnete 

Musíte už mít vytvořený účet NetApp.   

[Vytvoření účtu NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Kroky 

1. Přejděte do okna správy účtu NetApp a potom v navigačním podokně klikněte na **fondy kapacity**.  
    
    ![Přechod do fondu kapacit](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Kliknutím na **+ Přidat fondy** vytvořte nový fond kapacity.   
    Zobrazí se okno Nový fond kapacity.

3. Zadejte pro nový fond kapacity následující informace:  
   * **Název**  
     Zadejte název pro fond kapacity.  
     Název fondu kapacity musí být pro každý účet NetApp jedinečný.

   * **Úroveň služby**   
     Toto pole ukazuje cílový výkon fondu kapacity.  
     Zadejte úroveň služby pro fond kapacit: [**Premium**](azure-netapp-files-service-levels.md#Premium) nebo [**Standard**](azure-netapp-files-service-levels.md#Standard).

   * **Velikost**     
     Zadejte velikost fondu kapacity, kterou kupujete.        
     Minimální velikost fondu kapacity je 4 TiB. Můžete vytvořit fond s velikostí, která je násobkem 4 TiB.   
      
     ![Nový fond kapacity](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Klikněte na tlačítko **OK**.

## <a name="next-steps"></a>Další kroky 

- [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
- Ceny různých úrovní služeb najdete na [stránce s cenami souborů Azure NetApp.](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Delegování podsítě do Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
