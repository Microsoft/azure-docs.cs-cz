---
title: Převzetí služeb při selhání a selhání zálohování virtuálních počítačů Hyper-V replikovat do Azure pomocí Site Recovery | Microsoft Docs
description: Naučte se vrátí zpátky na místní lokalitu s Azure Site Recovery a selhání virtuálních počítačů Hyper-V do Azure
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 6a34187a87c6ecda461357a1c2fc8747ddf4b056
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294288"
---
# <a name="failover-and-failback-hyper-v-vms-replicated-to-azure"></a>Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Hyper-V replikovat do Azure

Tento kurz popisuje, jak převzetí služeb při selhání virtuálního počítače technologie Hyper-V do Azure. Poté, co jste jste po selhání, můžete navrácení služeb po obnovení na místní lokalitu případě, že je k dispozici. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte, zda vlastnosti virtuálního počítače technologie Hyper-V a zjistěte, v souladu s požadavky na Azure
> * Spuštění převzetí služeb při selhání do Azure
> * Navrácení služeb po obnovení z Azure do místní
> * Zpětná replikace místní virtuální počítače, spusťte znovu replikaci do Azure

V tomto kurzu je pátý kurz v řadě. Přitom se předpokládá, že jste už dokončili úkoly v předchozí kurzy.    

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava místního Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. Nastavit zotavení po havárii pro [virtuálních počítačů Hyper-V](tutorial-hyper-v-to-azure.md), nebo pro [virtuálních počítačů Hyper-V spravované v cloudech System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Příprava na převzetí služeb při selhání a navrácení služeb po obnovení

Zajistěte, aby ve virtuálním počítači nejsou žádné snímky a místní virtuální počítač vypnutý během navrácení služeb po obnovení. Pomáhá zajistit konzistenci dat během replikace. Nikdy nezapínat na virtuálním počítači místní během navrácení služeb po obnovení. 

Převzetí služeb při selhání a navrácení služeb po obnovení mají tři fáze:

1. **Převzetí služeb při selhání do Azure**: převzetí služeb při selhání Hyper-V virtuálních počítačů z místní lokality do Azure.
2. **Navrácení služeb po obnovení k místnímu**: virtuální počítače Azure převzetí služeb při selhání k místní lokalitě, pokud je k dispozici. Spuštění replikace virtuálních počítačů zpět do místní virtuální počítače Hyper-V. Po synchronizaci počáteční data virtuálních počítačích Azure převzetí služeb při selhání k místní lokalitě.  
3. **Zpětná replikace lokálních virtuálních počítačů**: po dat se nezdařila zpět, zpětného replikovat místní virtuální počítače k zahájení replikace je do Azure.

## <a name="verify-vm-properties"></a>Ověření vlastností virtuálního počítače

Před převzetí služeb při selhání ověřte vlastnosti virtuálního počítače a ujistěte se, že splňují virtuální počítač s [požadavky pro Azure](hyper-v-azure-support-matrix.md#replicated-vms).

1. V **chráněné položky**, klikněte na tlačítko **replikované položky** >< název virtuálního počítače >.

2. V **replikované položky** podokně, přečtěte si informace o virtuálních počítačů, stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.
     - V **výpočty a síť**, můžete upravit nastavení virtuálního počítače a nastavení sítě, včetně sítě a podsítě, ve kterém virtuální počítač Azure budou umístěné po převzetí služeb při selhání, a IP adresy, které budou přiřazeny ho. Spravované disky nejsou podporovány pro navrácení služeb po obnovení z Azure do Hyper-V.
      - V části **Disky** se zobrazí informace o operačním systému a datových discích ve virtuálním počítači.

## <a name="failover-to-azure"></a>Převzetí služeb při selhání do Azure

1. V části **Nastavení** > **Replikované položky** klikněte na virtuální počítač a pak na **Převzetí služeb při selhání**.
2. V **převzetí služeb při selhání** vyberte **nejnovější** bod obnovení. 
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Site Recovery se pokusí provést vypnutí zdrojové virtuální počítače před spuštěním převzetí služeb při selhání. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Po ověření, a klikněte na převzetí služeb při selhání **potvrdit**. Odstraní všechny body obnovení k dispozici.

> [!WARNING]
> **Nemáte zrušení převzetí služeb při selhání v průběhu**: Pokud zrušíte probíhá zastaví převzetí služeb při selhání, ale nebude znovu replikaci virtuálního počítače.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Navrácení služeb po obnovení virtuálního počítače Azure a místními zpětného replikovat místní virtuální počítač

Operace navrácení služeb po obnovení je v podstatě převzetí služeb při selhání z Azure do místní lokality a zpětná replikace začne znovu replikace virtuálních počítačů z místní lokality do Azure.

1. V **nastavení** > **replikované položky**, klikněte na virtuální počítač > **plánované převzetí služeb při selhání**.
2. V **potvrďte plánované převzetí služeb při selhání**, ověřte směr převzetí služeb při selhání (z Azure) a vyberte zdrojové a cílové umístění.
3. Vyberte **synchronizovat data před převzetí služeb při selhání (synchronizovat jenom rozdílové změny)**. Tato možnost minimalizuje výpadek virtuálních počítačů, protože synchronizuje bez vypnutí virtuálního počítače.
4. Zahájit převzetí služeb při selhání. Můžete sledovat průběh převzetí služeb při selhání **úlohy** kartě.
5. Po počáteční data se provádí synchronizaci a jste připraveni vypnout a klikněte na virtuálních počítačích Azure **úlohy** > plánované převzetí služeb při selhání úlohy název--> **dokončení převzetí služeb při selhání**. Vypnutí virtuálního počítače Azure, přenáší nejnovější změny místní a spustí virtuální místní počítač.
6. Přihlaste se k virtuálnímu počítači na místě a zkontrolovat, zda že je k dispozici podle očekávání.
7. Místní virtuální počítač je nyní v **čekajícího potvrzení** stavu. Klikněte na tlačítko **potvrdit**. Odstraní virtuální počítače Azure a jeho disky a připraví místní virtuální počítač na zpětnou replikaci.
Pokud chcete spustit replikovat místní virtuální počítač Azure, povolte **zpětnou replikaci**. Aktivuje replikace rozdílové změny, ke kterým došlo, protože se vypnout virtuální počítač Azure.  
