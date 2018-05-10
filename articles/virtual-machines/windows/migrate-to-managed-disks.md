---
title: Migrovat virtuální počítače Azure k spravovaná diskům | Microsoft Docs
description: Migrujte virtuální počítače Azure vytvořené pomocí nespravované disky v účtech úložiště použít spravované disky.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.openlocfilehash: d280ad1180949167bb8ebfc6b21521736db0f55d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrovat virtuální počítače Azure k spravovaná diskům v Azure

Disky spravované Azure zjednodušuje správu vašeho úložiště odebráním potřeba samostatně spravovat účty úložiště.  Můžete také migrovat existující virtuální počítače Azure spravované disků, abyste mohli využívat výhod vyšší spolehlivost virtuálních počítačů ve skupině dostupnosti. Zajišťuje, že disky různé virtuální počítače ve skupině dostupnosti je dostatečně od sebe navzájem oddělené předejdete jediný bod selhání. Automaticky umístí disky pro různé virtuální počítače do skupiny dostupnosti v různých jednotek škálování úložiště (razítka) omezující vliv jednoho úložiště škálování jednotky chyby způsobené hardwaru a chyby softwaru.
V závislosti na vašich potřebách můžete vybrat ze dvou typů možností úložiště:

- [Pro prémiové disky spravované](premium-storage.md) jsou plnou stavu jednotky SSD (Solid-State Drive) založené na úložná média, který poskytuje vysoký výkon, podporu disků s nízkou latencí pro virtuální počítače běžící I náročnými úlohy. Migrací prémiové disky spravované můžete využít výhod rychlosti a výkonu z těchto disků.

- [Standardní disky spravované](standard-storage.md) používat jednotku pevného disku (HDD) na základě paměťových médií a jsou nejvhodnější pro vývoj/testování a další úlohy nepravidelným přístup, které jsou menší citlivá s ohledem na výkon proměnlivost.

Můžete migrovat do spravované disky v těchto scénářích:

| Migrujte...                                            | Dokumentace k propojení                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Převést samostatný virtuální počítače a virtuální počítače ve skupině dostupnosti nastavena na spravované disky   | [Převést virtuální počítače použít spravované disky](convert-unmanaged-to-managed-disks.md) |
| Jeden virtuální počítač z classic do Resource Manager na discích spravovaných     | [Migrovat jeden virtuální počítač](migrate-single-classic-to-resource-manager.md)  | 
| Všechny virtuální počítače ve virtuální síti z classic do Resource Manager na discích spravovaných     | [Migrovat prostředky infrastruktury z classic do Resource Manager](migration-classic-resource-manager-ps.md) a potom [převeďte virtuální počítač z disků nespravované na spravované disky](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>Plán pro převod na spravované disky

V této části můžete vytvořit nejlepší rozhodnutí o typech virtuálního počítače a disku.


## <a name="location"></a>Umístění

Vyberte umístění, kde Azure spravované disky jsou dostupné. Pokud přecházíte na prémiové disky spravované, ujistěte se také, že úložiště Premium je k dispozici v oblasti, kde máte v úmyslu přesunout do. V tématu [služby Azure podle oblasti](https://azure.microsoft.com/regions/#services) aktuální informace o dostupných umístění.

## <a name="vm-sizes"></a>Velikost virtuálních počítačů

Pokud provádíte migraci na disky spravované Premium, budete muset aktualizovat velikost virtuálního počítače do Storage úrovně Premium podporující velikost dostupné v oblasti, kde je umístěn virtuální počítač. Zkontrolujte velikosti virtuálních počítačů, které jsou podporující Storage úrovně Premium. Specifikace velikosti virtuálního počítače Azure, jsou uvedeny v [velikosti virtuálních počítačů](sizes.md).
Zkontrolujte vlastnosti výkonu virtuálních počítačů, které pracovat Storage úrovně Premium a vyberete nejvhodnější velikost virtuálního počítače, který nejlépe vyhovuje vaší zatížení. Ujistěte se, že je dostatečnou šířku pásma dostupné na vašem virtuálním počítači k řízení provozu disku.

## <a name="disk-sizes"></a>Velikost disků

**Premium spravované disky**

Existují sedm typy premium spravované disky, které lze použít s vaší virtuálních počítačů a každá z nich má konkrétní IOPs a propustnost omezení. Vzít v úvahu tyto limity při výběru typu Premium disku pro virtuální počítač podle potřeb vaší aplikace z hlediska kapacity, výkon, škálovatelnost a načte ve špičce.

| Disky typu Premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Velikost disku           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Vstupně-výstupní operace za sekundu / disk       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Propustnost / disk | 25 MB za sekundu  | 50 MB za sekundu  | 100 MB za sekundu | 125 MB za sekundu |150 MB za sekundu | 200 MB za sekundu | 250 MB za sekundu | 250 MB za sekundu |

**Standardní disky spravované**

Existují sedm typy standardní spravovaných disků, které lze použít s virtuálního počítače. Každý z nich mají různé kapacity ale mít stejné IOPS a omezení propustnosti. Vyberte typ standardní spravovaných disků na základě potřeb kapacitu vaší aplikace.

| Disk typu Standard  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Velikost disku           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| Vstupně-výstupní operace za sekundu / disk       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Propustnost / disk | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu |60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 

## <a name="disk-caching-policy"></a>Zásady ukládání do mezipaměti na disku

**Premium spravované disky**

Ve výchozím nastavení, je disk ukládání do mezipaměti zásad *jen pro čtení* pro všechny Premium datových disků, a *pro čtení a zápis* pro disk operačního systému Premium připojen k virtuálnímu počítači. Toto nastavení konfigurace se doporučuje pro dosažení optimálního výkonu pro vaše aplikace IOs. Těžký zápisu nebo pouze pro zápis datové disky (například soubory protokolu serveru SQL Server) zakažte ukládání do mezipaměti disku, takže můžete dosáhnout lepší výkon aplikace.

## <a name="pricing"></a>Ceny

Zkontrolujte [ceny pro spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/). Ceny disků spravované Premium je stejný jako nespravované prémiové disky. Ale ceny pro standardní disky spravované se liší od standardní nespravované disky.



## <a name="next-steps"></a>Další postup

- Další informace o [spravované disky](managed-disks-overview.md)
