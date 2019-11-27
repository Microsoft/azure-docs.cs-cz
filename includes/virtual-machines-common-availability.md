---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 06b54c3038e8b4f5879a93b696920534c2199008
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414565"
---
Tento článek obsahuje přehled funkcí dostupnosti virtuálních počítačů Azure (VM).

## <a name="high-availability"></a>Vysoká dostupnost

Zatížení se obvykle šíří napříč různými virtuálními počítači, aby se zajistila vysoká propustnost, výkon a vytvořila redundance v případě, že je virtuální počítač ovlivněný z důvodu aktualizace nebo jiné události. 

Existuje několik možností, které Azure poskytuje k dosažení vysoké dostupnosti. Nejdřív si budeme mluvit o základních konstrukcích. 

### <a name="availability-zones"></a>Zóny dostupnosti

[Zóny dostupnosti](../articles/availability-zones/az-overview.md) rozšiřují úroveň řízení, kterou máte k dispozici, abyste zachovali dostupnost aplikací a dat na vašich virtuálních počítačích. Zóna dostupnosti je fyzicky oddělená zóna v oblasti Azure. Existují tři Zóny dostupnosti na jednu podporovanou oblast Azure. 

Každá zóna dostupnosti má samostatný zdroj napájení, síť a chlazení. Díky navrhování vašich řešení pro použití replikovaných virtuálních počítačů v zónách můžete chránit aplikace a data před ztrátou datacentra. Pokud dojde k ohrožení jedné zóny, replikované aplikace a data jsou okamžitě k dispozici v jiné zóně. 

