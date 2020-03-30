---
title: Nastavení převzetí služeb při selhání nebo navrácení služeb po obnovení na sekundární masovou lokalitu pomocí azure site recovery
description: Zjistěte, jak přepojit virtuální počítače Hyper-V do sekundárního místního webu a vrátit se zpět do primární lokality během zotavení po havárii pomocí azure site recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: d31355bcb0ce42874c19988738ba06138c7a0b7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082604"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Převzetí služeb při selhání a navrácení služeb po selhání virtuální ch od pv Hyper-V virtuální počítače replikované do sekundární místní lokality

Služba [Azure Site Recovery](site-recovery-overview.md) spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení místních počítačů a virtuálních počítačů Azure (Virtuální počítače).

Tento článek popisuje, jak převzetí služeb při selhání virtuálního počítače Hyper-V spravovaného v cloudu Správce virtuálních strojů system center (VMM) do sekundární lokality VMM. Po převzetí služeb při selhání navrátíte služby po obnovení do místní lokality, až bude dostupná. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Převzetí služeb při selhání virtuálního vavoda Hyper-V z primárního cloudu VMM do sekundárního cloudu VMM
> * Znovu chránit ze sekundární lokality na primární a zpětně navrácení služeb po obnovení
> * Volitelně znovu začít replikovat z primárního na sekundární.

## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení

Převzetí služeb při selhání a navrácení služeb po selhání má tři fáze:

1. **Převzetí služeb při selhání do sekundární lokality**: Selhání počítače z primární lokality do sekundární.
2. **Převzetí služeb při selhání ze sekundární lokality**: Replikujte virtuální chod ze sekundárního na primární a spusťte plánované převzetí služeb při selhání a navrácení služeb po selhání.
3. Po plánovaném převzetí služeb při selhání můžete volitelně začít replikovat z primární lokality do sekundární znovu.


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste dokončili [cvičení zotavení po havárii,](hyper-v-vmm-test-failover.md) abyste zkontrolovali, zda vše funguje podle očekávání.
- Chcete-li dokončit navrácení služeb po obnovení, ujistěte se, že primární a sekundární servery VMM jsou připojeny k site recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Spuštění převzetí služeb při selhání z primárního na sekundární

Můžete spustit pravidelné nebo plánované převzetí služeb při selhání pro virtuální aplikace Hyper-V.

- Pro neočekávané výpadky použijte běžné převzetí služeb při selhání. Když spustíte toto převzetí služeb při selhání, site recovery vytvoří virtuální ho v sekundární lokalitě a napne ji. Ke ztrátě dat může dojít v závislosti na čekajících datech, která nebyla synchronizována.
- Plánované převzetí služeb při selhání lze použít pro údržbu nebo během očekávaného výpadku. Tato možnost poskytuje nulovou ztrátu dat. Když se aktivuje plánované převzetí služeb při selhání, zdrojové virtuální chody se vypnou. Nesynchronizovaná data jsou synchronizována a převzetí služeb při selhání je aktivováno. 
- 
  Tento postup popisuje, jak spustit pravidelné převzetí služeb při selhání.


1. V **nastavení** > **Replikované položky** klepněte na > **převzetí služeb při selhání**virtuálního počítače .
1. Vyberte **Vypnout počítač před zahájením převzetí služeb při selhání,** pokud chcete, aby se obnovení webu pokusilo před spuštěním převzetí služeb při selhání provést vypnutí zdrojových virtuálních počítačů. Obnovení webu se také pokusí synchronizovat místní data, která ještě nebyla odeslána do sekundární lokality, před aktivací převzetí služeb při selhání. Všimněte si, že převzetí služeb při selhání pokračuje i v případě, že vypnutí se nezdaří. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
2. Teď byste měli být schopni vidět virtuální ho v sekundárním cloudu VMM.
3. Po ověření virtuálního virtuálního **soudu, potvrďte** převzetí služeb při selhání. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání**: před zahájením procesu převzetí služeb při selhání se zastaví replikace virtuálního počítače. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.  


## <a name="reverse-replicate-and-failover"></a>Obrácená replikace a převzetí služeb při selhání

Začněte replikovat ze sekundární lokality do primární a zpětně naprimární lokalitu. Po virtuálních discích jsou spuštěny v primární lokalitě znovu, můžete replikovat do sekundární lokality.  

 
1. Klikněte na tlačítko Virtuální počítač > klikněte na **Možnost Obrátit replikaci**.
2. Po dokončení úlohy klikněte na >v převzetí **služeb při selhání**, ověřte směr převzetí služeb při selhání (ze sekundárního cloudu VMM) a vyberte zdrojová a cílová umístění. 
4. Zahajte převzetí služeb při selhání. Průběh převzetí služeb při selhání můžete sledovat na kartě **Úlohy**.
5. V primárním cloudu VMM zkontrolujte, zda je virtuální montovna k dispozici.
6. Pokud chcete znovu začít replikovat primární virtuální počítač zpět do sekundární lokality, klikněte na **Obrátit replikaci**.

## <a name="next-steps"></a>Další kroky
[Zkontrolujte krok](hyper-v-vmm-disaster-recovery.md) pro replikaci virtuálních počítače Hyper-V do sekundární lokality.
