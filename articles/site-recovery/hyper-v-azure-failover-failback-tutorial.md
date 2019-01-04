---
title: Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Hyper-V během zotavení po havárii do Azure pomocí Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak provést převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Hyper-V během zotavení po havárii do Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 03b8dd9bd0a7acc63fdae66330904142384429a8
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794205"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Hyper-V replikovaných do Azure

Tento kurz popisuje, jak provést převzetí služeb při selhání virtuálního počítače Hyper-V do Azure. Po převzetí služeb při selhání navrátíte služby po obnovení do místní lokality, až bude dostupná. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověření vlastností virtuálního počítače Hyper-V a kontrola, že odpovídá požadavkům Azure
> * Spuštění převzetí služeb při selhání do Azure
> * Navrácení služeb po obnovení z Azure do místní lokality
> * Zpětná replikace místních virtuálních počítačů a opětovné spuštění replikace do Azure

Tento kurz je pátou částí série. Předpokládá se, že jste už dokončili úlohy z předchozích kurzů.    

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava místního Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. Nastavení zotavení po havárii pro [virtuální počítače Hyper-V](tutorial-hyper-v-to-azure.md) nebo [virtuální počítače Hyper-V spravované v cloudech System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Příprava na převzetí služeb při selhání a navrácení služeb po obnovení

Ujistěte se, že virtuální počítač neobsahuje žádné snímky a že je místní virtuální počítač během navracení služeb po obnovení vypnutý. Pomáhá to zajistit konzistenci dat během replikace. Během navracení služeb po obnovení místní počítač nezapínejte. 

Převzetí služeb při selhání a navrácení služeb po obnovení probíhá ve třech fázích:

1. **Převzetí služeb při selhání do Azure**: Převzetí služeb při selhání Hyper-V virtuálních počítačů z místní lokality do Azure.
2. **Navrácení služeb po obnovení do místní**: Převzetí služeb při selhání virtuálních počítačů Azure do místní lokality, pokud je k dispozici v místní lokalitě. Spustí se synchronizace dat z Azure do místní lokality a po dokončení se zprovozní virtuální počítače v místní lokalitě.  
3. **Zpětná replikace místních virtuálních počítačů**: Po selhání zpátky do místního, zpětnou replikaci místních virtuálních počítačů ke spuštění replikace do Azure.

## <a name="verify-vm-properties"></a>Ověření vlastností virtuálního počítače

Před převzetím služeb při selhání ověřte vlastnosti virtuálního počítače a ujistěte se, že splňuje [požadavky Azure](hyper-v-azure-support-matrix.md#replicated-vms).

V části **Chráněné položky** klikněte na **Replikované položky** a pak na virtuální počítač.

2. V podokně **Replikovaná položka** se zobrazí souhrn informací o virtuálním počítači, jeho stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.

3. V části **Výpočty a síť** můžete upravit název Azure, skupinu prostředků, cílovou velikost, [skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) a nastavení spravovaného disku.

4. Můžete zobrazit a upravit nastavení sítě, včetně sítě a podsítě, do které se virtuální počítače Azure umístí po převzetí služeb při selhání, a IP adresy, která se jim přiřadí.

5. V části **Disky** se zobrazí informace o operačním systému a datových discích ve virtuálním počítači.

## <a name="failover-to-azure"></a>Převzetí služeb při selhání do Azure

1. V části **Nastavení** > **Replikované položky** klikněte na virtuální počítač a pak na **Převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Nejnovější** bod obnovení. 
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se před aktivací převzetí služeb při selhání pokusí vypnout zdrojové virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Po ověření převzetí služeb při selhání klikněte na **Potvrdit**. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> **Nepřerušujte převzetí služeb při selhání v průběhu**: Pokud zrušíte probíhá převzetí služeb při selhání zastaví, ale virtuální počítač se znovu nereplikuje.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Navrácení služeb po obnovení virtuálního počítače Azure do místní lokality a zpětná replikace místního virtuálního počítače

Operace navrácení služeb po obnovení je v podstatě převzetí služeb při selhání z Azure do místní lokality a zpětná replikace znovu spustí replikaci virtuálních počítačů z místní lokality do Azure.

1. V části **Nastavení** > **Replikované položky** klikněte na virtuální počítač a pak na **Plánované převzetí služeb při selhání**.
2. V části **Potvrdit plánované převzetí služeb při selhání** ověřte směr převzetí služeb při selhání (z Azure) a vyberte zdrojové a cílové umístění.
3. Vyberte **Před převzetím služeb při selhání synchronizovat data (synchronizovat pouze rozdílové změny)**. Tato možnost minimalizuje výpadky virtuálních počítačů, protože se synchronizace provedete bez vypnutí virtuálního počítače.
4. Zahajte převzetí služeb při selhání. Průběh převzetí služeb při selhání můžete sledovat na kartě **Úlohy**.
5. Jakmile po dokončení počáteční synchronizace dat budete připraveni vypnout virtuální počítače Azure, klikněte na **Úlohy** > název_úlohy_plánovaného_převzetí_služeb_při_selhání > **Dokončit převzetí služeb při selhání**. Tím se vypne virtuální počítač Azure, nejnovější změny se přenesou do místní lokality a spustí se místní virtuální počítač.
6. Přihlaste se k místnímu virtuálnímu počítači a ověřte, že je dostupný podle očekávání.
7. Místní virtuální počítač je teď ve stavu **Čekání na potvrzení**. Klikněte na **Potvrdit**. Tím se odstraní virtuální počítač Azure a jeho disky a místní virtuální počítač se připraví na zpětnou replikaci.
Pokud chcete spustit replikaci místního virtuálního počítače do Azure, povolte **zpětnou replikaci**. Tím se aktivuje replikace rozdílových změn, ke kterým došlo od vypnutí virtuálního počítače Azure.  
