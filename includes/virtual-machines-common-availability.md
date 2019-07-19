---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1582f61befb9b6d71adbfda9482175d67874ffb9
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850272"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Možnosti dostupnosti pro virtuální počítače v Azure
Tento článek obsahuje přehled funkcí dostupnosti virtuálních počítačů Azure (VM).


## <a name="availability-sets"></a>Skupiny dostupnosti
Skupina dostupnosti je logické seskupení virtuálních počítačů v rámci datového centra, které umožňuje službě Azure pochopit, jak je vaše aplikace sestavená, aby poskytovala redundanci a dostupnost. Doporučujeme, aby se v rámci skupiny dostupnosti vytvořily dva nebo víc virtuálních počítačů, které poskytují vysoce dostupné aplikace a splňovaly [99,95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Pro samotnou skupinu dostupnosti se neúčtují žádné náklady. platíte jenom za každou vytvořenou instanci virtuálního počítače. Pokud jeden virtuální počítač používá [Azure Premium SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd), platí Azure SLA pro neplánované události údržby.

Skupina dostupnosti se skládá ze dvou dalších seskupení, která chrání před selháním hardwaru a umožňují bezpečné použití aktualizací – domén selhání (doménami selhání) a aktualizačních domén (UDs). O správě dostupnosti [virtuálních počítačů s Linuxem](../articles/virtual-machines/linux/manage-availability.md) nebo [Windows](../articles/virtual-machines/windows/manage-availability.md) si můžete přečíst víc.

### <a name="fault-domains"></a>Domény selhání
Doména selhání je logická skupina hardwarových komponent, které sdílejí společný zdroj napájení a síťový přepínač (je obdobou stojanu v místním datovém centru). Když vytváříte virtuální počítače v rámci skupiny dostupnosti, platforma Azure je automaticky distribuuje do těchto domén selhání. Tento přístup omezuje dopady potenciálního selhání fyzického hardwaru, výpadků sítě nebo přerušení napájení.

### <a name="update-domains"></a>Aktualizační domény
Aktualizační doména je logická skupina hardwarových komponent, u kterých je možné provést údržbu nebo restart současně. Když vytváříte virtuální počítače v rámci skupiny dostupnosti, platforma Azure je automaticky distribuuje do těchto aktualizačních domén. Tento přístup zajišťuje, že při pravidelné údržbě Azure zůstává vždycky nejméně jedna instance vaší aplikace spuštěná. Restartování aktualizačních domén během plánované údržby nemusí probíhat sekvenčně, ale vždycky se restartuje jenom jedna aktualizační doména.

![Skupiny dostupnosti](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

### <a name="managed-disk-fault-domains"></a>Domény selhání spravovaného disku
Virtuální počítače, které používají [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md), odpovídají doménám selhání spravovaných disků (pokud se použije spravovaná skupina dostupnosti). Toto uspořádání zajišťuje, aby všechny spravované disky připojené k virtuálnímu počítači byly umístěné ve stejné doméně selhání spravovaných disků. Ve spravované skupině dostupnosti je možné vytvořit jenom virtuální počítače se spravovanými disky. Počet domén selhání spravovaných disků se liší podle oblasti – buď dvě, nebo tři domény selhání na oblast. Další informace o těchto doménách selhání spravovaného disku pro [virtuální](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) počítače se [systémem Linux nebo virtuální počítače s Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set)najdete v článku.

![Spravovaná Skupina dostupnosti](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="availability-zones"></a>Zóny dostupnosti

[Zóny dostupnosti](../articles/availability-zones/az-overview.md), alternativa k sadám dostupnosti, rozbalíte úroveň kontroly, kterou máte k dispozici pro zajištění dostupnosti aplikací a dat na vašich virtuálních počítačích. Zóna dostupnosti je fyzicky oddělená zóna v oblasti Azure. Existují tři Zóny dostupnosti na jednu podporovanou oblast Azure. Každá zóna dostupnosti má samostatný zdroj napájení, síť a chlazení. Díky navrhování vašich řešení pro použití replikovaných virtuálních počítačů v zónách můžete chránit aplikace a data před ztrátou datacentra. Pokud dojde k ohrožení jedné zóny, replikované aplikace a data jsou okamžitě k dispozici v jiné zóně. 

![Zóny dostupnosti](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Přečtěte si další informace o nasazení virtuálního počítače se [systémem Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) nebo [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) v zóně dostupnosti.


## <a name="next-steps"></a>Další postup
Teď můžete tyto funkce pro zajištění redundance a dostupnosti začít používat a vytvořit prostředí Azure. Informace o doporučených postupech najdete v tématu věnovaném [osvědčeným postupům pro zajištění dostupnosti v Azure](../articles/best-practices-availability-checklist.md).

