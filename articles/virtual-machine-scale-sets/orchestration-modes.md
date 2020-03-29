---
title: Další informace o režimech orchestrace pro škálovací sady virtuálních strojů v Azure
description: Další informace o režimech orchestrace pro škálovací sady virtuálních strojů v Azure.
author: shandilvarun
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: vashan
ms.openlocfilehash: 4a0be30f181921461ad0bacea6f18ce439d22353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279066"
---
# <a name="orchestration-mode-preview"></a>Režim Orchestrace (náhled)

Škálovací sady virtuálních počítačů poskytují logické seskupení virtuálních počítačů spravovaných platformou. Pomocí škálovacích sad vytvoříte model konfigurace virtuálního počítače, automaticky přidáte nebo odeberete další instance na základě zatížení procesoru nebo paměti a automaticky upgradujete na nejnovější verzi operačního systému. Škálovací sady tradičně umožňují vytvářet virtuální počítače pomocí modelu konfigurace virtuálního počítače poskytovaného v době vytváření škálovací sady a škálovací sada může spravovat pouze virtuální počítače, které jsou implicitně vytvořeny na základě modelu konfigurace.

Pomocí režimu orchestrace škálovací sady (preview) můžete nyní zvolit, zda má škálovací sada organizovat virtuální počítače, které jsou vytvořeny explicitně mimo model konfigurace škálovací sady, nebo instance virtuálních počítačů vytvořené implicitně na základě konfiguračního modelu. Režim orchestrace škálovací sady vám také pomůže navrhnout infrastrukturu virtuálních zařízení pro vysokou dostupnost nasazením těchto virtuálních počítačů v doménách selhání a zónách dostupnosti.


Škálovací sady virtuálních strojů budou podporovat 2 různé režimy orchestrace:

- ScaleSetVM – instance virtuálních počítačů přidané do škálovací sady jsou založeny na modelu konfigurace škálovací sady. Životní cyklus instance virtuálního počítače – vytváření, aktualizace, odstraňování – je spravován škálovací sadou.
- Virtuální počítač (virtuální počítače) – virtuální počítače vytvořené mimo škálovací sadu lze explicitně přidat do škálovací sady. 
 

> [!IMPORTANT]
> Režim orchestrace je definován při vytváření škálovací sady a nelze jej později změnit ani aktualizovat. 
> 
> Tato funkce škálovacích sad virtuálních strojů je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. 
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="orchestration-modes"></a>Režimy orchestrace

|                             | "orchestraationMode": "VM" (VirtualMachine) | "orchestraationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Konfigurační model virtuálního počítače      | Žádný                                       | Požaduje se |
| Přidání nového virtuálního virtuálního míse do škálovací sady  | Virtuální virtuální chody se explicitně přidají do škálovací sady při vytvoření virtuálního soudu. | Virtuální počítače jsou implicitně vytvořené a přidané do škálovací sady založené na modelu konfigurace virtuálního počítače, počtu instancí a pravidlech automatického škálování | |
| Odstranění virtuálního počítače                   | Virtuální aplikace musí být odstraněna jednotlivě, škálovací sada se neodstraní, pokud má nějaké virtuální aplikace v něm. | Virtuální aplikace se dá odstranit jednotlivě, odstraněníškálovací sady odstraní všechny instance virtuálních mit.  |
| Připojit nebo odpojit virtuální chod           | Nepodporuje se                              | Nepodporuje se |
| Životní cyklus instance (vytvoření pomocí odstranění) | Virtuální počítače a jejich artefakty (například disky a síťové karty) lze spravovat nezávisle. | Instance a jejich artefakty (jako jsou disky a síťové karty) jsou implicitní pro instance škálovací sady, které je vytvářejí. Nelze je odpojit nebo spravovat samostatně mimo sadu měřítek |
| Domény selhání               | Může definovat domény selhání. 2 nebo 3 na základě regionální podpory a 5 pro zónu dostupnosti. | Může definovat domény selhání od 1 do 5 |
| Aktualizační domény              | Aktualizační domény jsou automaticky mapovány na domény selhání | Aktualizační domény jsou automaticky mapovány na domény selhání |
| Zóny dostupnosti          | Podporuje místní nasazení nebo virtuální počítače v jedné zóně dostupnosti | Podporuje regionální nasazení nebo více zón dostupnosti; Může definovat strategii vyrovnávání zón |
| Automatické škálování                   | Nepodporuje se                              | Podporuje se |
| Upgrade operačního systému                  | Nepodporuje se                              | Podporuje se |
| Aktualizace modelu               | Nepodporuje se                              | Podporuje se |
| Ovládací prvek instance            | Úplné řízení virtuálního mísy. Virtuální počítače mají plně kvalifikované identifikátorURI, které podporují celou řadu možností správy virtuálních zařízení Azure (jako jsou zásady Azure, zálohování Azure a Azure Site Recovery). | Virtuální virtuální chody jsou závislé prostředky škálovací sady. Instance lze přistupovat pro správu pouze prostřednictvím škálovací sady. |
| Instance Model              | Definice modelu Microsoft.Compute/VirtualMachines. | Definice modelu Microsoft.Compute/VirtualMachineScaleSets/VirtualMachines. |
| Kapacita                    | Lze vytvořit prázdnou škálovací sadu; až 200 virtuálních disponování lze přidat do škálovací sady | Škálovací sady lze definovat s počtem instancí 0 - 1000 |
| Přesunout                        | Podporuje se                                  | Podporuje se |
| Skupina jednoho umístění == false | Nepodporuje se                          | Podporuje se |


## <a name="next-steps"></a>Další kroky

Další informace naleznete v [přehledu možností dostupnosti](availability.md).
