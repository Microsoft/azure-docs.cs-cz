---
title: Řady Eav4-Series a Easv4-Series
description: Specifikace pro virtuální počítače s Eav4 a Easv4-Series.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 9b6ea9491575236a3ef84d660ead8022d13e01ef
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964124"
---
# <a name="eav4-and-easv4-series"></a>Řada Eav4 a Easv4

Řady Eav4-Series a Easv4-Series využívají procesor AMD 2.35 EPYC<sup>TM</sup> 7452 v konfiguraci s více vlákny s až 256mb mezipamětí L3 a zvyšují možnosti spouštění většiny paměťově optimalizovaných úloh. Řady Eav4-Series a Easv4-Series mají stejnou konfiguraci paměti a disku jako Ev3 & Esv3-Series.

## <a name="eav4-series"></a>Eav4-Series

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): nepodporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): nepodporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
<br>

Velikosti řady Eav4-Series jsou založené na procesoru AMD EPYC<sup>TM</sup> 7452 v 2.35 GHz, který může dosáhnout zvýšení maximální frekvence 3.35 GHz. Velikosti řady Eav4-Series jsou ideální pro podnikové aplikace náročné na paměť. Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disk SSD úrovně Premium, použijte velikosti řady Easv4-Series. Měřiče cen a účtování pro velikosti Easv4 jsou stejné jako pro Eav3-Series.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet síťových karet | Očekávaná šířka pásma sítě (MB/s) |
| -----|-----|-----|-----|-----|-----|-----|-----|
| \_E2A \_ v4 úrovně Standard|2|16|50|4|3000 / 46 / 23|2 | 1000 |
| \_E4a \_ v4 úrovně Standard|4|32|100|8|6000 / 93 / 46|2 | 2000 |
| \_E8a \_ v4 úrovně Standard|8|64|200|16|12000 / 187 / 93|4 | 4000 |
| \_E16a \_ v4 úrovně Standard|16|128|400|32|24000 / 375 / 187|8 | 8000 |
| \_E20a \_ v4 úrovně Standard|20|160|500|32|30000/468/234|8 | 10000 |
| \_E32a \_ v4 úrovně Standard|32|256|800|32|48000 / 750 / 375|8 | 16000 |
| \_E48a \_ v4 úrovně Standard|48|384|1200|32|96000/1000 (500)|8 | 24000 |
| \_E64a \_ v4 úrovně Standard|64|512|1600|32|96000/1000 (500)|8 | 30000 |
| \_E96a \_ v4 úrovně Standard|96|672|2400|32|96000/1000 (500)|8 | 30000 |

## <a name="easv4-series"></a>Easv4-Series

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
<br>

Velikosti řady Easv4-Series jsou založené na procesoru AMD EPYC<sup>TM</sup> 7452 v 2.35 GHz, který může dosáhnout zvýšení maximální frekvence 3.35 GHz a používání jednotky SSD úrovně Premium. Velikosti řady Easv4-Series jsou ideální pro podnikové aplikace náročné na paměť.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet | Očekávaná šířka pásma sítě (MB/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 | 1000 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 | 2000 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 | 4000 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 | 8000 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 | 10000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 | 16000 |
| Standard_E48as_v4|48|384|768|32|96000/1020 (1200)|76800/1148|8 | 24000 |
| Standard_E64as_v4|64|512|1024|32|128000/1020 (1600)|80000/1200|8 | 30000 |
| Standard_E96as_v4 <sup>1</sup>|96|672|1344|32|192000/1020 (2400)|80000/1200|8 | 30000 |

<sup></sup> [jsou k dispozici 1 omezené základní velikosti](./constrained-vcpu.md).

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Další velikosti a informace

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

Cenová kalkulačka: [Cenová Kalkulačka](https://azure.microsoft.com/pricing/calculator/)

Další informace o typech disků: [typy disků](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
