---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 961f82cd4970abfdd11a30b2847a14f8ff1880b0
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454410"
---
Úložiště optimalizované velikosti virtuálních počítačů nabízejí Vysoká propustnost disku a vstupně-výstupní operace a jsou ideální pro velké objemy dat, SQL a NoSQL databáze. Tento článek obsahuje informace o počtu virtuálních procesorů, datové disky a síťové adaptéry, jakož i úložiště propustnost a šířku pásma sítě pro jednotlivé velikosti v této skupině. 

Řada Ls-series nabízí až 32 virtuálních procesorů a využívá [řadu procesorů Intel® Xeon® E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Řada Ls-series má stejný výkon procesoru jako řady G/GS-series a 8 GiB paměti na virtuální procesor.  Virtuální počítače řady ls-series jsou ideální pro aplikace vyžadující nízkou latenci, vysokou propustnost a velké místní úložiště na disku. 

Vzorové případy použití zahrnují databáze NoSQL, jako jsou Cassandra, MongoDB, Cloudera a Redis, datových skladů a velké transakční databáze.

> [!NOTE]
> Řada Ls-series je optimalizována pro použití dočasného disku připojené k virtuálnímu počítači počítače nikoli použitím trvalých datových disků. Vysoká propustnost a IOPS dočasný disk je řada Ls-series ideální pro úložišť typu NoSQL, jako je například Apache Cassandra a MongoDB, které replikujte data napříč několika virtuálních počítačů k přetrvávání v případě selhání jednoho virtuálního počítače. Řada Ls-series nepodporuje vytváření z místní mezipaměti a zvyšuje dosažitelné vstupně-výstupních operací trvalých datových disků.

## <a name="ls-series"></a>Řada Ls

ACU: 180–240

Storage úrovně Premium: podporováno

Premium Storage ukládání do mezipaměti: Není podporováno
 
| Velikost          | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / MB/s | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4    | 32   | 678   | 16    | 20,000 / 200   | 5 000 / 125        | 2 / 4,000  | 
| Standard_L8s   | 8    | 64   | 1 388 | 32   | 40,000 / 400   | 10 000 / 250       | 4 / 8,000  | 
| Standard_L16s  | 16   | 128  | 2 807 | 64   | 80,000 / 800   | 20 000 / 500       | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40 000 / 1 000     | 8 / 20,000 | 
 

Maximální propustnost disku s virtuální počítače řady Ls-series může být omezená podle počtu, velikostí a prokládáním připojených disků. Podrobnosti viz článek [Premium Storage: Vysoce výkonné úložiště pro virtuální počítače Azure](../articles/virtual-machines/windows/premium-storage.md).

<sup>1</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka.

## <a name="size-table-definitions"></a>Definice tabulky velikostí

- Kapacita úložiště je v jednotkách GiB, tj. 1024^3 bajtů. Při porovnávání diskové kapacity měřené v GB (1000^3 B) s kapacitou měřenou v GiB (1024^3) pamatujte, že údaj v GiB je číselně menší. Například 1023 GiB = 1098,4 GB
- Propustnost disku se měří v počtu V/V operací za sekundu (IOPS) a v MB/s, kde 1 MB/s = 10^6 bajtů/s.
- Pokud chcete získat nejlepší výkon pro vaše virtuální počítače, byste měli omezit počet datových disků na 2 disky na virtuální procesor.
- **Byl očekáván šířky pásma sítě** je maximální agregovaná [šířka pásma přidělená podle typu virtuálního počítače](../articles/virtual-network/virtual-machine-network-throughput.md) pro všechny síťové karty pro všechny cíle. Horní omezení nejsou garantována, ale slouží jako vodítko pro výběr správného typu virtuálního počítače pro příslušnou aplikaci. Skutečný výkon sítě bude záviset na řadě faktorů, včetně zahlcení sítě, zatížení aplikací a nastavení sítě. Informace o optimalizaci propustnosti sítě najdete v tématu [Optimalizace propustnosti sítě pro Windows a Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Pro dosažení očekávaného výkonu sítě na Linuxu nebo ve Windows může být nutné vybrat konkrétní verzi nebo optimalizovat virtuální počítač. Další informace najdete v tématu [Spolehlivé testování propustnosti virtuálního počítače](../articles/virtual-network/virtual-network-bandwidth-testing.md).
