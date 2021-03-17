---
title: Nastavení převzetí služeb při selhání/navrácení služeb po obnovení na sekundární lokalitu Hyper-V pomocí Azure Site Recovery
description: Přečtěte si, jak převzít služby při selhání virtuálních počítačů Hyper-V do sekundární místní lokality a navrácení služeb po havárii do primární lokality během zotavení po havárii s Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: d31355bcb0ce42874c19988738ba06138c7a0b7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74082604"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Převzetí služeb při selhání a obnovení záložních virtuálních počítačů Hyper-V replikovaných do sekundární místní lokality

Služba [Azure Site Recovery](site-recovery-overview.md) spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení místních počítačů a virtuálních počítačů Azure (VM).

Tento článek popisuje, jak převzít služby při selhání virtuálního počítače Hyper-V spravovaného v cloudu System Center Virtual Machine Manager (VMM) do sekundární lokality VMM. Po převzetí služeb při selhání navrátíte služby po obnovení do místní lokality, až bude dostupná. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Převzetí služeb při selhání virtuálního počítače Hyper-V z primárního cloudu VMM do sekundárního cloudu VMM
> * Opětovné zapnutí ochrany ze sekundární lokality na primární a navrácení služeb po obnovení
> * Volitelně zahájit replikaci z primárního do sekundárního

## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení

Převzetí služeb při selhání a obnovení má tři fáze:

1. **Převzetí služeb při selhání sekundární lokalitou**: převzetí služeb při selhání z primární lokality do sekundárního počítače.
2. Navrácení **služeb po obnovení ze sekundární lokality**: replikace virtuálních počítačů ze sekundárního na primární a spuštění plánovaného převzetí služeb při selhání.
3. Po plánovaném převzetí služeb při selhání můžete znovu spustit replikaci z primární lokality do sekundárního.


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste dokončili postup [zotavení po havárii](hyper-v-vmm-test-failover.md) , abyste zkontrolovali, že všechno funguje podle očekávání.
- Pro dokončení navrácení služeb po obnovení se ujistěte, že jsou primární a sekundární servery VMM připojené k Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Spuštění převzetí služeb při selhání z primární na sekundární

Pro virtuální počítače Hyper-V můžete spustit běžné nebo plánované převzetí služeb při selhání.

- Pro neočekávané výpadky použijte běžné převzetí služeb při selhání. Když spustíte toto převzetí služeb při selhání, Site Recovery vytvoří virtuální počítač v sekundární lokalitě a naplní ho. Může dojít ke ztrátě dat v závislosti na probíhajících datech, která nebyla synchronizovaná.
- Plánované převzetí služeb při selhání se dá použít k údržbě nebo během očekávaného výpadku. Tato možnost poskytuje nulovou ztrátu dat. Při aktivaci plánovaného převzetí služeb při selhání se zdrojové virtuální počítače vypnou. Nesynchronizovaná data se synchronizují a aktivuje se převzetí služeb při selhání. 
- 
  Tento postup popisuje, jak spustit normální převzetí služeb při selhání.


1. V části **Nastavení**  >  **replikované položky** klikněte na virtuální počítač > **převzetí služeb při selhání**.
1. Před spuštěním **převzetí služeb při selhání vyberte vypnout počítač** , pokud se chcete Site Recovery pokusit před aktivací převzetí služeb při selhání provést vypnutí zdrojových virtuálních počítačů. Site Recovery se taky pokusí synchronizovat místní data, která se ještě neposlala do sekundární lokality, před aktivací převzetí služeb při selhání. Všimněte si, že převzetí služeb při selhání pokračuje i v případě selhání vypnutí. Průběh převzetí služeb při selhání můžete sledovat na stránce **úlohy** .
2. Nyní byste měli být schopni zobrazit virtuální počítač v sekundárním cloudu VMM.
3. Po ověření virtuálního počítače **potvrďte** převzetí služeb při selhání. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání**: před zahájením procesu převzetí služeb při selhání se zastaví replikace virtuálního počítače. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.  


## <a name="reverse-replicate-and-failover"></a>Zpětná replikace a převzetí služeb při selhání

Zahájení replikace ze sekundární lokality na primární a navrácení služeb po obnovení do primární lokality. Po opětovném spuštění virtuálních počítačů v primární lokalitě je můžete replikovat do sekundární lokality.  

 
1. Klikněte na virtuální počítač > klikněte na **Zpětná replikace**.
2. Po dokončení úlohy klikněte na virtuální počítač >v **převzetí služeb při selhání**, zkontrolujte směr převzetí služeb při selhání (z sekundárního cloudu VMM) a vyberte zdrojové a cílové umístění. 
4. Zahajte převzetí služeb při selhání. Průběh převzetí služeb při selhání můžete sledovat na kartě **Úlohy**.
5. V primárním cloudu VMM ověřte, že je virtuální počítač dostupný.
6. Pokud chcete znovu spustit replikaci primárního virtuálního počítače zpět do sekundární lokality, klikněte na **Zpětná replikace**.

## <a name="next-steps"></a>Další kroky
[Přečtěte si krok](hyper-v-vmm-disaster-recovery.md) pro replikaci virtuálních počítačů Hyper-V do sekundární lokality.
