---
title: Řešení potíží s fondem kapacit pro Azure NetApp Files | Microsoft Docs
description: Popisuje potenciální problémy, které můžete mít při správě fondů kapacity a poskytuje řešení pro tyto problémy.
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
ms.topic: troubleshooting
ms.date: 01/14/2021
ms.author: b-juche
ms.openlocfilehash: 759759b67582b241d0bab1e043dd15e54a804faf
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251535"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Řešení potíží s fondy kapacity

Tento článek popisuje řešení problémů, ke kterým může dojít při správě fondů kapacity, včetně operace změny fondu. 

## <a name="issues-managing-a-capacity-pool"></a>Problémy se správou fondu kapacity 

|     Chybový stav    |     Řešení    |
|-|-|
| Problémy při vytváření fondu kapacity |  Ujistěte se, že počet fondů kapacity nepřekračuje limit. Azure NetApp Files najdete v části [omezení prostředků](azure-netapp-files-resource-limits.md).  Pokud je počet nižší než limit a stále dochází k problémům, zastavte lístek podpory a zadejte název fondu kapacity. |
| Problémy s odstraněním fondu kapacity  |  Ujistěte se, že jste odebrali všechny Azure NetApp Files svazky a snímky v předplatném, kde se pokoušíte odstranit fond kapacit. <br> Pokud jste už odebrali všechny svazky a snímky a stále nemůžete odstranit fond kapacit, můžou existovat odkazy na prostředky, aniž by se na portálu zobrazovaly. V takovém případě zadejte lístek podpory a určete, že jste provedli výše uvedené doporučené kroky. |
| Vytvoření nebo změna svazku se nezdařila s `Requested throughput not available` chybou | Dostupná propustnost svazku závisí na velikosti fondu kapacity a na úrovni služby. Pokud nemáte dostatek propustnosti, měli byste zvětšit velikost fondu nebo upravit stávající propustnost svazku. | 

## <a name="issues-when-changing-the-capacity-pool-of-a-volume"></a>Problémy při změně fondu kapacity svazku 

|     Chybový stav    |     Řešení    |
|-|-|
| Změna fondu kapacit pro svazek není povolená. | Je možné, že ještě nemáte oprávnění k použití této funkce. <br> Funkce pro přesunutí svazku do jiného fondu kapacity je momentálně ve verzi Preview. Pokud tuto funkci používáte poprvé, budete ji muset nejdřív zaregistrovat a nastavit `-FeatureName ANFTierChange` . Projděte si postup registrace v tématu [dynamické Změna úrovně služby svazku](dynamic-change-volume-service-level.md). |
| Velikost fondu kapacity je pro celkovou velikost svazku příliš malá. |  Chyba je způsobena tím, že cílový fond kapacity nemá dostupnou kapacitu pro přesouvaný svazek.  <br> Zvětšete velikost cílového fondu nebo vyberte jiný fond, který je větší.  Podívejte [se na téma Změna velikosti fondu kapacity nebo svazku](azure-netapp-files-resize-capacity-pools-or-volumes.md).   |
|  Změnu fondu nelze dokončit, protože `'{source pool name}'` v cílovém fondu již existuje svazek s názvem. `'{target pool name}'` | K této chybě dochází, protože svazek se stejným názvem už v cílovém fondu kapacity existuje.  Vyberte jiný fond kapacit, který nemá svazek se stejným názvem.   | 

## <a name="next-steps"></a>Další kroky  

* [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
* [Správa fondu ručně zřizovaného kapacity QoS](manage-manual-qos-capacity-pool.md)
* [Dynamická změna úrovně služeb svazku](dynamic-change-volume-service-level.md)
* [Změna velikosti fondu kapacity nebo svazku](azure-netapp-files-resize-capacity-pools-or-volumes.md)
