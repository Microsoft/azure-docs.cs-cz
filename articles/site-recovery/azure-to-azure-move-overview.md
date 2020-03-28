---
title: Přesunutí virtuálních počítačů Azure do jiné oblasti pomocí Azure Site Recovery
description: Pomocí Azure Site Recovery přesunout virtuální počítače Azure z jedné oblasti Azure do jiné.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 3f715af835df6783ae5d59dd073a042a553fba4d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498043"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Přesunutí virtuálních počítačů Azure do jiné oblasti Azure

Tento článek obsahuje přehled důvodů a kroků spojených s přesunutím virtuálních virtuálních počítače Azure do jiné oblasti Azure pomocí [Azure Site Recovery](site-recovery-overview.md). 


## <a name="reasons-to-move-azure-vms"></a>Důvody pro přesun virtuálních počítačů Azure

Virtuální hotely můžete přesunout z následujících důvodů:

- Již jste nasadili v jedné oblasti a byla přidána nová podpora oblasti, která je blíže koncovým uživatelům vaší aplikace nebo služby. V tomto scénáři byste chtěli přesunout virtuální počítače, jako je do nové oblasti ke snížení latence. Stejný přístup použijte, pokud chcete konsolidovat předplatná nebo pokud existují pravidla zásad správného řízení nebo organizace, která vyžadují přesunutí.
- Váš virtuální počítač byl nasazený jako virtuální počítač s jednou instancí nebo jako součást skupiny dostupnosti. Pokud chcete zvýšit dostupnost sla, můžete přesunout virtuální počítače do zóny dostupnosti.

## <a name="steps-to-move-azure-vms"></a>Postup přesunutí virtuálních počítačů Azure

Přesunutí virtuálních společností zahrnuje následující kroky:

1. Ověřte požadavky.
2. Připravte zdrojové virtuální počítače.
3. Připravte cílovou oblast.
4. Zkopírujte data do cílové oblasti. Pomocí technologie replikace obnovení lokality Azure zkopírujte data ze zdrojového virtuálního počítače do cílové oblasti.
5. Otestujte konfiguraci. Po dokončení replikace otestujte konfiguraci provedením zkušebního převzetí služeb při selhání v neprodukční síti.
6. Proveďte pohyb.
7. Zahodit prostředky ve zdrojové oblasti.

> [!NOTE]
> Podrobnosti o těchto krocích jsou uvedeny v následujících částech.
> [!IMPORTANT]
> V současné době Azure Site Recovery podporuje přesun virtuálních počítačů z jedné oblasti do druhé, ale nepodporuje přesun v rámci oblasti.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Typické architektury pro vícevrstvé nasazení

Tato část popisuje nejběžnější architektury nasazení pro vícevrstvou aplikaci v Azure. Příkladem je třívrstvá aplikace s veřejnou IP adresou. Každá z vrstev (web, aplikace a databáze) má dva virtuální počítače a jsou propojeny pomocí azure balancer na jiné vrstvy. Databázová vrstva má SQL Server always on replikace mezi virtuálními počítači pro vysokou dostupnost.

* **Virtuální počítače s jednou instancí nasazené napříč různými vrstvami**: Každý virtuální virtuální počítače na vrstvě je nakonfigurovaný jako virtuální virtuální počítače s jednou instancí a je připojený vykladači zatížení k ostatním vrstvám. Tato konfigurace je nejjednodušší přijmout.

     ![Nasazení virtuálních počítače s jednou instancí napříč vrstvami](media/move-vm-overview/regular-deployment.png)

* **Virtuální počítače v každé vrstvě nasazené napříč sadami dostupnosti**: Každý virtuální virtuální počítače ve vrstvě se nakonfiguruje v sadě dostupnosti. [Skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) zajišťují, že virtuální počítače, které nasadíte v Azure, jsou distribuovány mezi více izolovanými hardwarovými uzly v clusteru. Tím zajistíte, že pokud dojde k selhání hardwaru nebo softwaru v rámci Azure, bude ovlivněna pouze podmnožina vašich virtuálních počítačích a vaše celkové řešení zůstane dostupné a funkční.

     ![Nasazení virtuálních virtuálních počítače napříč sadami dostupnosti](media/move-vm-overview/avset.png)

* **Virtuální počítače v každé vrstvě nasazené napříč zónami dostupnosti:** Každý virtuální virtuální počítače ve vrstvě se nakonfiguruje napříč [zónami dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). Zóna dostupnosti v oblasti Azure je kombinací domény selhání a aktualizační domény. Pokud například vytvoříte tři nebo více virtuálních počítačů ve třech zónách v oblasti Azure, vaše virtuální počítače se efektivně distribuují mezi tři domény selhání a tři aktualizační domény. Platforma Azure rozpoznává tuto distribuci mezi aktualizačními doménami, aby se ujistila, že virtuální počítače v různých zónách nejsou aktualizovány současně.

     ![Nasazení zóny dostupnosti](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Přesunutí virtuálních mích stejně jako do cílové oblasti

Na základě architektury [uvedené](#typical-architectures-for-a-multi-tier-deployment) výše, tady je to, co nasazení bude vypadat po provedení přesunutí, jak je do cílové oblasti.

* **Virtuální virtuální ms s jednou instancí nasazené na různých úrovních**

     ![Nasazení virtuálních počítače s jednou instancí napříč vrstvami](media/move-vm-overview/single-zone.png)

* **Virtuální virtuální virtuální telefony v každé vrstvě nasazené napříč sadami dostupnosti**

     ![Skupiny dostupnosti napříč oblastmi](media/move-vm-overview/crossregionaset.png)

* **Virtuální virtuální virtuální společnosti v každé vrstvě nasazené napříč zónami dostupnosti**

     ![Nasazení virtuálního počítače napříč zónami dostupnosti](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Přesunutí virtuálních aplikací pro zvýšení dostupnosti

* **Virtuální virtuální ms s jednou instancí nasazené na různých úrovních**

     ![Nasazení virtuálních počítače s jednou instancí napříč vrstvami](media/move-vm-overview/single-zone.png)

* **Virtuální počítače v každé vrstvě nasazené napříč sadami dostupnosti**: Virtuální počítače můžete nakonfigurovat v sadě dostupnosti do samostatných zón dostupnosti, když povolíte replikaci pro váš virtuální počítač pomocí Azure Site Recovery. SLA pro dostupnost bude 99.99% po dokončení operace přesunutí.

     ![Nasazení virtuálních virtuálních počítače napříč sadami dostupnosti a zónami dostupnosti](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> 
> * [Přesun virtuálních počítačů Azure do jiné oblasti](azure-to-azure-tutorial-migrate.md)
> 
> * [Přesun virtuálních počítačů Azure do zón dostupnosti](move-azure-vms-avset-azone.md)

