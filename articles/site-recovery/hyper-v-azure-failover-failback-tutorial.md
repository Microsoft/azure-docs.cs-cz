---
title: Nastavení převzetí služeb při selhání virtuálních počítačů Hyper-V do Azure v Azure Site Recovery
description: Zjistěte, jak si pomocí Azure Site Recovery pojít přes virtuální počítače Hyper-V do Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 03826abf6da94859c510f4c127dfce035aa79370
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498174"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Převzetí služeb při selhání virtuálních počítačích Hyper-V do Azure

Tento kurz popisuje, jak převzetí služeb při selhání virtuálních počítačích Hyper-V do Azure pomocí [Azure Site Recovery](site-recovery-overview.md). Po převzetí služeb při selhání navrátíte služby po obnovení do místní lokality, až bude dostupná. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte vlastnosti virtuálního počítače Hyper-V, abyste zkontrolovali, zda splňují požadavky Azure.
> * Převzetí služeb při selhání konkrétních virtuálních počítačích do Azure.


Tento kurz je pátou částí série. Předpokládá se, že jste už dokončili úlohy z předchozích kurzů.    

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava místního Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. Nastavení zotavení po havárii pro [virtuální počítače Hyper-V](tutorial-hyper-v-to-azure.md) nebo [virtuální počítače Hyper-V spravované v cloudech System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)

[Další informace o](failover-failback-overview.md#types-of-failover) různých typech převzetí služeb při selhání. Pokud chcete převzetí služeb při selhání více virtuálních počítačů v plánu obnovení, přečtěte si [tento článek](site-recovery-failover.md).

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

1. V **nastavení** > **replikovaných položek**klepněte na > převzetí služeb při selhání virtuálního **počítače**.
2. V části **Převzetí služeb při selhání** vyberte **Nejnovější** bod obnovení. 
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se před aktivací převzetí služeb při selhání pokusí vypnout zdrojové virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Po ověření převzetí služeb při selhání klikněte na **Potvrdit**. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání:** Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.

## <a name="connect-to-failed-over-vm"></a>Připojení k virtuálnímu virtuálnímu zařízení s připojením k selhání

1. Pokud se chcete po převzetí služeb při selhání připojit k virtuálním počítačům Azure pomocí protokolu RDP (RDP) a zabezpečeného prostředí (SSH), [ověřte, zda byly splněny požadavky](failover-failback-overview.md#connect-to-azure-after-failover).
2. Po převzetí služeb při selhání přejděte na virtuální hod a ověřte [připojením](../virtual-machines/windows/connect-logon.md) k němu.
3. Pokud chcete po převzetí služeb při selhání použít **bod obnovení** změnit. Po potvrzení převzetí služeb při selhání v dalším kroku již nebude tato možnost k dispozici.
4. Po ověření vyberte **Potvrdit,** chcete-li dokončit bod obnovení virtuálního virtuálního soudu po převzetí služeb při selhání.
5. Po potvrzení budou odstraněny všechny ostatní dostupné body obnovení. Tento krok dokončí převzetí služeb při selhání.

>[!TIP]
> Pokud po převzetí služeb při selhání narazíte na potíže s připojením, postupujte podle [pokynů pro řešení potíží](site-recovery-failover-to-azure-troubleshoot.md).


## <a name="next-steps"></a>Další kroky

Po převzetí služeb při selhání znovu přizamkněte virtuální počítače Azure tak, aby se replikovaly z Azure do místního. Potom po virtuálnípočítače jsou znovu chráněné a replikovat do místního webu, navrácení služeb po obnovení z Azure, když jste připraveni.
