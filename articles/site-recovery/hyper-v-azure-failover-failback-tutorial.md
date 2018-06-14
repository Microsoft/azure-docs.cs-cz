---
title: Převzetí služeb při selhání a selhání zálohování virtuálních počítačů Hyper-V replikovat do Azure pomocí Site Recovery | Microsoft Docs
description: Naučte se vrátí zpátky na místní lokalitu s Azure Site Recovery a selhání virtuálních počítačů Hyper-V do Azure
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/8/2018
ms.author: raynew
ms.openlocfilehash: 7863feb29fbb04f643aa3b7e1984209f44cdbe9a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852893"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Převzetí služeb při selhání a selhání zálohování virtuálních počítačů Hyper-V replikovat do Azure

Tento kurz popisuje, jak k převzetí služeb virtuálního počítače technologie Hyper-V do Azure. Po převzetí služeb při selhání navrátíte služby po obnovení do místní lokality, až bude dostupná. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte, zda vlastnosti virtuálního počítače technologie Hyper-V a zjistěte, v souladu s požadavky na Azure
> * Spuštění převzetí služeb při selhání do Azure
> * Znovunastavení ochrany virtuálních počítačů Azure do místní lokality
> * Po obnovení vrátit Azure k místnímu
> * Znovunastavení ochrany místních virtuálních počítačů a opětovné spuštění replikace do Azure

Tento kurz je pátou částí série. Tento kurz předpokládá, že jste už dokončili úlohy z předchozích kurzů.

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava VMware v místním prostředí](tutorial-prepare-on-premises-hyper-v.md)
3. Nastavit zotavení po havárii pro [virtuálních počítačů Hyper-V](tutorial-hyper-v-to-azure.md), nebo pro [virtuálních počítačů Hyper-V spravované v cloudech System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Příprava na převzetí služeb při selhání a navrácení služeb po obnovení

Zajistěte, aby ve virtuálním počítači nejsou žádné snímky a místní virtuální počítač vypnutý během vytvoření. To pomáhá zajistit konzistenci dat během replikace. Po dokončení opětovného nastavování ochrany virtuální počítač nezapínejte. 

Převzetí služeb při selhání a navrácení služeb po obnovení probíhá ve čtyřech fázích:

1. **Převzetí služeb při selhání do Azure:** Převezmou se služby při selhání počítačů z místní lokality do Azure.
2. **Znovu nastavte ochranu virtuálních počítačů Azure**: znovu nastavte ochranu virtuálních počítačů Azure, tak, aby jejich spuštění replikovat zpět na místní virtuální počítače Hyper-V.
3. **Převzetí služeb při selhání místně**: spuštění převzetí služeb při selhání z Azure na svůj místní server, pokud je k dispozici.
4. **Opětovné ochrany místní virtuální počítače**: po dat se nezdařila zpět, znovu aktivujte ochranu místní virtuální počítače k zahájení replikace je do Azure.

## <a name="verify-vm-properties"></a>Ověření vlastností virtuálního počítače

Ověřte vlastnosti virtuálního počítače a ujistěte se, že splňuje [požadavky Azure](hyper-v-azure-support-matrix.md#replicated-vms).

1. V **chráněné položky**, klikněte na tlačítko **replikované položky** >< název virtuálního počítače >.

2. V **replikované položky** podokně, přečtěte si informace o virtuálních počítačů, stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.
     - V **výpočty a síť**, můžete upravit nastavení virtuálního počítače a sítě nastavení, včetně sítě a podsítě, ve kterém virtuální počítač Azure. Spravované disky nejsou podporovány pro navrácení služeb po obnovení z Azure do Hyper-V.
   budou umístěné po převzetí služeb při selhání a IP adresu, která bude přiřazena k němu.
    - V části **Disky** se zobrazí informace o operačním systému a datových discích ve virtuálním počítači.

## <a name="fail-over-to-azure"></a>Převzetí služeb při selhání do Azure

1. V části **Nastavení** > **Replikované položky** klikněte na virtuální počítač a pak na **Převzetí služeb při selhání**.
2. V **převzetí služeb při selhání** vyberte **nejnovější** bod obnovení. 
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se pokusí provést vypnutí zdrojové virtuální počítače před spuštěním převzetí služeb při selhání. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Po ověření, a klikněte na převzetí služeb při selhání **potvrdit**. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání**: před zahájením procesu převzetí služeb při selhání se zastaví replikace virtuálního počítače. Je-li zrušit probíhající zastaví převzetí služeb při selhání, ale nebude znovu replikaci virtuálního počítače.

## <a name="reprotect-azure-vms"></a>Znovunastavení ochrany virtuálních počítačů Azure

1. V **AzureVMVault** > **replikované položky**, klikněte pravým tlačítkem na virtuální počítač, který byl převzetí služeb při selhání a vyberte **znovu nastavit ochranu**.
2. Ověřte, zda je směr ochrany nastavena **Azure k místnímu**.
3. Místní virtuální počítač je vypnutý během vytvoření zajistit konzistenci dat. Nezapnete ji po dokončení vytvoření.
4. Po dokončení procesu vytvoření virtuální počítač spustí, replikace z Azure do místního webu.



## <a name="fail-over-from-azure-and-reprotect-the-on-premises-vm"></a>Převzetí služeb při selhání z Azure a znovu aktivujte ochranu virtuálních počítačů na místě

Převzetí služeb při selhání z Azure na místní lokalitu a spustit replikaci virtuálních počítačů z místní lokality do Azure.

1. V **nastavení** > **replikované položky**, klikněte na virtuální počítač > **plánované převzetí služeb při selhání**.
2. V **potvrďte plánované převzetí služeb při selhání**, ověřte směr převzetí služeb při selhání (z Azure) a vyberte zdrojové a cílové umístění.
3. Vyberte **synchronizovat data před převzetí služeb při selhání (synchronizovat jenom rozdílové změny)**. Tato možnost minimalizuje výpadek virtuálních počítačů, protože synchronizuje bez vypnutí virtuálního počítače.
4. Zahájit převzetí služeb při selhání. Můžete sledovat průběh převzetí služeb při selhání **úlohy** kartě.
5. Po počáteční data se provádí synchronizaci a jste připraveni vypnout a klikněte na virtuálních počítačích Azure **úlohy** > plánované převzetí služeb při selhání úlohy název--> **dokončení převzetí služeb při selhání**. To vypne virtuální počítač Azure, přenáší nejnovější změny místní a spustí virtuální počítač místně.
6. Přihlaste se k virtuálnímu počítači na místě a zkontrolovat, zda že je k dispozici podle očekávání.
7. Místní virtuální počítač je nyní v **čekajícího potvrzení** stavu. Klikněte na tlačítko **potvrdit**. Odstraní virtuální počítače Azure a jeho disky a připraví místní virtuální počítač na zpětnou replikaci.
Pokud chcete spustit replikovat místní virtuální počítač Azure, povolte **zpětnou replikaci**. Tím se spustí replikaci rozdílové změny, ke kterým došlo, protože se vypnout virtuální počítač Azure.  
