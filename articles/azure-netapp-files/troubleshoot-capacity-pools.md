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
ms.openlocfilehash: 0b5558501042dd7816202ea05b3a332b23400ff4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342024"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Řešení potíží s fondem kapacit

Tento článek popisuje řešení problémů, ke kterým může dojít při správě fondů kapacity. 

## <a name="issues-creating-a-capacity-pool"></a>Problémy při vytváření fondu kapacity

Ujistěte se, že počet fondů kapacity nepřekračuje limit. Azure NetApp Files najdete v části [omezení prostředků](azure-netapp-files-resource-limits.md).  Pokud je počet nižší než limit a stále dochází k problémům, zastavte lístek podpory a zadejte název fondu kapacity.

## <a name="issues-deleting-a-capacity-pool"></a>Problémy s odstraněním fondu kapacity

Ujistěte se, že jste odebrali všechny Azure NetApp Files svazky a snímky v předplatném, kde se pokoušíte odstranit fond kapacit.   

Pokud jste už odebrali všechny svazky a snímky a stále nemůžete fond kapacit odstranit, můžou existovat odkazy na prostředky, aniž by se na portálu zobrazovaly. V takovém případě zadejte lístek podpory a určete, že jste provedli výše uvedené doporučené kroky. 

## <a name="volume-creation-or-modification-fails-with-requested-throughput-not-available-error"></a>Vytvoření nebo změna svazku se nezdařila s chybou "požadovaná propustnost není k dispozici"

Dostupná propustnost svazku závisí na velikosti fondu kapacity a na úrovni služby. Pokud nemáte dostatek propustnosti, měli byste zvětšit velikost fondu nebo upravit stávající propustnost svazku.


## <a name="next-steps"></a>Další kroky  

* [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
* [Správa ručního fondu kapacity QoS](manage-manual-qos-capacity-pool.md)
