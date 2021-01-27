---
title: Řady Ev3-Series a Esv3-Series
description: Specifikace pro virtuální počítače s Ev3 a Esv3-Series.
author: DavidCBerry13
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: daberry
ms.openlocfilehash: 1a0943c9f87960f9fcec074941b6e8ead81d92af
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917905"
---
# <a name="ev3-and-esv3-series"></a>Řada Ev3 a Esv3

Ev3 a Esv3-Series běží na Intel® Xeon® Platinum 8272CL (Cascade Lake), nebo Intel® Xeon® 8171M 2,1 GHz (Skylake), nebo procesor Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) v konfiguraci s více vlákny, poskytuje lepší hodnotu pro většinu úloh pro obecné účely a přináší Ev3 do sblížení s virtuálními počítači pro obecné účely většiny ostatních cloudů.  Paměť se rozšířila (od 7 GiB/vCPU do 8 GiB/vCPU), zatímco limity disku a sítě se přizpůsobily na základě počtu jader, aby se přerovnaly k přechodu na vlákno. Ev3 je následná velikost virtuálních počítačů s vysokou pamětí pro řady D/Dv2.

## <a name="ev3-series"></a>Ev3-series

Instance Ev3-Series běží na Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) nebo procesory Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) a funkce technologie Intel Turbo pro zvýšení úrovně 2,0. Instance Ev3-series jsou ideální pro podnikové aplikace s vysokými nároky na paměť.

Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti ESv3. Měřiče cen a účtování pro velikosti ESv3 jsou stejné jako pro Ev3-series.

Funkce virtuálního počítače Ev3-Series Hyper-Threading technologie Intel®.

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): nepodporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): nepodporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): podporované<br>
<br>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Max. počet NIC / Šířka pásma sítě |
|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3 000 / 46 / 23     | 2/1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6 000 / 93 / 46     | 2/2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12 000 / 187 / 93   | 4/4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24 000 / 375 / 187  | 8/8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8/10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48 000 / 750 / 375  | 8/16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96 000 / 1 000 / 500 | 8/24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96 000 / 1 000 / 500 | 8/30000 |
| Standard_E64i_v3 <sup>1, 2</sup> | 64 | 432 | 1600 | 32 | 96 000 / 1 000 / 500 | 8/30000 |

jsou k dispozici <sup>1</sup> omezené základní velikosti.

<sup>2</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.

## <a name="esv3-series"></a>Esv3-Series

Instance Esv3-Series běží na procesorech Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) nebo procesor Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell), funkce Intel Turbo pro zvýšení úrovně 2,0 a používat Premium Storage. Instance Esv3-Series jsou ideální pro podnikové aplikace náročné na paměť.

Funkce virtuálního počítače Esv3-Series Hyper-Threading technologie Intel®.

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): podporované<br>
<br>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s (velikost mezipaměti v GiB) | Propustnost shluku a dočasného úložiště v mezipaměti: IOPS/MB/s<sup>3</sup> | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s |  Propustnost neuloženého disku s nárůstem zatížení: IOPS/MB/s<sup>3</sup>| Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v3                | 2  | 16  | 32  | 4  | 4000/32 (50)       | 4000/100    | 3200/48    | 4000/100 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>   | 4  | 32  | 64  | 8  | 8000/64 (100)      | 8000/200    | 6400/96    | 8000/200 | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>   | 8  | 64  | 128 | 16 | 16000/128 (200)    | 16000/400   | 12800/192  | 16000/400 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup>  | 16 | 128 | 256 | 32 | 32000/256 (400)    | 32000/800   | 25600/384  | 32000/800 | 8/8000 |
| Standard_E20s_v3               | 20 | 160 | 320 | 32 | 40000/320 (400)    | 40000/1000  | 32000/480  | 40000/1000 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 64000/1600  | 51200/768  | 64000/1600 | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 96000/2000  | 76800/1152 | 80000/2000 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 128000/2000 | 80000/1200 | 80000/2000 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 128000/2000 | 80000/1200 | 80000/2000 | 8/30000 |

jsou k dispozici <sup>1</sup> omezené základní velikosti.

<sup>2</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.

<sup>3</sup> virtuální počítače řady Esv3- [Series můžou zvýšit](./disk-bursting.md) výkon svých disků a získat až 30 minut až po dobu až 30 minut.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Další velikosti a informace

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU.](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)
- [Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
- Další informace o typech disků najdete v tématu [Jaké typy disků jsou dostupné v Azure?](disks-types.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.