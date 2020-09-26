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
ms.date: 08/26/2020
ms.author: b-juche
ms.openlocfilehash: 9050982338c4a6096ef180b34c0d0a0dca931427
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278307"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Dynamická změna úrovně služeb svazku

Úroveň služby existujícího svazku můžete změnit přesunutím svazku do jiného fondu kapacity, který využívá požadovanou [úroveň služby](azure-netapp-files-service-levels.md) pro daný svazek. Tato místní změna na úrovni služby pro svazek nevyžaduje migraci dat. Nemá to vliv na přístup ke svazku.  

Tato funkce vám umožní splnit požadavky na zatížení na vyžádání.  Existující svazek můžete změnit tak, aby používal vyšší úroveň služby pro lepší výkon, nebo použít nižší úroveň služby pro optimalizaci nákladů. Pokud je svazek aktuálně ve fondu kapacity, který používá *úroveň služby* Premium, a chcete, aby tento svazek používal úroveň služeb *Premium* , můžete ho dynamicky přesunout do fondu kapacit, který používá úroveň služeb *Premium* .  

Fond kapacit, na který chcete přesunout svazek, již existuje. Fond kapacit může obsahovat i další svazky.  Pokud chcete přesunout svazek do nového fondu kapacity, musíte před přesunutím svazku [vytvořit fond kapacit](azure-netapp-files-set-up-capacity-pool.md) .  

## <a name="considerations"></a>Požadavky

* Po přesunu svazku do jiného fondu kapacity už nebudete mít přístup k předchozím protokolům aktivit svazků a metrikám svazků. Svazek se spustí s novými protokoly aktivit a metrikami v rámci nového fondu kapacit.

* Pokud přesunete svazek do fondu kapacity vyšší úrovně služby (například přesunete z úrovně služeb *Standard* na *Premium* nebo *Ultra* ), musíte počkat aspoň sedm dní, než budete moct tento svazek *znovu* přesunout do fondu kapacity nižší úrovně služby (například přesunutí z *Ultra* na *Premium* nebo *Standard*).  

## <a name="register-the-feature"></a>Zaregistrujte funkci.

Funkce pro přesunutí svazku do jiného fondu kapacity je momentálně ve verzi Preview. Pokud tuto funkci používáte poprvé, budete ji muset nejdřív zaregistrovat.

1. Zaregistrujte funkci: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Ověřte stav registrace funkce: 

    > [!NOTE]
    > **RegistrationState** může být ve `Registering` stavu až 60 minut, než se změní na `Registered` . Než budete pokračovat, počkejte, než se stav **zaregistruje** .

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
Můžete také použít [příkazy rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) `az feature register` a `az feature show` zaregistrovat funkci a zobrazit stav registrace. 

## <a name="move-a-volume-to-another-capacity-pool"></a>Přesunout svazek do jiného fondu kapacity

1.  Na stránce svazky klikněte pravým tlačítkem na svazek, jehož úroveň služby se má změnit. Vyberte **změnit fond**.

    ![Klikněte pravým tlačítkem na svazek.](../media/azure-netapp-files/right-click-volume.png)

2. V okně změnit fond vyberte fond kapacit, do kterého chcete přesunout svazek. 

    ![Změnit fond](../media/azure-netapp-files/change-pool.png)

3.  Klikněte na **OK**.


## <a name="next-steps"></a>Další kroky  

* [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
