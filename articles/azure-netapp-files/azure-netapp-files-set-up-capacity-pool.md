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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: af3738849382317eeddf8bce3d2f87e38e0fb583
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56427784"
---
# <a name="set-up-a-capacity-pool"></a>Nastavení fondu kapacity

Nastavení fondu kapacity umožňuje vytvářet v něm svazky.  

## <a name="before-you-begin"></a>Před zahájením 

Musíte už mít vytvořený účet NetApp.   

[Vytvoření účtu NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Kroky 

1. Přejděte do okna pro správu pro váš účet NetApp a pak v navigačním podokně klikněte na **kapacity fondů**.  
    
    ![Přejít na kapacitu fondu](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Kliknutím na **+ Přidat fondy** vytvořte nový fond kapacity.   
    Zobrazí se okno Nový fond kapacity.

3. Zadejte pro nový fond kapacity následující informace:  
  * **Název**  
    Zadejte název pro fond kapacity.  
    Název fondu kapacity musí být pro každý účet NetApp jedinečný.

  * **Úroveň služeb**   
    Toto pole ukazuje cílový výkon fondu kapacity.  
    Zadejte úroveň služby pro kapacitu fondu: [**Premium** ](azure-netapp-files-service-levels.md#Premium) nebo [ **standardní**](azure-netapp-files-service-levels.md#Standard).

  * **Velikost**     
    Zadejte velikost fondu kapacity, kterou kupujete.        
    Minimální velikost fondu kapacity je 4 TiB. Můžete vytvořit fond s velikostí, která je násobkem 4 TiB.   
      
    ![Nový fond kapacity](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Klikněte na **OK**.

## <a name="next-steps"></a>Další postup 

- [Úrovně služeb pro soubory Azure NetApp](azure-netapp-files-service-levels.md)
- Najdete v článku [souborů NetApp Azure stránce s cenami](https://azure.microsoft.com/pricing/details/storage/netapp/) za cenu úrovní různých služeb
- [Delegát podsítě do služby soubory Azure NetApp](azure-netapp-files-delegate-subnet.md)
