---
title: Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Hyper-V replikovat do Azure pomocí Site Recovery | Dokumentace Microsoftu
description: Další informace o převzetí služeb při selhání virtuálních počítačů Hyper-V do Azure a navrácení služeb po obnovení místní lokality pomocí Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: f758939964045ed373703a211d4cbef00f0e42e7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919542"
---
# <a name="failover-and-failback-hyper-v-vms-replicated-to-azure"></a>Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Hyper-V replikovat do Azure

Tento kurz popisuje, jak převzetí služeb při selhání virtuálních počítačů Hyper-V do Azure. Poté, co jste převzetí služeb při selhání, které navrácení služeb po obnovení do místní lokality až bude k dispozici. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte vlastnosti virtuálního počítače Hyper-V ke kontrole splňovaly požadavky Azure
> * Spuštění převzetí služeb při selhání do Azure
> * Navrácení služeb po obnovení z Azure do místního
> * Zpětná replikace místních virtuálních počítačů, spusťte znovu replikaci do Azure

Tento kurz je páté kurz ze specializované série. Předpokládá, že jste už dokončili úlohy z předchozích kurzů.    

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava místního Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. Nastavení zotavení po havárii pro [virtuálních počítačů Hyper-V](tutorial-hyper-v-to-azure.md), nebo pro [virtuálních počítačů Hyper-V spravovaných v cloudech System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Příprava na převzetí služeb při selhání a navrácení služeb po obnovení

Ujistěte se, že nejsou žádné snímky ve virtuálním počítači a že místní virtuální počítač je vypnutý během navrácení služeb po obnovení. Pomáhá zajistit konzistenci dat během replikace. Nikdy nezapínat na místní virtuální počítač během navrácení služeb po obnovení. 

Převzetí služeb při selhání a navrácení služeb po obnovení mají tři fáze:

1. **Převzetí služeb při selhání do Azure**: převzetí služeb při selhání Hyper-V virtuálních počítačů z místní lokality do Azure.
2. **Navrácení služeb po obnovení do místní**: převzetí služeb při selhání virtuálních počítačů Azure do místní lokality, pokud je k dispozici v místní lokalitě. Spustí synchronizaci dat z Azure do místního a dokončení, přináší zprovoznit virtuální počítače na místní.  
3. **Zpětná replikace místních virtuálních počítačů**: po selhání zpátky do místního, zpětnou replikaci místních virtuálních počítačů ke spuštění replikace do Azure.

## <a name="verify-vm-properties"></a>Ověření vlastností virtuálního počítače

Před převzetí služeb při selhání ověřte vlastnosti virtuálního počítače a ujistěte se, že virtuální počítač splňuje s [požadavky služby Azure](hyper-v-azure-support-matrix.md#replicated-vms).

V části **Chráněné položky** klikněte na **Replikované položky** a pak na virtuální počítač.

2. V podokně **Replikovaná položka** se zobrazí souhrn informací o virtuálním počítači, jeho stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.

3. V části **Výpočty a síť** můžete upravit název Azure, skupinu prostředků, cílovou velikost, [skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) a nastavení spravovaného disku.

4. Můžete zobrazit a upravit nastavení sítě, včetně sítě a podsítě, do které se virtuální počítače Azure umístí po převzetí služeb při selhání, a IP adresy, která se jim přiřadí.

5. V části **Disky** se zobrazí informace o operačním systému a datových discích ve virtuálním počítači.

## <a name="failover-to-azure"></a>Převzetí služeb při selhání do Azure

1. V **nastavení** > **replikované položky**, klikněte na virtuální počítač > **převzetí služeb při selhání**.
2. V **převzetí služeb při selhání**, vyberte **nejnovější** bod obnovení. 
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se pokusí provést před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Po ověření, převzetí služeb při selhání, klikněte na tlačítko **potvrzení**. Odstraní se všechny dostupné body obnovení.

> [!WARNING]
> **Nepřerušujte převzetí služeb při selhání v průběhu**: Pokud můžete zrušit probíhající převzetí služeb při selhání zastaví, ale virtuální počítač se znovu nereplikuje.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Navrácení služeb po obnovení virtuálního počítače Azure na místní a naopak replikovat místní virtuální počítač

Operace navrácení služeb po obnovení je v podstatě převzetí služeb při selhání z Azure do místní lokality a zpětná replikace znovu spustí replikaci virtuálních počítačů z místní lokality do Azure.

1. V **nastavení** > **replikované položky**, klikněte na virtuální počítač > **plánované převzetí služeb při selhání**.
2. V **potvrďte plánované převzetí služeb při selhání**zkontrolujte směr převzetí služeb při selhání (z Azure) a vyberte zdrojovým a cílovým umístěním.
3. Vyberte **synchronizovat data před převzetí služeb při selhání (synchronizovat jenom rozdílové změny)**. Tato možnost minimalizuje výpadky virtuálních počítačů, protože synchronizuje bez vypnutí virtuálního počítače.
4. Zahajte převzetí služeb při selhání. Průběh převzetí služeb při selhání, můžete postupovat podle **úlohy** kartu.
5. Po počáteční data se provádí pro synchronizace a budete připraveni pro ukončení klikněte na virtuální počítače Azure **úlohy** > plánované převzetí služeb při selhání úlohy název--> **dokončení převzetí služeb při selhání**. Vypne virtuální počítač Azure, přenese nejnovější změny v místním a spustí místní virtuální počítač.
6. Přihlaste se k místní virtuální počítač, zkontrolujte, že je k dispozici podle očekávání.
7. Místní virtuální počítač je teď v **čekajícího potvrzení** stavu. Klikněte na tlačítko **potvrzení**. Odstraní virtuální počítače Azure a jeho disky a připraví místní virtuální počítač na zpětnou replikaci.
Ke spuštění replikace místních virtuálních počítačů do Azure, povolte **reverzní replikaci**. Aktivuje replikace rozdílových dat, ke kterým došlo od se vypnout virtuální počítač Azure.  
