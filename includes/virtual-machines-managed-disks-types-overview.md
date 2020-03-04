---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72d87142f9b9c1f7bcb2b02281851bd1e29bc9c8
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262167"
---
Služba Azure Managed disks v současné době nabízí čtyři typy disků. každý typ je zaměřený na konkrétní scénáře zákazníků.

## <a name="disk-comparison"></a>Porovnání disků

V následující tabulce najdete porovnání disků Ultra, Premium Solid-State Drives (SSD), Standard SSD a standardních pevných disků (HDD) pro spravované disky, které vám pomůžou rozhodnout se, co použít.

|   | Ultra disk   | SSD úrovně Premium   | SSD úrovně Standard   | HDD úrovně Standard   |
|---------|---------|---------|---------|---------|
|Typ disku   |SSD   |SSD   |SSD   |HDD   |
|Scénář   |Úlohy náročné na v/v, jako jsou [SAP HANA](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md), databáze nejvyšší úrovně (například SQL, Oracle) a další úlohy náročné na transakce.   |Úlohy v produkčním prostředí a úlohy, u kterých záleží na výkonu   |Webové servery, lehce používané podnikové aplikace a vývoj a testování   |Zálohování, Nekritická, zřídka přístup   |
|Maximální velikost disku   |65 536 gibibajt (GiB)    |32 767 GiB    |32 767 GiB   |32 767 GiB   |
|Maximální propustnost   |soubory MiB 2 000/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Maximální počet vstupně-výstupních operací za sekundu   |160 000    |20,000   |6,000   |2,000   |

## <a name="ultra-disk"></a>Ultra disk

Disky Azure Ultra poskytují vysokou propustnost, vysoké IOPS a konzistentní diskové úložiště s nízkou latencí pro virtuální počítače Azure s IaaS. Mezi další výhody prostředí Ultra disks patří schopnost dynamicky měnit výkon disku společně s vašimi úlohami, aniž by bylo potřeba restartovat virtuální počítač. Disky Ultra jsou vhodné pro úlohy náročné na data, jako jsou SAP HANA, databáze nejvyšší úrovně a zatížení náročné na transakce. Disky Ultra se dají používat jenom jako datové disky. Jako disky s operačním systémem doporučujeme používat prémiové SSD.

### <a name="performance"></a>Výkon

Když zřizujete disk Ultra, můžete nezávisle konfigurovat kapacitu a výkon disku. Disky Ultra přicházejí v několika pevných velikostech od 4 GiB až 64 TiB a funkce flexibilního modelu konfigurace výkonu, který umožňuje nezávisle konfigurovat vstupně-výstupní operace a propustnost.

Mezi klíčové funkce Ultra diskù patří:

- Kapacita disku: rozsahy disků Ultra jsou v rozsahu 4 GiB až 64 TiB.
- Disk IOPS: disky Ultra podporují limity IOPS 300 IOPS/GiB, až do maximálního počtu 160 kB za sekundu na disk. Abyste dosáhli IOPS, které jste zřídili, zajistěte, aby byl vybraný disk IOPS menší než limit počtu IOPS virtuálních počítačů. Minimální zaručená IOPS na disk je 2 IOPS/GiB, přičemž celkové minimální hodnoty jsou 100 IOPS. Pokud byste například měli 4 GiB disk Ultra, budete mít minimálně 100 IOPS, ale ne osm IOPS.
- Propustnost disku: s Ultra disky je pro každý zřízený IOPS povolený limit propustnosti jednoho disku 256 KiB/s až do maximálního počtu 2000 MB/s na disk (kde MB/s = 10 ^ 6 bajtů za sekundu). Minimální zaručená propustnost na disk je 4KiB/s pro každý zřízený IOPS, přičemž celkové minimální hodnoty jsou 1 MB/s.
- Disky Ultra podporují úpravu atributů výkonu disku (IOPS a propustnosti) za běhu bez odpojení disku od virtuálního počítače. Jakmile se na disku vystavila operace změny velikosti výkonu disku, může trvat až hodinu, než se změna projeví. V průběhu 24 hodin je omezeno čtyři operace změny velikosti výkonu. Je možné, že operace změny velikosti výkonu selže z důvodu nedostatečné kapacity šířky pásma výkonu.

### <a name="disk-size"></a>Velikost disku

|Velikost disku (GiB)  |Limit IOPS  |Limit propustnosti (MB/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9 600         |2,000         |
|64     |19 200         |2,000         |
|128     |38 400         |2,000         |
|256     |76 800         |2,000         |
|512     |80,000         |2,000         |
|1024 – 65536 (velikosti v tomto rozsahu se zvyšují v přírůstcích po 1 TiB)     |160 000         |2,000         |

### <a name="ga-scope-and-limitations"></a>Rozsah a omezení GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
