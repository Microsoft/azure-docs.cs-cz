---
title: Přesun virtuálních počítačů Azure IaaS do jiné oblasti Azure pomocí služby Azure Site Recovery | Dokumentace Microsoftu
description: Pomocí Azure Site Recovery pro přesun virtuálních počítačů Azure IaaS z jedné oblasti Azure do jiného.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 07fee8e5d051f0d2c04d3b5e34b66299734ca29c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549135"
---
# <a name="move-azure-vms-to-another-region"></a>Přesun virtuálních počítačů Azure do jiné oblasti

Azure roste společně s zákazník základní a přidává podporu pro nové oblasti drží neustále se zvyšující požadavky. Nové funkce jsou také přidány měsíčně napříč službami. Můžete chtít vaše virtuální počítače (VM) přesunout do jiné oblasti nebo do zóny dostupnosti a zvyšují dostupnost.

Tento kurz popisuje různé scénáře, ve kterých chcete přesunout virtuální počítače. Také popisuje, jak nakonfigurovat architekturu v cílové oblasti, abyste dosáhli vysoké dostupnosti. 

V tomto kurzu se naučíte:

> [!div class="checklist"]

> * Důvody pro přesun virtuálních počítačů
> * Typické architektury
> * Přesun virtuálních počítačů, protože je v cílové oblasti
> * Přesun virtuálních počítačů a zvyšují dostupnost

## <a name="reasons-to-move-azure-vms"></a>Důvody pro přesun virtuálních počítačů Azure

Můžou přesunout virtuální počítače z následujících důvodů:

- Již nasazené v jedné oblasti a jsme přidali nové oblasti podporu, který je blíž ke koncovým uživatelům vaší aplikace nebo služby. V tomto scénáři chcete přesunout virtuální počítače jako nové oblasti pro snížení latence. Pokud chcete konsolidovat předplatná, nebo jestli neexistují nějaké zásady správného řízení nebo organizace pravidel, která vyžadují, abyste přesunout, použijte stejný přístup.
- Váš počítač byl nasazený jako virtuálního počítače s jednou instancí nebo v rámci skupiny dostupnosti. Pokud chcete zvýšit dostupnost smlouvy o úrovni služeb, můžete přesunout virtuální počítače v zóně dostupnosti.

## <a name="steps-to-move-azure-vms"></a>Postup přesunutí virtuálních počítačů Azure

Přesun virtuálních počítačů zahrnuje následující kroky:

1. Ověřte požadavky.
2. Příprava zdrojové virtuální počítače.
3. Připravte cílové oblasti.
4. Kopírování dat do cílové oblasti. Použijte replikační technologii Azure Site Recovery ke kopírování dat ze zdrojového virtuálního počítače do cílové oblasti.
5. Otestujte konfiguraci. Po dokončení replikace testovací převzetí služeb při selhání pro nevýrobní prostředí sítě Otestujte konfiguraci.
6. Provedení přesunu.
7. Zahoďte prostředky ve zdrojové oblasti.

> [!NOTE]
> Podrobnosti o těchto krocích, které jsou k dispozici v následujících částech.
> [!IMPORTANT]
> V současné době Azure Site Recovery podporuje přesun virtuálních počítačů z jedné oblasti do jiného, ale nepodporuje přesun v rámci oblasti.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Typických architekturách vícevrstvých nasazení

Tato část popisuje nejběžnější nasazení architektury pro vícevrstvou aplikaci v Azure. V příkladu je třívrstvá aplikace s veřejnou IP adresu. Každý z úrovní (web, aplikace a databáze) má dva virtuální počítače každý a jsou připojeni pomocí služby Azure load balancer do dalších vrstev. Databázová vrstva je replikace SQL serveru Always On mezi virtuálními počítači pro zajištění vysoké dostupnosti.

* **Jednou instancí virtuálních počítačů nasazených na různých úrovních**: Každý virtuální počítač ve vrstvě je nakonfigurovaný jako virtuálního počítače s jednou instancí a je připojen pomocí nástroje pro vyrovnávání zatížení do dalších vrstev. Tato konfigurace je nejjednodušší přijmout.

     ![Nasazení jednou instancí virtuálních počítačů ve vrstvách](media/move-vm-overview/regular-deployment.png)

* **Virtuální počítače v každé úrovni nasazené ve skupinách dostupnosti**: Každý virtuální počítač ve vrstvě je nakonfigurován ve skupině dostupnosti. [Skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) zajišťují distribuci virtuálních počítačů nasazených v Azure napříč několika izolovanými hardwarovými uzly v clusteru. Tím se zajistí, že pokud selhání hardwaru nebo softwaru v rámci Azure se stane, se vztahuje pouze dílčí část vašich virtuálních počítačů a vaše celkové řešení zůstane dostupné a funkční.

     ![Nasazení virtuálního počítače v skupinách dostupnosti](media/move-vm-overview/avset.png)

* **Virtuální počítače v jednotlivých vrstvách nasazení napříč zónami dostupnosti**: Každý virtuální počítač ve vrstvě je nakonfigurovaný přes [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). Zónu dostupnosti, do oblasti Azure je kombinace doména selhání a aktualizační doména. Například pokud vytvoříte tři nebo více virtuálních počítačů napříč zónami tři v oblasti Azure, vaše virtuální počítače jsou účinně rozloženy na tři domény selhání a aktualizačních doménách tři. Platforma Azure rozpoznává této distribuce napříč aktualizační domény, abyste měli jistotu, že virtuální počítače v různých oblastech nejsou aktualizovány ve stejnou dobu.

     ![Zóna dostupnosti nasazení](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Přesunout virtuální počítače, protože je v cílové oblasti

Na základě [architektury](#typical-architectures-for-a-multi-tier-deployment) již bylo zmíněno dříve, tady je nasazení bude vypadat po provedení přesunutí jako cílové oblasti.

* **Jednou instancí virtuálních počítačů nasazených na různých úrovních**

     ![Nasazení jednou instancí virtuálních počítačů ve vrstvách](media/move-vm-overview/single-zone.png)

* **Virtuální počítače v každé úrovni nasazené ve skupinách dostupnosti**

     ![Pro různé sady dostupnosti oblast](media/move-vm-overview/crossregionaset.png)

* **Virtuální počítače v jednotlivých vrstvách nasazení napříč zónami dostupnosti**

     ![Nasazení virtuálních počítačů napříč zónami dostupnosti](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Přesuňte virtuální počítače, které zvyšují dostupnost

* **Jednou instancí virtuálních počítačů nasazených na různých úrovních**

     ![Nasazení jednou instancí virtuálních počítačů ve vrstvách](media/move-vm-overview/single-zone.png)

* **Virtuální počítače v každé úrovni nasazené ve skupinách dostupnosti**: Můžete konfigurovat vaše virtuální počítače ve skupině dostupnosti do samostatných zón dostupnosti při povolení replikace pro virtuální počítač pomocí Azure Site Recovery. Po dokončení operace přesunutí, bude smlouva SLA pro dostupnost 99,9 %.

     ![Nasazení virtuálního počítače přes skupiny dostupnosti a zóny dostupnosti](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]

> * [Přesuňte virtuální počítače Azure do jiné oblasti](azure-to-azure-tutorial-migrate.md)

> * [Přesuňte virtuální počítače Azure do zóny dostupnosti](move-azure-vms-avset-azone.md)

