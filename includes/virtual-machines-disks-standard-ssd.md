---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f8ca716f4ab991fecca52ca2d5fed080e6f4c177
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060360"
---
# <a name="standard-ssd-managed-disks-for-azure-virtual-machine-workloads"></a>Standardní spravované disky SSD pro úlohy Azure virtuálních počítačů

Služba Azure Managed Disks Solid stavu disky (SSD na úrovni Standard) jsou řešením nákladově efektivní úložiště optimalizované pro úlohy, které je třeba zajistit konzistentní výkon na nižších úrovních vstupně-výstupních operací. SSD na úrovni Standard nabízí prostředí dobrý základní úrovně pro ty, kteří chtějí přesunout do cloudu, zejména v případě, že dochází k problémům s odchylkou úloh běžících na řešení pevný disk v místním prostředí. Standardní disky SSD poskytovat lepší dostupnosti, konzistence, spolehlivost a latenci v porovnání s HDD disky a jsou vhodné pro webové servery, s nízkou vstupně-výstupních operací aplikační servery, málo používaná podnikových aplikací a vývojové a testovací úlohy.

## <a name="standard-ssd-features"></a>Funkce SSD na úrovni Standard

**Spravované disky**: SSD disky Standard jsou dostupné jenom jako Managed Disks. Objekty BLOB stránky a nespravované disky nejsou podporovány na SSD na úrovni Standard. Při vytváření spravovaného disku, můžete zadat typ disku jako SSD na úrovni Standard a určení velikosti disku potřebujete, a Azure vytvoří a spravuje disk za vás.
SSD disky standard podporují všechny operace správy služby Managed Disks nabízí. Například můžete vytvořit, kopírovat nebo snímku standardní spravované disky SSD stejným způsobem se službou Managed Disks.

**Virtuální počítače**: standardní disky SSD, je možné všechny virtuální počítače Azure, včetně typů virtuálních počítačů, které nepodporují prémiové disky. Například, pokud používáte virtuálních počítačů řady A-series, nebo virtuálních počítačů řady N-series nebo řady DS-series nebo jiné řady virtuálních počítačů Azure můžete používat standardní disky SSD s tohoto virtuálního počítače. Se zavedením SSD na úrovni Standard jsme uvolnili širokou škálu úloh, které dříve používají disky založené na pevný disk pro přechod do založené na discích SSD disků a konzistentní výkon, vyšší dostupnost, latenci lepší a celkové lepší prostředí, které jsou součástí vlastnosti jednotek SSD.

**Vysoká odolnost a dostupnost**: standardní disky SSD jsou vytvořeny v rámci téže platformy Azure Disks, který konzistentně dodal vysokou dostupnost a odolnost pro disky. Disky Azure jsou navržené pro 99,999 % dostupnost. Podobně jako všechny spravované disky standardní disky SSD nabídne také místní redundantní úložiště (LRS). Při použití úložiště LRS, platforma udržuje několik replik dat pro každý disk a konzistentně dodal na podnikové úrovni odolnosti pro IaaS disky se špičkovou NULOVOU procenta s hodinovou chybovost.

**Snímky**: jako všechny Managed Disks, standardní disky SSD také podporují vytváření snímků. Typ snímku může být buď Standard (HDD), nebo Premium (SSD). Náklady na ukládání, doporučujeme typ snímku z Standard (HDD) pro všechny typy disků Azure. Je to proto, že při vytváření spravovaného disku ze snímku, dokážete vždy vybrat vyšší úroveň, jako je například SSD na úrovni Standard nebo Premium SSD.

## <a name="scalability-and-performance-targets"></a>Škálovatelnost a cíle výkonnosti

Následující tabulka obsahuje velikosti disků, které jsou v současné době nabízena SSD na úrovni Standard.

