---
title: Scénáře zotavení po havárii
description: Zjistěte, co dělat v případě, že narušení služby Azure ovlivňuje virtuální počítače Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 05/31/2017
ms.author: cynthn
ms.openlocfilehash: f2dc43e1f07d449bf2f8ed39ce4523c99b551dae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115626"
---
# <a name="what-if-an-azure-service-disruption-impacts-azure-vms"></a>Co když narušení služby Azure ovlivní virtuální počítače Azure
Ve společnosti Microsoft usilovně pracujeme na tom, abychom zajistili, že naše služby budou vždy dostupné, když je budete potřebovat. Síly mimo naši kontrolu nás někdy ovlivňují způsoby, které způsobují neplánované přerušení služeb.

Společnost Microsoft poskytuje smlouvu o úrovni služeb (SLA) pro své služby jako závazek pro provozunostia a připojení. Smlouvu SLA pro jednotlivé služby Azure najdete na [webu Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

Azure už má mnoho integrovaných funkcí platformy, které podporují vysoce dostupné aplikace. Další informace o těchto službách načtete [zotavení po havárii a vysokou dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Tento článek popisuje skutečný scénář zotavení po havárii, kdy celý region dojde k výpadku v důsledku závažné přírodní katastrofy nebo rozsáhlé přerušení služby. Jedná se o vzácné události, ale musíte se připravit na možnost, že dojde k výpadku celé oblasti. Pokud dojde k přerušení služby v celé oblasti, místně redundantní kopie dat by dočasně nebyly k dispozici. Pokud jste povolili geografickou replikaci, tři další kopie objektů blob a tabulky azure storage jsou uloženy v jiné oblasti. V případě úplného regionálního výpadku nebo havárie, ve které není obnovitelná primární oblast, Azure přemapuje všechny položky DNS do geograficky replikované oblasti.

Abychom vám pomohli zpracovat tyto vzácné výskyty, poskytujeme následující pokyny pro virtuální počítače Azure v případě přerušení služby v celé oblasti, kde se nasazuje vaše aplikace virtuálního počítače Azure.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Možnost 1: Zahájení převzetí služeb při selhání pomocí Azure Site Recovery
Azure Site Recovery můžete nakonfigurovat pro vaše virtuální počítače, takže můžete obnovit aplikaci jediným kliknutím během několika minut. Můžete replikovat do oblasti Azure podle vašeho výběru a není omezena na spárované oblasti. Můžete začít [replikací virtuálních počítačů](https://aka.ms/a2a-getting-started). Můžete [vytvořit plán obnovení,](../site-recovery/site-recovery-create-recovery-plans.md) abyste mohli automatizovat celý proces převzetí služeb při selhání pro vaši aplikaci. Převzetí služeb při selhání můžete předem [otestovat,](../site-recovery/site-recovery-test-failover-to-azure.md) aniž by to mělo vliv na produkční aplikaci nebo probíhající replikaci. V případě narušení primární oblasti stačí [zahájit převzetí služeb při selhání](../site-recovery/site-recovery-failover.md) a přenést vaši aplikaci do cílové oblasti.


## <a name="option-2-wait-for-recovery"></a>Možnost 2: Počkejte na obnovení
V takovém případě není nutná žádná akce z vaší strany. Vězte, že usilovně pracujeme na obnovení dostupnosti služeb. Aktuální stav služby najdete na našem [řídicím panelu stavu služby Azure](https://azure.microsoft.com/status/).

To je nejlepší možnost, pokud jste nenastavili Azure Site Recovery, geograficky redundantní úložiště pro čtení nebo geograficky redundantní úložiště před přerušením. Pokud jste nastavili geograficky redundantní úložiště nebo geograficky redundantní úložiště pro přístup ke čtení pro účet úložiště, kde jsou uloženy virtuální pevné disky virtuálního počítače (VHD), můžete se podívat na obnovení virtuálního pevného disku základní bitové kopie a pokusit se z něj zřídit nový virtuální počítač. Toto není upřednostňovaná možnost, protože neexistují žádné záruky synchronizace dat. V důsledku toho není zaručeno, že tato možnost bude fungovat.


> [!NOTE]
> Uvědomte si, že nemáte žádnou kontrolu nad tímto procesem a dojde pouze pro přerušení služby v rámci celé oblasti. Z tohoto důvodu musíte také spoléhat na jiné strategie zálohování specifické pro aplikaci k dosažení nejvyšší úrovně dostupnosti. Další informace naleznete v části [Strategie dat pro zotavení po havárii](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan).
>
>

## <a name="next-steps"></a>Další kroky

- Začněte [chránit své aplikace spuštěné na virtuálních počítačích Azure](https://aka.ms/a2a-getting-started) pomocí Azure Site Recovery

- Další informace o implementaci strategie zotavení po havárii a strategie vysoké dostupnosti najdete v [tématu Zotavení po havárii a vysoká dostupnost pro aplikace Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- Podrobné technické znalosti o možnostech cloudové platformy najdete v [technických příručkách odolnosti azure](/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).


- Pokud pokyny nejsou jasné nebo pokud chcete, aby společnost Microsoft operace dělala vaším jménem, obraťte se na [zákaznickou podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
