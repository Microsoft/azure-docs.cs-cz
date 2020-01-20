---
title: Další informace o režimech orchestrace pro Virtual Machine Scale Sets v Azure
description: Přečtěte si další informace o režimech orchestrace pro Virtual Machine Scale Sets v Azure.
author: shandilvarun
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: vashan
ms.openlocfilehash: 4a0be30f181921461ad0bacea6f18ce439d22353
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279066"
---
# <a name="orchestration-mode-preview"></a>Režim orchestrace (Preview)

Sady škálování virtuálních počítačů poskytují logické seskupení virtuálních počítačů spravovaných platformou. Pomocí sady škálování můžete vytvořit konfigurační model virtuálních počítačů, automaticky přidat nebo odebrat další instance na základě procesoru nebo zatížení paměti a automaticky upgradovat na nejnovější verzi operačního systému. Sady škálování umožňují vytvářet virtuální počítače pomocí modelu konfigurace virtuálních počítačů, který je k dispozici v době vytváření sady škálování, a sada škálování může spravovat jenom virtuální počítače, které jsou implicitně vytvořené na základě modelu konfigurace.

V režimu orchestrace sady škálování (Preview) si teď můžete vybrat, jestli má sada škálování orchestrovat virtuální počítače, které se vytvářejí explicitně mimo model konfigurace sady škálování, nebo instance virtuálních počítačů vytvořené implicitně na základě Konfigurační model. Režim orchestrace sady škálování vám také pomůže navrhnout infrastrukturu virtuálních počítačů pro zajištění vysoké dostupnosti nasazením těchto virtuálních počítačů do domén selhání a Zóny dostupnosti.


Sada škálování virtuálních počítačů bude podporovat 2 odlišné režimy orchestrace:

- ScaleSetVM – instance virtuálních počítačů přidané do sady škálování jsou založené na modelu konfigurace sady škálování. Životní cyklus instance virtuálního počítače – vytvoření, aktualizace, odstranění – je spravováno sadou škálování.
- Virtuální počítače (virtuální počítače) – virtuální počítače vytvořené mimo sadu škálování je možné explicitně přidat do škálovací sady. 
 

> [!IMPORTANT]
> Režim orchestrace je definován při vytváření sady škálování a nelze ji změnit ani aktualizovat později. 
> 
> Tato funkce pro Virtual Machine Scale Sets je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. 
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="orchestration-modes"></a>Režimy orchestrace

|                             | "orchestrationMode": "VM" (VirtualMachine) | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Model konfigurace virtuálních počítačů      | Žádné                                       | Požaduje se |
| Přidání nového virtuálního počítače do sady škálování  | Virtuální počítače se při vytvoření virtuálního počítače explicitně přidávají do sady škálování. | Virtuální počítače se implicitně vytvoří a přidají do sady škálování na základě modelu konfigurace virtuálního počítače, počtu instancí a pravidel automatického škálování. | |
| Odstranit virtuální počítač                   | Virtuální počítače se musí odstranit jednotlivě, sada škálování se neodstraní, pokud obsahuje nějaké virtuální počítače. | Virtuální počítače se dají odstranit jednotlivě a odstraněním sady škálování se odstraní všechny instance virtuálních počítačů.  |
| Připojení a odpojení virtuálních počítačů           | Nepodporováno                              | Nepodporováno |
| Životní cyklus instance (vytváření prostřednictvím odstranění) | Virtuální počítače a jejich artefakty (například disky a síťové karty) se dají spravovat nezávisle. | Instance a jejich artefakty (například disky a síťové adaptéry) jsou implicitní pro instance sady škálování, které je vytvářejí. Nedají se odpojit ani spravovat odděleně mimo sadu škálování. |
| Domény selhání               | Může definovat domény selhání. 2 nebo 3 založené na místní podpoře a 5 pro zónu dostupnosti. | Může definovat domény selhání při přechodu od 1 do 5. |
| Aktualizační domény              | Aktualizace domén se automaticky namapují na domény selhání. | Aktualizace domén se automaticky namapují na domény selhání. |
| Zóny dostupnosti          | Podporuje místní nasazení nebo virtuální počítače v jedné zóně dostupnosti. | Podporuje regionální nasazení nebo více Zóny dostupnosti; Může definovat strategii vyrovnávání zón. |
| Automatické škálování                   | Nepodporováno                              | Podporováno |
| Upgrade operačního systému                  | Nepodporováno                              | Podporováno |
| Aktualizace modelu               | Nepodporováno                              | Podporováno |
| Řízení instance            | Úplný ovládací prvek VM Virtuální počítače mají plně kvalifikovaný identifikátor URI, který podporuje celou škálu možností správy virtuálních počítačů Azure (například Azure Policy, Azure Backup a Azure Site Recovery). | Virtuální počítače jsou závislé na zdrojích škálované sady. K instancím lze přistupovat pouze prostřednictvím sady škálování. |
| Model instance              | Definice modelu Microsoft. COMPUTE/VirtualMachines. | Definice modelu Microsoft. COMPUTE/VirtualMachineScaleSets/VirtualMachines. |
| Kapacita                    | Je možné vytvořit prázdnou sadu škálování. do sady škálování se dá přidat až 200 virtuálních počítačů. | Sady škálování je možné definovat s počtem instancí 0-1000. |
| Přesunout                        | Podporováno                                  | Podporováno |
| Jedna skupina umístění = = false | Nepodporováno                          | Podporováno |


## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Přehled možností dostupnosti](availability.md).
