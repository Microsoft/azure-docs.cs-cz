---
title: Možnosti dostupnosti pro Azure Virtual Machines
description: Přečtěte si o možnostech dostupnosti pro spouštění virtuálních počítačů v Azure.
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 1ea87d40430dbf3edabd557b80ab1456b49f4605
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507870"
---
# <a name="availability-options-for-azure-virtual-machines"></a>Možnosti dostupnosti pro Azure Virtual Machines
Tento článek poskytuje přehled možností dostupnosti pro virtuální počítače Azure (VM).

## <a name="availability-zones"></a>Zóny dostupnosti

[Zóny dostupnosti](../availability-zones/az-overview.md?context=/azure/virtual-machines/context/context) rozšiřují úroveň řízení, kterou je třeba udržovat v dostupnosti aplikací a dat na virtuálních počítačích. Zóna dostupnosti je fyzicky oddělená zóna v oblasti Azure. Existují tři Zóny dostupnosti na jednu podporovanou oblast Azure. 

Každá zóna dostupnosti má samostatný zdroj napájení, síť a chlazení. Návrhem řešení pro použití replikovaných virtuálních počítačů v zónách můžete chránit aplikace a data před ztrátou datového centra. Pokud dojde k ohrožení jedné zóny, replikované aplikace a data jsou okamžitě k dispozici v jiné zóně. 

## <a name="availability-sets"></a>Skupiny dostupnosti
[Skupina dostupnosti](availability-set-overview.md) je logické seskupení virtuálních počítačů, které umožňuje Azure pochopit, jak je vaše aplikace sestavená, aby poskytovala redundanci a dostupnost. Doporučujeme, aby se v rámci skupiny dostupnosti vytvořily dva nebo víc virtuálních počítačů, které poskytují vysoce dostupné aplikace a splňovaly [99,95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Pro samotnou skupinu dostupnosti se neúčtují žádné náklady. platíte jenom za každou vytvořenou instanci virtuálního počítače.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines škálování sad 

[Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md?context=/azure/virtual-machines/context/context) vám umožní vytvořit a spravovat skupinu virtuálních počítačů s vyrovnáváním zatížení. Počet instancí virtuálních počítačů se může automaticky zvyšovat nebo snižovat v reakci na poptávku nebo podle určeného rozvrhu. Sady škálování poskytují vysokou dostupnost vašim aplikacím a umožňují centrálně spravovat, konfigurovat a aktualizovat spoustu virtuálních počítačů. Pro zajištění vysoce dostupné aplikace a pro splnění [99,95% smlouvy SLA pro Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/)doporučujeme, aby se v rámci sady škálování vytvořily dva nebo víc virtuálních počítačů. Pro samotnou sadu škálování se neúčtují žádné náklady. platíte jenom za každou vytvořenou instanci virtuálního počítače.

Virtuální počítače v sadě škálování je taky možné nasadit do jedné zóny dostupnosti nebo v regionu. Možnosti nasazení zóny dostupnosti se můžou lišit v závislosti na [režimu orchestrace](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md?context=/azure/virtual-machines/context/context).

## <a name="load-balancer"></a>Nástroj pro vyrovnávání zatížení
Kombinací [Azure Load Balancer](../load-balancer/load-balancer-overview.md) se zónou dostupnosti nebo skupinou dostupnosti získáte maximální odolnost aplikací. Azure Load Balancer rozděluje provoz mezi víc virtuálních počítačů. Azure Load Balancer je součástí virtuálních počítačů úrovně Standard. Ne všechny úrovně virtuálních počítačů zahrnují nástroj Azure Load Balancer. Další informace o vyrovnávání zatížení virtuálních počítačů najdete v tématu **Vyrovnávání zatížení virtuálních počítačů** pro [Linux](linux/tutorial-load-balancer.md) nebo [Windows](windows/tutorial-load-balancer.md).


## <a name="azure-storage-redundancy"></a>Redundance Azure Storage
Azure Storage vždycky ukládá více kopií vašich dat, takže je chráněná před plánovanými a neplánovanými událostmi, včetně přechodných selhání hardwaru, sítě nebo výpadků napájení a obrovských přirozených havárií. Redundance zajišťuje, že váš účet úložiště splňuje požadavky na dostupnost a odolnost i v případě selhání.

Při rozhodování, která možnost redundance je pro váš scénář nejvhodnější, zvažte kompromisy mezi nižšími náklady a vyšší dostupností. Mezi faktory, které vám pomůžou určit, kterou možnost redundance byste měli zvolit, patří:
- Způsob replikace dat v primární oblasti
- Bez ohledu na to, jestli se vaše data replikují do druhé oblasti, která je geograficky vzdálená v primární oblasti, aby se chránila před místními katastrofami
- Bez ohledu na to, jestli vaše aplikace vyžaduje přístup pro čtení replikovaných dat v sekundární oblasti, pokud z nějakého důvodu nebude dostupná primární oblast

Další informace najdete v tématu [Azure Storage redundance](../storage/common/storage-redundancy.md) .

## <a name="azure-site-recovery"></a>Azure Site Recovery
Jako organizace potřebujete, abyste přijali strategii pro provozní kontinuitu a zotavení po havárii (BCDR), která udržuje vaše data v bezpečí a vaše aplikace a úlohy online, když dojde k plánovaným a neplánovaným výpadkům.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) pomáhá zajistit kontinuitu podnikových procesů tím, že udržuje podnikové aplikace a úlohy spuštěné během výpadků. Site Recovery replikuje úlohy spuštěné na fyzických a virtuálních počítačích z primární lokality do sekundárního umístění. Když dojde k výpadku v primární lokalitě, převezme služby při selhání sekundární lokalita a přistupujete k aplikacím z ní. Po opětovném zprovoznění můžete navrátit služby do primární lokality.

Site Recovery může spravovat replikaci pro:
- Virtuální počítače Azure replikované mezi oblastmi Azure.
- Místní virtuální počítače, Azure Stack virtuální počítače a fyzické servery.

## <a name="next-steps"></a>Další kroky
- [Vytvoření virtuálního počítače v zóně dostupnosti](/linux/create-cli-availability-zone.md)
- [Vytvoření virtuálního počítače ve skupině dostupnosti](/linux/tutorial-availability.md)
- [Vytvoření škálovací sady virtuálních počítačů](../virtual-machine-scale-sets/quick-create-portal.md)