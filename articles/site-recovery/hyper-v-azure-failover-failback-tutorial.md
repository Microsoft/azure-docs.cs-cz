---
title: Nastavení převzetí služeb při selhání virtuálních počítačů Hyper-V do Azure v Azure Site Recovery
description: Přečtěte si, jak převzít služby při selhání virtuálních počítačů Hyper-V do Azure pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b46a2ea12a697afde8223cc3595365c1286512c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86132461"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Převzetí služeb při selhání virtuálních počítačů Hyper-V do Azure

V tomto kurzu se dozvíte, jak převzít služby při selhání virtuálních počítačů Hyper-V do Azure pomocí [Azure Site Recovery](site-recovery-overview.md). Po převzetí služeb při selhání navrátíte služby po obnovení do místní lokality, až bude dostupná. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte vlastnosti virtuálního počítače Hyper-V a zkontrolujte, jestli splňuje požadavky Azure.
> * Převzetí služeb při selhání konkrétními virtuálními počítači do Azure


Tento kurz je pátou částí série. Předpokládá se, že jste už dokončili úlohy z předchozích kurzů.    

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava místního Hyper-V](./hyper-v-prepare-on-premises-tutorial.md)
3. Nastavení zotavení po havárii pro [virtuální počítače Hyper-V](./hyper-v-azure-tutorial.md) nebo [virtuální počítače Hyper-V spravované v cloudech System Center VMM](./hyper-v-vmm-azure-tutorial.md)
4. [Provedení postupu zotavení po havárii](tutorial-dr-drill-azure.md)

[Přečtěte si o](failover-failback-overview.md#types-of-failover) různých typech převzetí služeb při selhání. Pokud chcete převzít služby při selhání více virtuálních počítačů v plánu obnovení, přečtěte si [Tento článek](site-recovery-failover.md).

## <a name="prepare-for-failover"></a>Příprava převzetí služeb při selhání 
Ujistěte se, že virtuální počítač neobsahuje žádné snímky a že je místní virtuální počítač během navracení služeb po obnovení vypnutý. Pomáhá to zajistit konzistenci dat během replikace. Během navracení služeb po obnovení místní počítač nezapínejte. 

Převzetí služeb při selhání a navrácení služeb po obnovení probíhá ve třech fázích:

1. **Převzetí služeb při selhání do Azure:** Proběhne převzetí služeb při selhání virtuálních počítačů Hyper-V z místní lokality do Azure.
2. **Navrácení služeb po obnovení do místní lokality:** Jakmile bude dostupná místní lokalita, proběhne převzetí služeb při selhání virtuálních počítačů Azure do místní lokality. Spustí se synchronizace dat z Azure do místní lokality a po dokončení se zprovozní virtuální počítače v místní lokalitě.  
3. **Zpětná replikace místních virtuálních počítačů:** Po navrácení služeb po obnovení do místní lokality se provede zpětná replikace místních virtuálních počítačů a spustí se jejich replikace do Azure.

## <a name="verify-vm-properties"></a>Ověření vlastností virtuálního počítače

Před převzetím služeb při selhání ověřte vlastnosti virtuálního počítače a ujistěte se, že splňuje [požadavky Azure](hyper-v-azure-support-matrix.md#replicated-vms).

V části **Chráněné položky** klikněte na **Replikované položky** a pak na virtuální počítač.

1. V podokně **Replikovaná položka** se zobrazí souhrn informací o virtuálním počítači, jeho stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.

1. V části **Výpočty a síť** můžete upravit název Azure, skupinu prostředků, cílovou velikost, [skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) a nastavení spravovaného disku.

1. Můžete zobrazit a upravit nastavení sítě, včetně sítě a podsítě, do které se virtuální počítače Azure umístí po převzetí služeb při selhání, a IP adresy, která se jim přiřadí.

1. V části **Disky** se zobrazí informace o operačním systému a datových discích ve virtuálním počítači.

## <a name="fail-over-to-azure"></a>Převzetí služeb při selhání do Azure

1. V **Nastavení**  >  **replikované položky** klikněte na virtuální počítač > **převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Nejnovější** bod obnovení. 
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se před aktivací převzetí služeb při selhání pokusí vypnout zdrojové virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **úlohy** .
4. Po ověření převzetí služeb při selhání klikněte na **Potvrdit**. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání:** Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.

## <a name="connect-to-failed-over-vm"></a>Připojení k virtuálnímu počítači s podporou převzetí služeb při selhání

1. Pokud se chcete připojit k virtuálním počítačům Azure po převzetí služeb při selhání pomocí protokol RDP (Remote Desktop Protocol) (RDP) a Secure Shell (SSH), [Ověřte, že jsou splněné požadavky](failover-failback-overview.md#connect-to-azure-after-failover).
2. Po převzetí služeb při selhání přejdete na virtuální počítač a ověříte ho [připojením](../virtual-machines/windows/connect-logon.md) .
3. Použijte **změnu bodu obnovení** , pokud chcete po převzetí služeb při selhání použít jiný bod obnovení. Po potvrzení převzetí služeb při selhání v dalším kroku už tato možnost nebude dostupná.
4. Po ověření vyberte **Potvrdit** a dokončete bod obnovení virtuálního počítače po převzetí služeb při selhání.
5. Po potvrzení dojde k odstranění všech dalších dostupných bodů obnovení. Tento krok dokončí převzetí služeb při selhání.

>[!TIP]
> Pokud narazíte na problémy s připojením po převzetí služeb při selhání, postupujte podle pokynů [Průvodce řešením potíží](site-recovery-failover-to-azure-troubleshoot.md).


## <a name="next-steps"></a>Další kroky

Po převzetí služeb při selhání znovu nastavte ochranu virtuálních počítačů Azure tak, aby se replikují z Azure do místního prostředí. Až budou virtuální počítače znovu chráněny a replikovány na místní lokalitu, po dokončení navrácení služeb po obnovení z Azure.
