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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: e7e3c2eb058d3549f2e1a10ffacf01fd354fbd47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651050"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Řešení potíží s fondem kapacit

Tento článek popisuje řešení problémů, ke kterým může dojít při správě fondů kapacity. 

## <a name="error-conditions-and-resolutions"></a>Chybové stavy a řešení 

|     Chybový stav    |     Řešení    |
|-|-|
| Problémy při vytváření fondu kapacity |  Ujistěte se, že počet fondů kapacity nepřekračuje limit. Azure NetApp Files najdete v části [omezení prostředků](azure-netapp-files-resource-limits.md).  Pokud je počet nižší než limit a stále dochází k problémům, zastavte lístek podpory a zadejte název fondu kapacity. |
| Problémy s odstraněním fondu kapacity  |  Ujistěte se, že jste odebrali všechny Azure NetApp Files svazky a snímky v předplatném, kde se pokoušíte odstranit fond kapacit. <br> Pokud jste už odebrali všechny svazky a snímky a stále nemůžete odstranit fond kapacit, můžou existovat odkazy na prostředky, aniž by se na portálu zobrazovaly. V takovém případě zadejte lístek podpory a určete, že jste provedli výše uvedené doporučené kroky. |
| Vytvoření nebo změna svazku se nezdařila s `Requested throughput not available` chybou | Dostupná propustnost svazku závisí na velikosti fondu kapacity a na úrovni služby. Pokud nemáte dostatek propustnosti, měli byste zvětšit velikost fondu nebo upravit stávající propustnost svazku. | 

## <a name="next-steps"></a>Další kroky  

* [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
* [Správa fondu ručně zřizovaného kapacity QoS](manage-manual-qos-capacity-pool.md)
