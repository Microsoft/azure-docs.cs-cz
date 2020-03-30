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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262167"
---
Disky spravované Azure aktuálně nabízí čtyři typy disků, každý typ je zaměřen na konkrétní scénáře zákazníků.

## <a name="disk-comparison"></a>Porovnání disků

Následující tabulka obsahuje porovnání ultradisků, prémiových ssd disků (SSD), standardních disků SSD a standardních pevných disků (HDD) pro spravované disky, které vám pomohou rozhodnout se, co použít.

|   | Disky Ultra   | SSD úrovně Premium   | SSD úrovně Standard   | HDD úrovně Standard   |
|---------|---------|---------|---------|---------|
|Typ disku   |SSD   |SSD   |SSD   |HDD   |
|Scénář   |Úlohy náročné na io, jako je [SAP HANA](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md), databáze nejvyšší úrovně (například SQL, Oracle) a další úlohy náročné na transakce.   |Úlohy v produkčním prostředí a úlohy, u kterých záleží na výkonu   |Webové servery, málo používané podnikové aplikace a vývoj či testování   |Zálohování, úlohy, které nejsou kritické a používají se zřídka   |
|Maximální velikost disku   |65 536 gibibajtů (GiB)    |32 767 GiB    |32 767 GiB   |32 767 GiB   |
|Maximální propustnost   |2 000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Maximální počet vstupně-výstupních operací za sekundu   |160 000    |20 000   |6 000   |2 000   |

## <a name="ultra-disk"></a>Disky Ultra

Disky Azure Ultra zajišťují vysokou propustnost, vysoké IOPS a představují konzistentní diskové úložiště s nízkou latencí pro virtuální počítače Azure IaaS. Některé další výhody ultra disků zahrnují možnost dynamicky měnit výkon disku spolu s vašimi úlohami, aniž byste museli restartovat virtuální počítače (VM). Disky Ultra jsou vhodné pro úlohy náročné na data, jako jsou úlohy SAP HANA, databáze nejvyšší úrovně a úlohy s velkým počtem transakcí. Disky tohoto typu se dají používat jen jako datové disky. Jako disky s operačním systémem doporučujeme používat disky SSD úrovně Premium.

### <a name="performance"></a>Výkon

Při zřizování ultra disk, můžete nezávisle nakonfigurovat kapacitu a výkon disku. Ultra disky se dodávají v několika pevných velikostech, od 4 GiB až po 64 TiB, a jsou vybaveny flexibilním modelem konfigurace výkonu, který umožňuje nezávisle konfigurovat IOPS a propustnost.

Některé klíčové funkce ultra disků jsou:

- Kapacita disku: Kapacita ultradisků se pohybuje od 4 Gb až po 64 TiB.
- Disk ové vstupně-toopy: Ultra disky podporují limity IOPS 300 IOPS/GiB, maximálně 160 K IOPS na disk. Chcete-li dosáhnout viposlužby, které jste zřídit, ujistěte se, že vybrané diskové vipony jsou menší než limit VM IOPS. Minimální zaručené vstupně-toopy na disk jsou 2 VOPS/GiB s celkovým základním minimem 100 VOPS. Například pokud jste měli 4 GiB ultra disk, budete mít minimálně 100 IOPS, namísto osmi IOPS.
- Propustnost disku: U ultra disků je limit propustnost jednoho disku 256 KiB/s pro každý zřízený vstupně-up, maximálně 2000 MB/s na disk (kde mb/s = 10 ^6 bajtů za sekundu). Minimální zaručená propustnost na disk je 4KiB/s pro každý zřízený vstupně-sad, s celkovým základním minimem 1 Mb/s.
- Ultra disky podporují úpravu atributů výkonu disku (IOPS a propustnost) za běhu bez odpojení disku od virtuálního počítače. Jakmile je na disku vydána operace změny velikosti výkonu disku, může trvat až hodinu, než se změna skutečně projeví. Existuje limit čtyř operací změny velikosti výkonu během 24 hodinového okna. Je možné, že operace změny výkonu se nezdaří z důvodu nedostatku kapacity šířky pásma výkonu.

### <a name="disk-size"></a>Velikost disku

|Velikost disku (GiB)  |Víčko IOPS  |Cap propustnost (MB/s)  |
|---------|---------|---------|
|4     |1 200         |300         |
|8     |2,400         |600         |
|16     |4 800         |1 200         |
|32     |9 600         |2 000         |
|64     |19,200         |2 000         |
|128     |38,400         |2 000         |
|256     |76 800         |2 000         |
|512     |80,000         |2 000         |
|1,024-65,536 (velikosti v tomto rozsahu se zvyšují v přírůstcích po 1 TiB)     |160 000         |2 000         |

### <a name="ga-scope-and-limitations"></a>Rozsah a omezení GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