![Zóny dostupnosti](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Přečtěte si další informace o nasazení virtuálního počítače se [systémem Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) nebo [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) v zóně dostupnosti.


### <a name="fault-domains"></a>Domény selhání

Doména selhání je logická skupina hardwarových komponent, které sdílejí společný zdroj napájení a síťový přepínač (je obdobou stojanu v místním datovém centru). 

### <a name="update-domains"></a>Aktualizační domény

Aktualizační doména je logická skupina hardwarových komponent, u kterých je možné provést údržbu nebo restart současně. 

Tento přístup zajišťuje, že při pravidelné údržbě Azure zůstává vždycky nejméně jedna instance vaší aplikace spuštěná. Restartování aktualizačních domén nemusí během údržby pokračovat sekvenčně, ale v jednu chvíli se restartuje jenom jedna aktualizační doména.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines škálování sad 

Azure Virtual Machine Scale Sets vám umožní vytvořit a spravovat skupinu virtuálních počítačů s vyrovnáváním zatížení. Počet instancí virtuálních počítačů se může automaticky zvyšovat nebo snižovat s ohledem na požadavky nebo definovaný plán. Sady škálování poskytují vysokou dostupnost vašim aplikacím a umožňují centrálně spravovat, konfigurovat a aktualizovat spoustu virtuálních počítačů. Pro zajištění vysoce dostupné aplikace a pro splnění [99,95% smlouvy SLA pro Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/)doporučujeme, aby se v rámci sady škálování vytvořily dva nebo víc virtuálních počítačů. Pro samotnou sadu škálování se neúčtují žádné náklady. platíte jenom za každou vytvořenou instanci virtuálního počítače. Pokud jeden virtuální počítač používá [Azure Premium SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd), platí Azure SLA pro neplánované události údržby. Virtuální počítače v sadě škálování se dají nasadit mezi více aktualizačními doménami a doménami selhání, aby se maximalizovala dostupnost a odolnost proti výpadkům kvůli výpadkům datového centra a plánovaným nebo neplánovaným událostem údržby. Virtuální počítače v sadě škálování je taky možné nasadit do jedné zóny dostupnosti nebo v regionu. Možnosti nasazení zóny dostupnosti se můžou lišit v závislosti na režimu orchestrace.

### <a name="preview-orchestration-mode-preview"></a>Preview: režim orchestrace Preview
Sada škálování virtuálních počítačů umožňuje zadat režim orchestrace.  S režimem orchestrace sady virtuálních počítačů (Preview) můžete teď zvolit, jestli má sada škálování orchestrovat virtuální počítače, které se vytvářejí explicitně mimo model konfigurace sady škálování, nebo instance virtuálních počítačů vytvořené implicitně. na základě modelu konfigurace. Vyberte režim orchestrace, který model orchestrace virtuálních počítačů umožňuje seskupit explicitně definované Virtual Machines společně v oblasti nebo v zóně dostupnosti. Virtuální počítače nasazené v zóně dostupnosti nabízí izolaci na virtuálních počítačích, které jsou svázané s hranicí zóny dostupnosti, a nepodléhají jakýmkoli selháním, ke kterým může dojít v jiné zóně dostupnosti v oblasti. 

|   | "orchestrationMode": "VM" (VirtualMachine)| "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| Model konfigurace virtuálních počítačů| Žádné. VirtualMachineProfile není definován v modelu sady škálování. | Povinná hodnota. VirtualMachineProfile se naplní v modelu sady škálování. |
| Přidání nového virtuálního počítače do sady škálování| Virtuální počítače se při vytvoření virtuálního počítače explicitně přidávají do sady škálování. | Virtuální počítače se implicitně vytvoří a přidají do sady škálování na základě modelu konfigurace virtuálního počítače, počtu instancí a pravidel automatického škálování. |
| Zóny dostupnosti| Podporuje místní nasazení nebo virtuální počítače v jedné zóně dostupnosti.| Podporuje regionální nasazení nebo více Zóny dostupnosti; Může definovat strategii vyrovnávání zón. |
| Domény selhání| Může definovat počet domén selhání. 2 nebo 3 založené na místní podpoře a 5 pro zónu dostupnosti. Přiřazená doména selhání virtuálního počítače se uchová s životním cyklem virtuálních počítačů, včetně zrušení přidělení a restartování. | Může definovat 1, 2 nebo 3 domény selhání pro nasazení mimo oblast a 5 pro nasazení zón dostupnosti. Přiřazená doména selhání virtuálního počítače netrvala v životním cyklu virtuálních počítačů, virtuálním počítačům se v době přidělení přiřadí doména selhání. |
| Aktualizační domény| Není k dispozici. Aktualizace domén se automaticky namapují na domény selhání.| Není k dispozici. Aktualizace domén se automaticky namapují na domény selhání. |

**Domény selhání a aktualizační domény**

Služba Virtual Machine Scale Sets zjednodušuje návrh vysoké dostupnosti tím, že zarovnává domény selhání a aktualizační domény. Pro sadu škálování budete muset definovat jenom počet domén selhání. Počet domén selhání dostupných pro sadu škálování se může v jednotlivých oblastech lišit. Podívejte se [na počet domén selhání na oblast](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#number-of-fault-domains-per-region).


## <a name="availability-sets"></a>Skupiny dostupnosti
Skupina dostupnosti je logické seskupení virtuálních počítačů v rámci datového centra, které umožňuje službě Azure pochopit, jak je vaše aplikace sestavená, aby poskytovala redundanci a dostupnost. Doporučujeme, aby se v rámci skupiny dostupnosti vytvořily dva nebo víc virtuálních počítačů, které poskytují vysoce dostupné aplikace a splňovaly [99,95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Pro samotnou skupinu dostupnosti se neúčtují žádné náklady. platíte jenom za každou vytvořenou instanci virtuálního počítače. Pokud jeden virtuální počítač používá [Azure Premium SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd), platí Azure SLA pro neplánované události údržby.

Ve skupině dostupnosti jsou virtuální počítače automaticky distribuované napříč těmito doménami selhání. Tento přístup omezuje dopady potenciálního selhání fyzického hardwaru, výpadků sítě nebo přerušení napájení.

Virtuální počítače, které používají [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md), odpovídají doménám selhání spravovaných disků (pokud se použije spravovaná skupina dostupnosti). Toto uspořádání zajišťuje, aby všechny spravované disky připojené k virtuálnímu počítači byly umístěné ve stejné doméně selhání spravovaných disků. 

Ve spravované skupině dostupnosti je možné vytvořit jenom virtuální počítače se spravovanými disky. Počet domén selhání spravovaných disků se liší podle oblasti – buď dvě, nebo tři domény selhání na oblast. Další informace o těchto doménách selhání spravovaného disku pro [virtuální](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) počítače se [systémem Linux nebo virtuální počítače s Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set)najdete v článku.

![Spravovaná Skupina dostupnosti](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Virtuální počítače v rámci skupiny dostupnosti jsou taky automaticky distribuované napříč aktualizačními doménami. 

![Skupiny dostupnosti](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Další kroky
Teď můžete tyto funkce pro zajištění redundance a dostupnosti začít používat a vytvořit prostředí Azure. Informace o doporučených postupech najdete v tématu věnovaném [osvědčeným postupům pro zajištění dostupnosti v Azure](/azure/architecture/checklist/resiliency-per-service).

