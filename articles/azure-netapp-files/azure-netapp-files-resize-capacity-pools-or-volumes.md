---
title: Změnit velikost fondu kapacity nebo svazku pro Azure NetApp Files | Microsoft Docs
description: Přečtěte si, jak změnit velikost fondu kapacity nebo svazku. Změna velikosti fondu kapacity změní zakoupenou kapacitu Azure NetApp Files.
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
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 869f46207b940521ee0b66b5afa9c6e2718ab04f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594474"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Změna velikosti fondu kapacity nebo svazku
Velikost fondu kapacity nebo svazku můžete podle potřeby změnit. 

## <a name="resize-the-capacity-pool"></a>Změna velikosti fondu kapacity 

Velikost fondu kapacity můžete změnit v TiB přírůstcích nebo snížení. Velikost fondu kapacity ale nemůže být menší než 4 TiB. Změna velikosti fondu kapacity změní zakoupenou kapacitu Azure NetApp Files.

1. V okně Spravovat účet NetApp klikněte na fond kapacit, u kterého chcete změnit velikost. 
2. Klikněte pravým tlačítkem myši na název fondu kapacity nebo klikněte na "..." ikona na konci řádku fondu kapacit pro zobrazení kontextové nabídky. 
3. Pro změnu velikosti nebo odstranění fondu kapacity použijte možnosti místní nabídky.

## <a name="resize-a-volume"></a>Změna velikosti svazku

Velikost svazku můžete podle potřeby změnit. Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu.

1. V okně Spravovat účet NetApp klikněte na **svazky**. 
2. Klikněte pravým tlačítkem na název svazku, u kterého chcete změnit velikost, nebo klikněte na "..." ikona na konci řádku svazku, ve kterém se zobrazí místní nabídka.
3. Pro změnu velikosti nebo odstranění svazku použijte možnosti místní nabídky.

## <a name="resize-a-cross-region-replication-destination-volume"></a>Změna velikosti cílového svazku replikace mezi oblastmi 

V relaci [replikace mezi oblastmi](cross-region-replication-introduction.md) se automaticky změní velikost cílového svazku na základě velikosti zdrojového svazku. V takovém případě nemusíte velikost cílového svazku samostatně měnit. Toto chování automatické změny velikosti se použije v případě, že jsou svazky v aktivním vztahu replikace nebo když je partnerský vztah replikace přerušený pomocí [operace opětovné synchronizace](cross-region-replication-manage-disaster-recovery.md#resync-replication). 

Následující tabulka popisuje chování při změně velikosti cílového svazku na základě [stavu zrcadlení](cross-region-replication-display-health-status.md):

|  Stav zrcadlení  | Chování při změně velikosti cílového svazku |
|-|-|
| *Zrcadleno* | Když je cílový svazek inicializovaný a je připravený pro příjem aktualizací zrcadlení, změna velikosti zdrojového svazku automaticky změní velikost cílových svazků. |
| *Loženo* | Když změníte velikost zdrojového svazku a dojde k *přerušení* stavu zrcadlení, cílový svazek se automaticky změní na velikost [operace opětovné synchronizace](cross-region-replication-manage-disaster-recovery.md#resync-replication).  |
| *Neinicializováno* | Když změníte velikost zdrojového svazku a stav zrcadlení je stále *Neinicializovaný*, změna velikosti cílového svazku se musí provést ručně. V takovém případě se doporučuje počkat na dokončení inicializace (tj. když se stav *zrcadlení zrcadlí),* aby se změnila velikost zdrojového svazku. | 

> [!IMPORTANT]
> Ujistěte se, že máte dostatečnou rezervu v fondech kapacit pro zdroj i cílové svazky replikace mezi oblastmi. Při změně velikosti zdrojového svazku se automaticky změní velikost cílového svazku. Pokud ale fond kapacit hostující cílový svazek nemá dost místa, změna velikosti zdrojového i cílového svazku se nezdaří.

## <a name="next-steps"></a>Další kroky

- [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
- [Správa fondu ručně zřizovaného kapacity QoS](manage-manual-qos-capacity-pool.md)
- [Dynamická změna úrovně služeb svazku](dynamic-change-volume-service-level.md) 