|Typ disku SSD na úrovni Standard  |Velikost disku  |IOPS na Disk  |Propustnost / disk  |
|---------|---------|---------|---------|
|E10     |128 GiB         |Až 500         |Až 60 MiB za sekundu         |
|E15     |256 GiB         |Až 500         |Až 60 MiB za sekundu         |
|E20     |512 GiB         |Až 500         |Až 60 MiB za sekundu         |
|E30     |1 024 GB       |Až 500         |Až 60 MiB za sekundu         |
|E40     |2 048 GB       |Až 500         |Až 60 MiB za sekundu         |
|E50     |4 095 GB       |Až 500         |Až 60 MiB za sekundu         |
|E60     |8 192 giB       |1 až 300       |Až 300 MiB za sekundu        |
|E70     |16 384 GB      |Až 2 000       |Až 500 MiB za sekundu        |
|E80     |32 767 giB      |Až 2 000       |Až 500 MiB za sekundu        |

Standardní disky SSD jsou navržené k poskytnutí latence v řádu milisekund pro většinu operací vstupně-výstupních operací a k poskytování IOPS a propustnost až do omezení je popsáno v tabulce výše. Skutečné IOPS a propustnost může někdy lišit v závislosti na vzory přenosů. Standardní disky SSD zajistí konzistentnější výkon než HDD disky s nižší latencí.

Disky Premium SSD na druhé straně poskytují vyšší výkon než standardní disky SSD s nízkou latencí, vysoké IOPS a propustnosti a konzistence ještě lepší výkon zřízených disků. Je doporučené diskové typ pro důležité produkční úlohy. Pokud vaše úloha vyžaduje podporu vysoce výkonných disků s nízkou latencí, zvažte použití služby Premium Storage.

Standardní disky SSD jako disky Premium SSD také použít velikost 256 KiB jednotky vstupně-výstupních operací. Méně než 256 KiB při přenosu dat se považuje za 1 jednotkovou vstupně-výstupních operací. Větší velikosti vstupně-výstupní operace se počítají jako více vstupně-výstupních operací, o velikosti 256 KiB. Například 1100 KiB vstupně-výstupní operace se počítá jako pět jednotky vstupně-výstupních operací.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Při použití standardní disky SSD, platí následující aspekty fakturace:

- Spravovaná velikost disku
- Snímky
- Přenosy odchozích dat
- Transakce

**Spravovaná velikost disku**: spravované disky se účtují na zřízená velikost. Azure maps zřízená velikost (zaokrouhluje nahoru) na nejbližší nabídku velikost disku. Podrobnosti o velikosti disků nabízejí najdete v tabulce škálovatelnost a cíle výkonnosti výše v části. Každý disk mapuje na velikosti zřízeného disku podporované a proto se neúčtují. Například pokud jste zřídili 200 GB SSD na úrovni Standard, mapuje na nabídku velikost disku z E15 (256 GB). Za všechny zajišťovaným diskem se fakturuje po hodinách pomocí cenu za měsíc pro nabídku služby Premium Storage. Například pokud zřízené disk s E10 a odstraní ji po 20 hodin, bude se vám účtovat pro nabídky E10 nebo jeho poměrnou část 20 hodin. To je bez ohledu na velikost skutečných dat zapsaných na disk.

**Snímky**: snímky Managed Disks se vám účtovat kapacita použitá snímky v cílovém a ve zdroji, pokud existuje. Další informace o snímků, najdete v části [snímky služby Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#managed-disk-snapshots).

**Přenosy odchozích dat**: [přenosy odchozích dat](https://azure.microsoft.com/pricing/details/bandwidth/) (data odcházející z Datacenter Azure) jsou zpoplatněné využití šířky pásma.

**Transakce**: podobně jako standardní HDD účtovat transakce na standardní disky SSD fakturace. Transakce čtení a zápis na disk. Velikost jednotky vstupně-výstupní operace používá pro účtování transakce na SSD na úrovni Standard je 256 KiB. Větší velikosti vstupně-výstupní operace se počítají jako více vstupně-výstupních operací, o velikosti 256 KiB.

Další informace o cenách Virtual Machines a Managed Disks najdete v tématu:

- [Ceny Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [Ceny služby Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="next-steps"></a>Další postup

Další informace o vytváření standardních disků SSD, naleznete příklad, který ukazuje, jak vytvořit virtuální počítač s více standardní disky SSD.

> [!div class="nextstepaction"]
> [Vytvoření Virtuálního počítače s více standardní disky SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)