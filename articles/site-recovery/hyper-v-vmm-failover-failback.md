---
title: Převzetí služeb při selhání a selhání zálohování virtuálních počítačů Hyper-V replikovat do sekundárního datového centra pomocí Site Recovery | Microsoft Docs
description: Zjistěte, jak převzetí služeb při selhání Hyper-V virtuální počítače na sekundární místní lokalitu a navrácení služeb po obnovení primární lokality, službou Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.openlocfilehash: ecb0b9395ce7071442ddf0dd976e1ca57b8be906
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161137"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Převzetí služeb při selhání a selhání zálohování virtuálních počítačů Hyper-V replikovat na sekundární místní lokalitu

[Azure Site Recovery](site-recovery-overview.md) služby spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení místního počítače a virtuální počítače Azure (VM).

Tento článek popisuje, jak k převzetí služeb virtuálního počítače technologie Hyper-V spravované v cloudu System Center Virtual Machine Manager (VMM), pro sekundární lokalita VMM. Po převzetí služeb při selhání navrátíte služby po obnovení do místní lokality, až bude dostupná. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Selhání virtuálního počítače technologie Hyper-V z primárního cloudu VMM do sekundární cloudu VMM
> * Znovu nastavte ochranu ze sekundární lokality na primární a navrácení služeb po obnovení
> * Volitelně můžete spustit replikaci z primárního na sekundární znovu

## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení

Převzetí služeb při selhání a navrácení služeb po obnovení má tři fáze:

1. **Převzetí služeb při selhání sekundární lokality**: převzetí služeb při selhání počítače z primární lokality, sekundární.
2. **Po obnovení vrátit sekundární lokality**: replikace virtuálních počítačů z sekundární pro primární a spusťte plánované převzetí služeb při selhání pro navrácení služeb po obnovení.
3. Po plánovaném převzetí služeb při selhání můžete spusťte, replikace z primární lokality do sekundární znovu.


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, když jste dokončili [postupu zotavení po havárii](hyper-v-vmm-test-failover.md) zkontrolujte, zda vše funguje podle očekávání.
- Dokončit navrácení služeb po obnovení, ujistěte se, že primární a sekundární servery VMM připojeni k Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Spustit převzetí služeb při selhání z primárního na sekundární

Pro virtuální počítače Hyper-V můžete spustit standardním nebo plánované převzetí služeb při selhání.

- Použijte regulární převzetí služeb při selhání pro neočekávaných výpadků. Spustíte-li toto převzetí služeb při selhání, Site Recovery vytvoří virtuální počítač v sekundární lokalitě a zajišťuje nahoru. Může dojít ke ztrátě dat v závislosti na čekající na vyřízení data, která nebyla synchronizována.
- Plánované převzetí služeb při selhání můžete použít, údržbě nebo při očekávaný výpadek. Tato možnost poskytuje nulovou ztrátou data. Když se aktivuje plánované převzetí služeb při selhání, jsou zdrojové virtuální počítače vypnout. Nesynchronizovaná data synchronizována a aktivaci převzetí služeb při selhání. 
- 
Tento postup popisuje, jak spustit regulární převzetí služeb při selhání.


1. V části **Nastavení** > **Replikované položky** klikněte na virtuální počítač a pak na **Převzetí služeb při selhání**.
1. Vyberte **vypnout počítač před zahájením převzetí služeb při selhání** Pokud chcete, aby Site Recovery se pokusit o proveďte vypnutí zdrojové virtuální počítače před spuštěním převzetí služeb při selhání. Site Recovery se také pokusí synchronizovat místní data, která nebyla dosud odeslána do sekundární lokality, než převzetí služeb při selhání. Všimněte si, že převzetí služeb při selhání pokračovat i v případě, že vypnutí selže. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
2. Teď by měla být moci zobrazit virtuální počítač v sekundární cloudu VMM.
3. Po ověření, virtuální počítač, **potvrdit** převzetí služeb při selhání. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání:** Před spuštěním převzetí služeb při selhání se zastaví replikace virtuálního počítače. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.  


## <a name="reverse-replicate-and-failover"></a>Zpětná replikace a převzetí služeb při selhání

Zahájení replikace ze sekundární lokality na primární a selhání zpátky do primární lokality. Po virtuální počítače jsou spuštěné v primární lokalitě znovu, můžete provádět replikaci do sekundární lokality.  

 
1. Klikněte na virtuální počítač > klikněte na **zpětnou replikaci**.
2. Po dokončení úlohy klikněte na virtuální počítač > v **převzetí služeb při selhání**ověřte směr převzetí služeb při selhání (z sekundární cloudu VMM) a vyberte zdrojové a cílové umístění. 
4. Zahájit převzetí služeb při selhání. Můžete sledovat průběh převzetí služeb při selhání **úlohy** kartě.
5. V primárním cloudu VMM zkontrolujte, zda virtuální počítač je k dispozici.
6. Pokud chcete spustit replikaci primárního virtuálního počítače zpátky do sekundární lokality znovu, klikněte na **zpětnou replikaci**.

## <a name="next-steps"></a>Další postup
[Zkontrolujte v kroku](hyper-v-vmm-disaster-recovery.md) pro replikaci virtuálních počítačů Hyper-V do sekundární lokality.
