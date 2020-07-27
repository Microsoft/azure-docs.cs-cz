---
title: Dynamicky měnit úroveň služby svazku pro Azure NetApp Files | Microsoft Docs
description: Popisuje, jak dynamicky měnit úroveň služby svazku.
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
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: bd28f949d35d38c9e64af7ff4196aa1754fbc37a
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87172604"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Dynamické změny úrovně služby svazku

Úroveň služby existujícího svazku můžete změnit přesunutím svazku do jiného fondu kapacity, který využívá požadovanou [úroveň služby](azure-netapp-files-service-levels.md) pro daný svazek. Tato místní změna na úrovni služby pro svazek nevyžaduje migraci dat. Nemá to vliv na přístup ke svazku.  

> [!IMPORTANT] 
> Použití této funkce vyžaduje přidávání do seznamu povolených. Vyžádejte si e-mail anffeedback@microsoft.com s ID předplatného, abyste mohli požádat o tuto funkci.

Tato funkce vám umožní splnit požadavky na zatížení na vyžádání.  Existující svazek můžete změnit tak, aby používal vyšší úroveň služby pro lepší výkon, nebo použít nižší úroveň služby pro optimalizaci nákladů. Pokud je svazek aktuálně ve fondu kapacity, který používá *úroveň služby* Premium, a chcete, aby tento svazek používal úroveň služeb *Premium* , můžete ho dynamicky přesunout do fondu kapacit, který používá úroveň služeb *Premium* .  

Fond kapacit, na který chcete přesunout svazek, již existuje. Fond kapacit může obsahovat i další svazky.  Pokud chcete přesunout svazek do nového fondu kapacity, musíte před přesunutím svazku [vytvořit fond kapacit](azure-netapp-files-set-up-capacity-pool.md) .  

## <a name="considerations"></a>Požadavky

* Po přesunu svazku do jiného fondu kapacity už nebudete mít přístup k předchozím protokolům aktivit svazků a metrikám svazků. Svazek se spustí s novými protokoly aktivit a metrikami v rámci nového fondu kapacit.

* Pokud přesunete svazek do fondu kapacity vyšší úrovně služby (například přesunete z úrovně služeb *Standard* na *Premium* nebo *Ultra* ), musíte počkat aspoň sedm dní, než budete moct svazek přesunout do fondu kapacity nižší úrovně služby (například přesun z *Ultra* na *Premium* nebo *Standard*).  
Tato čekací doba se nevztahuje na případ, kdy svazek přesunete do fondu kapacit, který má stejnou úroveň služby nebo nižší úroveň služby.

## <a name="steps"></a>Kroky

1.  Na stránce svazky klikněte pravým tlačítkem na svazek, jehož úroveň služby se má změnit. Vyberte **změnit fond**.

    ![Klikněte pravým tlačítkem na svazek.](../media/azure-netapp-files/right-click-volume.png)

2. V okně změnit fond vyberte fond kapacit, do kterého chcete přesunout svazek. 

    ![Změnit fond](../media/azure-netapp-files/change-pool.png)

3.  Klikněte na **OK**.


## <a name="next-steps"></a>Další kroky  

* [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
