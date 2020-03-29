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
ms.openlocfilehash: 4860dcac666f790fed199536338e50a967113c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76748925"
---
Tento článek poskytuje přehled funkcí dostupnosti virtuálních počítačů Azure (VMs).

## <a name="high-availability"></a>Vysoká dostupnost

Úlohy jsou obvykle rozloženy na příčce různých virtuálních počítačů, aby se dosáhlo vysoké propustnosti, výkonu a vytvořilo se redundance v případě, že je virtuální počítač ovlivněn kvůli aktualizaci nebo jiné události. 

Existuje několik možností, které Azure poskytuje k dosažení vysoké dostupnosti. Nejprve si promluvme o základních konstrukcích. 

### <a name="availability-zones"></a>Zóny dostupnosti

[Zóny dostupnosti](../articles/availability-zones/az-overview.md) rozšiřují úroveň kontroly, kterou potřebujete k zachování dostupnosti aplikací a dat na virtuálních počítačích. Zóna dostupnosti je fyzicky samostatná zóna v rámci oblasti Azure. V podporované oblasti Azure jsou tři zóny dostupnosti. 

Každá zóna dostupnosti má samostatný zdroj napájení, síť a chlazení. Tím, že navrhnete řešení pro použití replikovaných virtuálních počítačů v zónách, můžete chránit vaše aplikace a data před ztrátou datového centra. Pokud dojde k ohrožení zabezpečení jedné zóny, replikované aplikace a data budou okamžitě k dispozici v jiné zóně. 

