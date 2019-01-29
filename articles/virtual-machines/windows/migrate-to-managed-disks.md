---
title: Migrace virtuálních počítačů Azure na Managed Disks | Dokumentace Microsoftu
description: Migrate virtuální počítače Azure vytvořené využitím nespravovaných disků na spravované disky v účtech úložiště.
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
ms.component: disks
ms.openlocfilehash: b87e27ae914a01f03ce78eafe5792433d18e417f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193705"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrace virtuálních počítačů Azure na Managed Disks v Azure

Azure Managed Disks zjednodušuje správu úložiště tím, že si nemusíte samostatně spravovat účty úložiště.  Můžete také migrovat vaše stávající virtuální počítače Azure do služby Managed Disks, abyste využili výhod vyšší spolehlivost virtuálních počítačů ve skupině dostupnosti. Zajišťuje, že disky různých virtuálních počítačů ve skupině dostupnosti je dostatečně od sebe navzájem oddělené, aby jediný bod selhání. Automaticky umístí disky různých virtuálních počítačů ve skupině dostupnosti v různých Uložišť jednotek škálování (razítka) který omezuje dopad selhání škálovací jednotky jednoho úložiště se situace způsobena vlivem hardwaru a softwaru selhání.
Podle svých potřeb, můžete vybrat ze dvou typů úložišť:

- [Managed Disks úrovně Premium](premium-storage.md) jsou úložná média, která nabízí vysoký výkon, podporu disků s nízkou latencí pro virtuální počítače spuštěné můžu intenzivních vstupně-výstupních operací založená na plné stav jednotky SSD (Solid-State Drive). Pomocí migrace na Premium Managed Disks můžete využít výhod rychlost a výkon těchto disků.

- [Standard Managed Disks](standard-storage.md) používat jednotku pevného disku (HDD) na základě úložných médií a jsou nejvhodnější pro vývoj/testování a jiné úlohy s řídkým přístupem úlohy, které jsou méně citlivé na variabilitu výkonu.

Můžete migrovat do Managed Disks v následujících scénářích:

| Migrate...                                            | Odkaz na dokumentaci                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Převést samostatné virtuální počítače a virtuální počítače ve skupině dostupnosti na spravované disky   | [Převod virtuálních počítačů použít spravované disky](convert-unmanaged-to-managed-disks.md) |
| Jeden virtuální počítač z modelu nasazení classic do Resource Manageru na spravovaných discích     | [Vytvoření virtuálního počítače z klasického virtuálního pevného disku](create-vm-specialized-portal.md)  | 
| Všechny virtuální počítače ve virtuální síti z modelu nasazení classic do Resource Manageru na spravovaných discích     | [Migrace prostředků IaaS z modelu classic na Resource Manager](migration-classic-resource-manager-ps.md) a potom [převod virtuálního počítače z nespravovaných disků na managed disks](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>Plán pro převod na Managed Disks

V této části vám umožní nejlepší rozhodnutí o typech virtuálních počítačů a disk.


## <a name="location"></a>Umístění

Vyberte umístění, kde jsou k dispozici Azure Managed Disks. Pokud přecházíte na Managed Disks úrovně Premium, ujistěte se také, že Premium storage je dostupné v oblasti, kde máte v úmyslu přesunout do. Zobrazit [služeb Azure podle oblasti](https://azure.microsoft.com/regions/#services) aktuální informace o dostupných umístění.

## <a name="vm-sizes"></a>Velikost virtuálních počítačů

Pokud provádíte migraci do služby Managed Disks úrovně Premium, budete muset aktualizovat velikost virtuálního počítače do Premium Storage podporuje velikost dostupná v oblasti, kde je umístěn virtuální počítač. Kontrola velikosti virtuálních počítačů, které jsou schopné Premium Storage. Specifikace velikosti virtuálního počítače Azure jsou uvedeny v [velikosti virtuálních počítačů](sizes.md).
Zkontrolujte charakteristiky výkonu virtuálních počítačů, které pracují se službou Premium Storage a zvolte nejvhodnější velikosti virtuálního počítače, který nejlépe vyhovuje vaší úlohy. Ujistěte se, že je dostatečná šířka pásma dostupné na virtuálním počítači Centrum umožňující prosazovat diskové přenosy.

## <a name="disk-sizes"></a>Velikost disků

**Premium Managed Disks**

Existuje sedm typů spravovaných disků úrovně premium se dají s virtuálním Počítačem a obsahují konkrétní IOPs a propustnost omezení. Vzít v úvahu tato omezení, pokud vyberete disk typu Premium pro virtuální počítač podle potřeb vaší aplikace z hlediska kapacity, výkonu, škálovatelnosti a načte ve špičce.

| Typ disky Premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Velikost disku           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Vstupně-výstupní operace za sekundu / disk       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Propustnost / disk | 25 MB za sekundu  | 50 MB za sekundu  | 100 MB za sekundu | 125 MB za sekundu |150 MB za sekundu | 200 MB za sekundu | 250 MB za sekundu | 250 MB za sekundu |

**Spravované disky úrovně Standard**

Existuje sedm typů spravované disky úrovně standard, které lze použít s virtuálním Počítačem. Každý z nich mít jiné kapacitě, ale mají stejné vstupně-výstupních operací a omezení propustnosti. Zvolte typ služby Standard Managed disks na základě potřeb kapacity vaší aplikace.

| Disk typu Standard  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Velikost disku           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| Vstupně-výstupní operace za sekundu / disk       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Propustnost / disk | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu |60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 

## <a name="disk-caching-policy"></a>Zásady ukládání do mezipaměti na disku

**Premium Managed Disks**

Ve výchozím nastavení, disk zásady ukládání do mezipaměti je *jen pro čtení* pro všechny úrovně Premium datové disky, a *čtení a zápis* pro disk s operačním systémem Premium připojené k virtuálnímu počítači. Toto nastavení konfigurace se doporučuje pro zajištění optimálního výkonu pro vaše aplikace IOs. Náročné na zápis nebo jen pro zápis datové disky (jako jsou například soubory protokolu serveru SQL Server) Zakázání používání mezipaměti disku, takže můžete dosáhnout lepší výkon aplikace.

## <a name="pricing"></a>Ceny

Zkontrolujte [ceny pro Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). Ceny služby Managed Disks úrovně Premium je stejná jako nespravované disky Premium. Ale ceny pro Standard Managed Disks se liší od Unmanaged Disks úrovně Standard.



## <a name="next-steps"></a>Další postup

- Další informace o [Managed Disks](managed-disks-overview.md)
