---
title: Dv3 a Dsv3-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače s Dv3 a Dsv3-Series.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ea5811fa20ea4866655de74d79ff3905ba03f16
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164402"
---
# <a name="dv3-and-dsv3-series"></a>Řada Dv3 a DSv3

Dv3-Series běží na procesorech Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) v konfiguraci s více vlákny, čímž je zajištěno lepší umístění nejdůležitějších hodnot. zatížení pro účely. Paměť se rozšířila (od ~ 3,5 GiB/vCPU na 4 GiB/vCPU), zatímco limity disku a sítě se přizpůsobily na základě počtu jader, aby se přerovnaly k přechodu na vlákno. Řady Dv3-Series již nemají virtuální počítače s vysokou velikostí paměti řady D/Dv2-Series byly přesunuty do paměti optimalizované pro [Ev3 a Esv3-Series](ev3-esv3-series.md).

Příklady případů použití řady D-Series zahrnují aplikace na podnikové úrovni, relační databáze, ukládání do mezipaměti v paměti a analýzy.

## <a name="dv3-series"></a>Dv3-series

Velikosti řady Dv3-Series běží na procesorech Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell), nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) s technologií Intel Turbo zvyšovat 2,0. Velikosti Dv3-series nabízejí kombinaci virtuálního procesoru, paměti a dočasného úložiště pro většinu produkčních úloh.

Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti Dsv3. Měřiče cen a účtování pro velikosti Dsv3 jsou stejné jako pro Dv3-series.

Virtuální počítače Dv3-Series jsou součástí technologie Intel® technologie Hyper-Threading.

ACU: 160–190

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Migrace za provozu: podporováno

Aktualizace pro zachování paměti: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Maximální počet síťových karet/šířka pásma sítě |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3 000 / 46 / 23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6 000 / 93 / 46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12 000 / 187 / 93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24 000 / 375 / 187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48 000 / 750 / 375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96 000 / 1 000 / 500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96 000 / 1 000 / 500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3-series

Velikosti řady Dsv3-Series běží na procesorech Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) s technologií Intel Turbo 2,0 a používat Premium Storage. Velikosti Dsv3-series nabízejí kombinaci virtuálního procesoru, paměti a dočasného úložiště pro většinu produkčních úloh.

Virtuální počítače Dsv3-Series jsou součástí technologie Intel® technologie Hyper-Threading.

ACU: 160–190

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Migrace za provozu: podporováno

Aktualizace pro zachování paměti: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 3200/48    | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 6400/96    | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 12800/192  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 25600/384  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
