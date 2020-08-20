---
title: Řady Ev3-Series a Esv3-Series
description: Specifikace pro virtuální počítače s Ev3 a Esv3-Series.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f9ef048fa7cc4b795f409326988e3276743b6992
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648951"
---
# <a name="ev3-and-esv3-series"></a>Řada Ev3 a Esv3

Ev3 a Esv3-Series běží na Intel® Xeon® Platinum 8272CL (Cascade Lake), nebo Intel® Xeon® 8171M 2,1 GHz (Skylake), nebo procesor Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) v konfiguraci s více vlákny, poskytuje lepší hodnotu pro většinu úloh pro obecné účely a přináší Ev3 do sblížení s virtuálními počítači pro obecné účely většiny ostatních cloudů.  Paměť se rozšířila (od 7 GiB/vCPU do 8 GiB/vCPU), zatímco limity disku a sítě se přizpůsobily na základě počtu jader, aby se přerovnaly k přechodu na vlákno. Ev3 je následná velikost virtuálních počítačů s vysokou pamětí pro řady D/Dv2.

## <a name="ev3-series"></a>Ev3-series

Instance Ev3-Series běží na Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) nebo procesory Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) a funkce technologie Intel Turbo pro zvýšení úrovně 2,0. Instance Ev3-series jsou ideální pro podnikové aplikace s vysokými nároky na paměť.

Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti ESv3. Měřiče cen a účtování pro velikosti ESv3 jsou stejné jako pro Ev3-series.

Funkce virtuálního počítače Ev3-Series Intel® technologie Hyper-Threading.

ACU: 160–190

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Migrace za provozu: podporováno

Aktualizace pro zachování paměti: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet síťových karet | Šířka pásma sítě |
|---|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3 000 / 46 / 23     | 2|1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6 000 / 93 / 46     | 2|2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12 000 / 187 / 93   | 4|4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24 000 / 375 / 187  | 8|8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8|10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48 000 / 750 / 375  | 8|16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96 000 / 1 000 / 500 | 8|24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96 000 / 1 000 / 500 | 8|30000 |
| Standard_E64i_v3 <sup>1, 2</sup> | 64 | 432 | 1600 | 32 | 96 000 / 1 000 / 500 | 8|30000 |

jsou k dispozici <sup>1</sup> omezené základní velikosti.

<sup>2</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.

## <a name="esv3-series"></a>Esv3-Series

Instance Esv3-Series běží na procesorech Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) nebo procesor Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell), funkce Intel Turbo pro zvýšení úrovně 2,0 a používat Premium Storage. Instance Esv3-Series jsou ideální pro podnikové aplikace náročné na paměť.

Funkce virtuálního počítače Esv3-Series Intel® technologie Hyper-Threading.

ACU: 160–190

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Migrace za provozu: podporováno

Aktualizace pro zachování paměti: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2|2000 |
| Standard_E8s_v3 <sup>1</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4|4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8|8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8|24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8|30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8|30000 |

jsou k dispozici <sup>1</sup> omezené základní velikosti.

<sup>2</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.

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