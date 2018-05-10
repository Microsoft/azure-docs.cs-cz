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
ms.openlocfilehash: f4207b8def3a5cd240b7a3ecdffde34a27f2a833
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
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

- Použijte regulární převzetí služeb při selhání pro neočekávaných výpadků. Spustíte-li toto převzetí služeb při selhání, Site Recovery vytvoří virtuální počítač v sekundární lokalitě a zajišťuje nahoru. Můžete spustit převzetí služeb při selhání pro konkrétní bod obnovení. Může dojít ke ztrátě dat v závislosti na bod obnovení, které používáte.
- Plánované převzetí služeb při selhání můžete použít, údržbě nebo při očekávaný výpadek. Tato možnost poskytuje nulovou ztrátou data. Když se aktivuje plánované převzetí služeb při selhání, jsou zdrojové virtuální počítače vypnout. Nesynchronizovaná data synchronizována a aktivaci převzetí služeb při selhání. 
- 
Tento postup popisuje, jak spustit regulární převzetí služeb při selhání.


1. V části **Nastavení** > **Replikované položky** klikněte na virtuální počítač a pak na **Převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Můžete použít jednu z následujících možností:
    - **Nejnovější (výchozí):** Tato možnost nejprve zpracuje veškerá data odeslaná do Site Recovery. Poskytuje nejnižší plánovaný bod obnovení (plánovaného bodu obnovení), protože virtuální počítač repliky vytvořené po převzetí služeb při selhání se všechna data, která se replikují do Site Recovery, když bylo spuštěno převzetí služeb při selhání.
    - **Nejnovější zpracovaný:** Tato možnost převezme služby při selhání virtuálního počítače k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
    - **Nejnovější konzistentní vzhledem k aplikacím:** Tato možnost převezme služby při selhání virtuálního počítače k nejnovějšímu bodu obnovení konzistentnímu vzhledem k aplikacím zpracovanému službou Site Recovery. 
3. Šifrovací klíč není relevantní v tomto scénáři.
4. Vyberte **vypnout počítač před zahájením převzetí služeb při selhání** Pokud chcete, aby Site Recovery se pokusit o proveďte vypnutí zdrojové virtuální počítače před spuštěním převzetí služeb při selhání. Site Recovery se také pokusí synchronizovat místní data, která nebyla dosud odeslána do sekundární lokality, než převzetí služeb při selhání. Všimněte si, že převzetí služeb při selhání pokračovat i v případě, že vypnutí selže. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
5. Teď by měla být moci zobrazit virtuální počítač v sekundární cloudu VMM.
6. Po ověření, virtuální počítač, **potvrdit** převzetí služeb při selhání. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání:** Před spuštěním převzetí služeb při selhání se zastaví replikace virtuálního počítače. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.  


## <a name="reprotect-and-fail-back"></a>Znovu nastavte ochranu a navrácení služeb po obnovení

Zahájení replikace ze sekundární lokality na primární a selhání zpátky do primární lokality. Po virtuální počítače jsou spuštěné v primární lokalitě znovu, můžete replikovat je do sekundární lokality znovu.  

1. V **nastavení** > **replikované položky** klikněte na virtuální počítač a povolte **zpětnou replikaci**. Virtuální počítač začne replikovat zpět do primární lokality.
2. Klikněte na virtuální počítač > **plánované převzetí služeb při selhání**.
3. V **potvrďte plánované převzetí služeb při selhání**ověřte směr převzetí služeb při selhání (z sekundární cloudu VMM) a vyberte zdrojové a cílové umístění. 
4. V **synchronizace dat**, zadejte, jak chcete synchronizovat:
    - **Synchronizace dat před převzetí služeb při selhání (synchronizovat jenom rozdílové změny)**– tato možnost minimalizuje výpadek virtuálních počítačů, protože synchronizuje bez vypnutí virtuálního počítače. Tady je co umožňuje:
        - Pořídí snímek virtuálního počítače repliky a zkopíruje ho primárním hostitelem technologie Hyper-V. Replika virtuálních počítačů neustále běží.
        - Vypne repliku virtuálního počítače, tak, aby žádné nové změny dojít k dispozici. Závěrečné sady rozdílové změny přenesou do primární lokality a spuštění virtuálního počítače v primární lokalitě.
    - **Synchronizace dat během pouze převzetí služeb při selhání (úplná ke stažení)**– tuto možnost použijte, pokud jste si už běží v sekundární lokalitě dlouhou dobu. Tato možnost je rychlejší, protože očekávané více změny na disku a nebudete věnovat času kontrolního součtu. Tato možnost provede stahování disku. Je také užitečné při primární virtuální počítač byl odstraněn.
5. Šifrovací klíč není relevantní v tomto scénáři.
6. Zahájit převzetí služeb při selhání. Můžete sledovat průběh převzetí služeb při selhání **úlohy** kartě.
7. Pokud jste vybrali k synchronizaci dat před převzetí služeb při selhání, po synchronizaci počáteční data se provádí a jste připraveni vypnout repliku virtuálního počítače v sekundární lokalitě, klikněte na tlačítko **úlohy** > název úlohy plánované převzetí služeb při selhání >  **Dokončete převzetí služeb při selhání**. Tato sekundární virtuální počítač vypne, přenáší nejnovější změny do primární lokality a spustí primárního virtuálního počítače.
8. V primárním cloudu VMM zkontrolujte, zda virtuální počítač je k dispozici.
9. Primární virtuální počítač je nyní ve stavu čekající potvrzení. Klikněte na tlačítko **potvrzení**, potvrzení převzetí služeb při selhání.
10. Pokud chcete, aby zahájení replikace primárního virtuálního počítače zpátky do sekundární lokality znovu povolte **zpětnou replikaci**.


> [!NOTE]
> Zpětná replikace replikuje pouze změny, které nastaly od repliky virtuálních počítačů se vypnulo a posílají se jenom rozdílové změny.

## <a name="next-steps"></a>Další postup
[Zkontrolujte v kroku](hyper-v-vmm-disaster-recovery.md) pro replikaci virtuálních počítačů Hyper-V do sekundární lokality.
