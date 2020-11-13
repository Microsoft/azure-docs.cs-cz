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
ms.date: 11/12/2020
ms.author: b-juche
ms.openlocfilehash: e5219e1c87221ade8da68c21209f41b4d6139be2
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579075"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Dynamická změna úrovně služeb svazku

> [!IMPORTANT] 
> * Registrace veřejné verze Preview pro tuto funkci je pozastavená, dokud nebudete pokračovat. 
> * Dynamická Změna úrovně služby cílového svazku replikace se v tuto chvíli nepodporuje.

Úroveň služby existujícího svazku můžete změnit přesunutím svazku do jiného fondu kapacity, který využívá požadovanou [úroveň služby](azure-netapp-files-service-levels.md) pro daný svazek. Tato místní změna na úrovni služby pro svazek nevyžaduje migraci dat. Nemá to vliv na přístup ke svazku.  

Tato funkce vám umožní splnit požadavky na zatížení na vyžádání.  Existující svazek můžete změnit tak, aby používal vyšší úroveň služby pro lepší výkon, nebo použít nižší úroveň služby pro optimalizaci nákladů. Pokud je svazek aktuálně ve fondu kapacity, který používá *úroveň služby* Premium, a chcete, aby tento svazek používal úroveň služeb *Premium* , můžete ho dynamicky přesunout do fondu kapacit, který používá úroveň služeb *Premium* .  

Fond kapacit, na který chcete přesunout svazek, již existuje. Fond kapacit může obsahovat i další svazky.  Pokud chcete přesunout svazek do nového fondu kapacity, musíte před přesunutím svazku [vytvořit fond kapacit](azure-netapp-files-set-up-capacity-pool.md) .  

## <a name="considerations"></a>Co je potřeba vzít v úvahu

* Po přesunu svazku do jiného fondu kapacity už nebudete mít přístup k předchozím protokolům aktivit svazků a metrikám svazků. Svazek se spustí s novými protokoly aktivit a metrikami v rámci nového fondu kapacit.

* Pokud přesunete svazek do fondu kapacity vyšší úrovně služby (například přesunete z úrovně služeb *Standard* na *Premium* nebo *Ultra* ), musíte počkat aspoň sedm dní, než budete moct tento svazek *znovu* přesunout do fondu kapacity nižší úrovně služby (například přesunutí z *Ultra* na *Premium* nebo *Standard* ).  
<!-- 
## Register the feature

The feature to move a volume to another capacity pool is currently in preview. If you are using this feature for the first time, you need to register the feature first.

1. Register the feature: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Check the status of the feature registration: 

    > [!NOTE]
    > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is **Registered** before continuing.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
--> 
## <a name="move-a-volume-to-another-capacity-pool"></a>Přesunout svazek do jiného fondu kapacity

1.  Na stránce svazky klikněte pravým tlačítkem na svazek, jehož úroveň služby se má změnit. Vyberte **změnit fond**.

    ![Klikněte pravým tlačítkem na svazek.](../media/azure-netapp-files/right-click-volume.png)

2. V okně změnit fond vyberte fond kapacit, do kterého chcete přesunout svazek. 

    ![Změnit fond](../media/azure-netapp-files/change-pool.png)

3.  Klikněte na **OK**.


## <a name="next-steps"></a>Další kroky  

* [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