![Zóny dostupnosti](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Přečtěte si další informace o nasazení virtuálního počítače [se systémem Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) nebo [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) v zóně dostupnosti.


### <a name="fault-domains"></a>Domény selhání

Doména selhání je logická skupina hardwarových komponent, které sdílejí společný zdroj napájení a síťový přepínač (je obdobou stojanu v místním datovém centru). 

### <a name="update-domains"></a>Aktualizační domény

Aktualizační doména je logická skupina hardwarových komponent, u kterých je možné provést údržbu nebo restart současně. 

Tento přístup zajišťuje, že při pravidelné údržbě Azure zůstává vždycky nejméně jedna instance vaší aplikace spuštěná. Pořadí restartování aktualizačních domén nemusí během údržby pokračovat postupně, ale současně je restartována pouze jedna aktualizační doména.


## <a name="virtual-machines-scale-sets"></a>Škálovací sady virtuálních počítačů 

Škálovací sady virtuálních strojů Azure umožňují vytvářet a spravovat skupinu virtuálních počítačů s vyrovnáváním zatížení. Počet instancí virtuálních počítačů se může automaticky zvyšovat nebo snižovat s ohledem na požadavky nebo definovaný plán. Škálovací sady poskytují vysokou dostupnost vašim aplikacím a umožňují centrální správu, konfiguraci a aktualizaci mnoha virtuálních počítačů. Doporučujeme, aby se dva nebo více virtuálních počítačů vytvořily v rámci škálovací sady, aby poskytovaly vysoce dostupnou aplikaci a splňovaly [99,95% azure sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Neexistuje žádné náklady na škálovací sadu sám, platíte jenom za každou instanci virtuálního virtuálního serveru, kterou vytvoříte. Když jeden virtuální počítač používá [Azure premium SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd), Azure SLA platí pro neplánované události údržby. Virtuální počítače ve škálovací sadě lze nasadit na více aktualizačních domén a domén selhání, aby se maximalizovala dostupnost a odolnost vůči výpadkům z důvodu výpadků datových center a plánovaným nebo neplánovaným událostem údržby. Virtuální počítače ve škálovací sadě lze také nasadit do jedné zóny dostupnosti nebo regionálně. Možnosti nasazení zóny dostupnosti se mohou lišit v závislosti na režimu orchestrace.

### <a name="preview-orchestration-mode-preview"></a>Náhled: Náhled režimu Orchestrace
Škálovací sady virtuálních počítačů umožňují určit režim orchestrace.  Pomocí režimu orchestrace sady škálování virtuálních počítačů (preview) můžete nyní zvolit, zda má škálovací sada orchestrovat virtuální počítače, které jsou vytvořeny explicitně mimo model konfigurace škálovací sady, nebo instance virtuálních počítačů vytvořené implicitně na základě konfiguračního modelu. Zvolte režim orchestrace, který model orchestrace virtuálního počítače umožňuje seskupit explicitně definované virtuální počítače v oblasti nebo v zóně dostupnosti. Virtuální počítače nasazené v zóně dostupnosti poskytuje zónovou izolaci virtuálních počítačů, protože jsou vázány na hranici zóny dostupnosti a nepodléhají žádným chybám, ke kterým může dojít v jiné zóně dostupnosti v oblasti. 

|   | "orchestraationMode": "VM" (VirtualMachine)| "orchestraationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| Konfigurační model virtuálního počítače| Žádné. VirtualMachineProfile není definován v modelu škálovací sady. | Povinná hodnota. VirtualMachineProfile je naplněn v modelu škálovací sady. |
| Přidání nového virtuálního virtuálního míse do škálovací sady| Virtuální virtuální chody se explicitně přidají do škálovací sady při vytvoření virtuálního soudu. | Virtuální počítače se implicitně vytvoří a přidají do škálovací sady založené na modelu konfigurace virtuálního počítače, počtu instancí a pravidlech automatického škálování. |
| Zóny dostupnosti| Podporuje místní nasazení nebo virtuální počítače v jedné zóně dostupnosti| Podporuje regionální nasazení nebo více zón dostupnosti; Může definovat strategii vyrovnávání zón |
| Domény selhání| Může definovat počet domén selhání. 2 nebo 3 na základě regionální podpory a 5 pro zónu dostupnosti. Přiřazená doména selhání virtuálního počítače bude přetrvávat s životním cyklem virtuálního počítače, včetně navrátit a restartovat. | Můžete definovat 1, 2 nebo 3 domény selhání pro nasazení mimo zónu a 5 pro nasazení zóny dostupnosti. Přiřazená doména selhání virtuálního počítače nepřetrvává s životním cyklem virtuálních počítačů, virtuálním počítačům je přiřazena doména selhání v době přidělení. |
| Aktualizační domény| Není k dispozici. Aktualizační domény jsou automaticky mapovány na domény selhání| Není k dispozici. Aktualizační domény jsou automaticky mapovány na domény selhání |

**Domény selhání a aktualizační domény**

Škálovací sady virtuálních strojů zjednodušují navrhování pro vysokou dostupnost zarovnáním domén selhání a aktualizačních domén. Budete muset definovat pouze počet domén selhání pro škálovací sadu. Počet domén selhání, které jsou k dispozici pro škálovací sady, se může lišit podle oblasti. Viz [Správa dostupnosti virtuálních počítačů v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).


## <a name="availability-sets"></a>Skupiny dostupnosti
Sada dostupnosti je logické seskupení virtuálních počítačů v rámci datového centra, které umožňuje Azure pochopit, jak je vaše aplikace sestavena tak, aby poskytovala redundanci a dostupnost. Doporučujeme, aby se v rámci sady dostupnosti vytvořily dva nebo více virtuálních počítačích, které poskytují vysoce dostupnou aplikaci a splňují [99,95% sla Azure .](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Neexistuje žádné náklady na sadu dostupnosti sám, platíte jenom za každou instanci virtuálního virtuálního soudu, který vytvoříte. Když jeden virtuální počítač používá [Azure premium SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd), Azure SLA platí pro neplánované události údržby.

V sadě dostupnosti jsou virtuální servery automaticky distribuovány v těchto doménách selhání. Tento přístup omezuje dopady potenciálního selhání fyzického hardwaru, výpadků sítě nebo přerušení napájení.

Virtuální počítače, které používají [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md), odpovídají doménám selhání spravovaných disků (pokud se použije spravovaná skupina dostupnosti). Toto uspořádání zajišťuje, aby všechny spravované disky připojené k virtuálnímu počítači byly umístěné ve stejné doméně selhání spravovaných disků. 

Ve spravované skupině dostupnosti je možné vytvořit jenom virtuální počítače se spravovanými disky. Počet domén selhání spravovaných disků se liší podle oblasti – buď dvě, nebo tři domény selhání na oblast. Další informace o těchto doménách selhání spravovaného disku pro [virtuální počítače s Linuxem](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) nebo [virtuální počítače se systémem Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Spravovaná sada dostupnosti](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Virtuální servery v rámci skupiny dostupnosti se také automaticky distribuují mezi aktualizačními doménami. 

![Skupiny dostupnosti](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Další kroky
Teď můžete tyto funkce pro zajištění redundance a dostupnosti začít používat a vytvořit prostředí Azure. Informace o doporučených postupech najdete v tématu věnovaném [osvědčeným postupům pro zajištění dostupnosti v Azure](/azure/architecture/checklist/resiliency-per-service).

