---
title: Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Hyper-V replikovat do sekundárního datového centra pomocí služby Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak převzít služby virtuálních počítačů Hyper-V na sekundární místní web a navrácení služeb po obnovení do primární lokality pomocí Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: b3ca7be647ff7db0d147eca57edb20e658a28130
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919467"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů Hyper-V replikovat do sekundárního místního serveru

[Azure Site Recovery](site-recovery-overview.md) služba spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení z místních počítačů a Azure virtual machines (VM).

Tento článek popisuje, jak převzít služby při selhání pro virtuální počítač Hyper-V spravované v cloudech System Center Virtual Machine Manager (VMM), do sekundární lokality VMM. Po převzetí služeb při selhání navrátíte služby po obnovení do místní lokality, až bude dostupná. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Převzetí služeb při selhání virtuálního počítače Hyper-V z primárního cloudu VMM do sekundárního cloudu VMM
> * Zpětná replikace ze sekundární lokality do primární a navrácení služeb po obnovení
> * Volitelně můžete spustit replikaci z primárního na sekundární znovu

## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení

Převzetí služeb při selhání a navrácení služeb po obnovení má tři fáze:

1. **Převzetí služeb při selhání do sekundární lokality**: selhání počítačů z primární lokality do sekundární.
2. **Selhání zpět ze sekundární lokality**: replikovat virtuální počítače ze sekundární do primární a spusťte plánované převzetí služeb při selhání pro navrácení služeb po obnovení.
3. Po plánované převzetí služeb při selhání volitelně spusťte, které se replikují z primární lokality do sekundární znovu.


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste dokončili [zotavení po havárii](hyper-v-vmm-test-failover.md) ke kontrole, že vše funguje podle očekávání.
- K dokončení navrácení služeb po obnovení, ujistěte se, že primární a sekundární server VMM jsou připojené k Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Spusťte převzetí služeb při selhání z primární na sekundární

Můžete spustit pravidelné nebo plánované převzetí služeb při selhání pro virtuální počítače Hyper-V.

- Použijte regulární převzetí služeb při selhání pro nečekaných výpadků. Při spuštění tohoto převzetí služeb při selhání Site Recovery vytvoří virtuální počítač v sekundární lokalitě a zajišťuje nahoru. Může dojít ke ztrátě dat v závislosti na čekání dat, která nebyla synchronizována.
- Plánované převzetí služeb při selhání je možné, údržbě nebo při očekávaný výpadek. Tato možnost poskytuje nulové ztráty. Při plánované převzetí služeb při selhání se aktivuje, jsou ukončení zdrojových virtuálních počítačů. Nesynchronizované synchronizaci dat a převzetí služeb při selhání se aktivuje. 
- 
Tento postup popisuje, jak regulární převzetí služeb při selhání.


1. V části **Nastavení** > **Replikované položky** klikněte na virtuální počítač a pak na **Převzetí služeb při selhání**.
1. Vyberte **před spuštěním převzetí služeb při selhání vypnout počítač** Pokud chcete, aby Site Recovery chcete pokusit před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače. Site Recovery se pokusí také synchronizovat místní data, která nebyla ještě nebyly odeslány do sekundární lokality před aktivací převzetí služeb. Všimněte si, že převzetí služeb při selhání pokračovat i v případě, že vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
2. Teď by měl být vidět virtuální počítač v sekundárním cloudu VMM.
3. Po ověření virtuálních počítačů, **potvrzení** převzetí služeb při selhání. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání:** Před spuštěním převzetí služeb při selhání se zastaví replikace virtuálního počítače. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.  


## <a name="reverse-replicate-and-failover"></a>Zpětná replikace a převzetí služeb při selhání

Zahájení replikace ze sekundární lokality na primární a navrácení služeb po obnovení primární lokality. Po spuštění virtuálních počítačů ve primární lokality znovu, můžete je replikovat do sekundární lokality.  

 
1. Klikněte na virtuální počítač > klikněte na **reverzní replikaci**.
2. Po dokončení úlohy klikněte na virtuální počítač > v **převzetí služeb při selhání**zkontrolujte směr převzetí služeb při selhání (ze sekundárního cloudu VMM) a vyberte zdrojovým a cílovým umístěním. 
4. Zahajte převzetí služeb při selhání. Průběh převzetí služeb při selhání, můžete postupovat podle **úlohy** kartu.
5. V primárním cloudu VMM zkontrolujte, že virtuální počítač je k dispozici.
6. Pokud chcete ke spuštění replikace primárního virtuálního počítače zpátky do sekundární lokality znovu, klikněte na **reverzní replikaci**.

## <a name="next-steps"></a>Další postup
[Projděte si krok](hyper-v-vmm-disaster-recovery.md) pro replikaci virtuálních počítačů Hyper-V do sekundární lokality.
