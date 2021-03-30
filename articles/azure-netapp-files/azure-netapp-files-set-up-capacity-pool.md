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
ms.topic: how-to
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 2b52ad50854092cddd7b9e79cbeebd4a83017081
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325402"
---
# <a name="set-up-a-capacity-pool"></a>Nastavení fondu kapacity

Nastavení fondu kapacity umožňuje vytvářet v něm svazky.  

## <a name="before-you-begin"></a>Než začnete 

Musíte už mít vytvořený účet NetApp.   

[Vytvoření účtu NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Postup 

1. Přejděte do okna pro správu účtu NetApp a pak v navigačním podokně klikněte na **fondy kapacit**.  
    
    ![Přejít do fondu kapacity](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Kliknutím na **+ Přidat fondy** vytvořte nový fond kapacity.   
    Zobrazí se okno Nový fond kapacity.

3. Zadejte pro nový fond kapacity následující informace:  
   * **Název**  
     Zadejte název pro fond kapacity.  
     Název fondu kapacity musí být pro každý účet NetApp jedinečný.

   * **Úroveň služby**   
     Toto pole ukazuje cílový výkon fondu kapacity.  
     Zadejte úroveň služby pro fond kapacit: [**Ultra**](azure-netapp-files-service-levels.md#Ultra), [**Premium**](azure-netapp-files-service-levels.md#Premium)nebo [**Standard**](azure-netapp-files-service-levels.md#Standard).

    * **Hodnota**     
     Zadejte velikost fondu kapacity, kterou kupujete.        
     Minimální velikost fondu kapacity je 4 TiB. Můžete vytvořit fond s velikostí, která je násobkem 4 TiB.   

   * **Službou**   
     Určete, jestli fond kapacit má používat **Ruční** nebo **Automatický** typ QoS.  

     Informace o typech QoS najdete v tématu věnovaném informacím o hierarchii a [výkonu](azure-netapp-files-performance-considerations.md) [úložiště](azure-netapp-files-understand-storage-hierarchy.md) .  

     > [!IMPORTANT] 
     > Nastavení **typu QoS** na **Ruční** je trvalé. Ruční fond kapacit QoS nejde převést tak, aby používal automatickou technologii QoS. Fond kapacit automatické kvality technologie QoS ale můžete převést na použití ruční technologie QoS. Přečtěte si téma [Změna fondu kapacit pro použití ruční technologie QoS](manage-manual-qos-capacity-pool.md#change-to-qos).   
     > Použití ručního typu QoS pro fond kapacit vyžaduje registraci. Viz [Správa ručního fondu kapacity QoS](manage-manual-qos-capacity-pool.md#register-the-feature). 

    ![Nový fond kapacity](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Klikněte na **Vytvořit**.

## <a name="next-steps"></a>Další kroky 

- [Hierarchie úložiště](azure-netapp-files-understand-storage-hierarchy.md) 
- [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Stránka s cenami Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Správa fondu ručně zřizovaného kapacity QoS](manage-manual-qos-capacity-pool.md)
- [Delegování podsítě na službu Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
