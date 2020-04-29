---
title: Přesun virtuálních počítačů Azure do jiné oblasti pomocí Azure Site Recovery
description: Použití Azure Site Recovery k přesunu virtuálních počítačů Azure z jedné oblasti Azure do jiné.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 3f715af835df6783ae5d59dd073a042a553fba4d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75498043"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Přesun virtuálních počítačů Azure do jiné oblasti Azure

Tento článek poskytuje přehled důvodů a kroků, které se podílejí na přesunu virtuálních počítačů Azure do jiné oblasti Azure pomocí [Azure Site Recovery](site-recovery-overview.md). 


## <a name="reasons-to-move-azure-vms"></a>Důvody pro přesun virtuálních počítačů Azure

Virtuální počítače se můžou přesunout z následujících důvodů:

- Již jste nasadili v jedné oblasti a byla přidána podpora nové oblasti, která je blíže koncovým uživatelům vaší aplikace nebo služby. V tomto scénáři byste chtěli přesunout své virtuální počítače tak, aby se snížila latence, protože se jedná o novou oblast. Stejný přístup použijte v případě, že chcete konsolidovat odběry nebo pokud existují pravidla zásad správného řízení nebo organizace, která vyžadují přesunutí.
- Váš virtuální počítač byl nasazený jako virtuální počítač s jednou instancí nebo jako součást skupiny dostupnosti. Pokud chcete zvýšit SLA dostupnosti, můžete virtuální počítače přesunout do zóny dostupnosti.

## <a name="steps-to-move-azure-vms"></a>Postup přesunutí virtuálních počítačů Azure

Přesun virtuálních počítačů zahrnuje následující kroky:

1. Ověřte požadavky.
2. Připravte zdrojové virtuální počítače.
3. Připravte cílovou oblast.
4. Kopírovat data do cílové oblasti. Použijte technologii replikace Azure Site Recovery ke kopírování dat ze zdrojového virtuálního počítače do cílové oblasti.
5. Otestujte konfiguraci. Po dokončení replikace otestujte konfiguraci provedením testovacího převzetí služeb při selhání do jiné než produkční sítě.
6. Proveďte přesun.
7. Zahodí prostředky ve zdrojové oblasti.

> [!NOTE]
> Podrobnosti o těchto krocích jsou uvedené v následujících oddílech.
> [!IMPORTANT]
> V současné době Azure Site Recovery podporuje přesun virtuálních počítačů z jedné oblasti do druhé, ale nepodporuje přesun v rámci oblasti.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Typická architektura pro nasazení ve více vrstvách

Tato část popisuje nejběžnější architektury nasazení pro vícevrstvou aplikaci v Azure. Příkladem je tři vrstvená aplikace s veřejnou IP adresou. Každá z vrstev (web, aplikace a databáze) má dva virtuální počítače a připojuje se pomocí nástroje pro vyrovnávání zatížení Azure k ostatním vrstvám. Databázová vrstva má pro vysokou dostupnost trvale SQL Server replikaci mezi virtuálními počítači.

* **Virtuální počítače s jednou instancí nasazené napříč různými úrovněmi**: každý virtuální počítač ve vrstvě je nakonfigurovaný jako virtuální počítač s jednou instancí a je připojený pomocí nástrojů pro vyrovnávání zatížení k ostatním vrstvám. Tato konfigurace je nejjednodušší k přijetí.

     ![Nasazení virtuálních počítačů s jednou instancí napříč úrovněmi](media/move-vm-overview/regular-deployment.png)

* **Virtuální počítače v každé vrstvě nasazené napříč**skupinami dostupnosti: každý virtuální počítač v úrovni je nakonfigurovaný v sadě dostupnosti. [Skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) zajišťují, že virtuální počítače, které nasazujete v Azure, jsou distribuované napříč několika izolovanými hardwarovými uzly v clusteru. Tím se zajistí, že pokud dojde k selhání hardwaru nebo softwaru v rámci Azure, ovlivní to jenom podmnožinu vašich virtuálních počítačů a vaše celkové řešení zůstane dostupné a funkční.

     ![Nasazení virtuálních počítačů napříč skupinami dostupnosti](media/move-vm-overview/avset.png)

* **Virtuální počítače v každé vrstvě nasazené napříč zóny dostupnosti**: každý virtuální počítač v úrovni je nakonfigurovaný přes [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). Zóna dostupnosti v oblasti Azure je kombinací domény selhání a aktualizační domény. Pokud například vytvoříte tři nebo více virtuálních počítačů ve třech zónách v oblasti Azure, budou vaše virtuální počítače efektivně distribuovány mezi tři domény selhání a tři aktualizační domény. Platforma Azure tuto distribuci rozpoznává mezi aktualizačními doménami, aby se zajistilo, že se virtuální počítače v různých zónách neaktualizují současně.

     ![Nasazení zóny dostupnosti](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Přesunutí virtuálních počítačů do cílové oblasti

V závislosti na tom, jaké [architektury](#typical-architectures-for-a-multi-tier-deployment) jsou zmíněné dříve, bude nasazení vypadat podobně, jako když provedete přesun do cílové oblasti.

* **Virtuální počítače s jednou instancí nasazené napříč různými úrovněmi**

     ![Nasazení virtuálních počítačů s jednou instancí napříč úrovněmi](media/move-vm-overview/single-zone.png)

* **Virtuální počítače v každé vrstvě nasazené napříč skupinami dostupnosti**

     ![Skupiny dostupnosti pro různé oblasti](media/move-vm-overview/crossregionaset.png)

* **Virtuální počítače v každé vrstvě nasazené napříč Zóny dostupnosti**

     ![Nasazení virtuálních počítačů v rámci Zóny dostupnosti](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Přesunutí virtuálních počítačů za účelem zvýšení dostupnosti

* **Virtuální počítače s jednou instancí nasazené napříč různými úrovněmi**

     ![Nasazení virtuálních počítačů s jednou instancí napříč úrovněmi](media/move-vm-overview/single-zone.png)

* **Virtuální počítače v každé vrstvě nasazené napříč**skupinami dostupnosti: virtuální počítače můžete nakonfigurovat v sadě dostupnosti na samostatné zóny dostupnosti, když pro virtuální počítač povolíte replikaci pomocí Azure Site Recovery. Smlouva SLA pro dostupnost bude po dokončení operace přesunutí 99,99%.

     ![Nasazení virtuálních počítačů v rámci skupin dostupnosti a Zóny dostupnosti](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> 
> * [Přesun virtuálních počítačů Azure do jiné oblasti](azure-to-azure-tutorial-migrate.md)
> 
> * [Přesun virtuálních počítačů Azure do zón dostupnosti](move-azure-vms-avset-azone.md)

