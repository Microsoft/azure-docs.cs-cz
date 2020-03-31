---
title: Řady Ev3 a Řady Esv3 – virtuální počítače Azure
description: Specifikace pro virtuální chody řady Ev3 a Esv3.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: aad4610a44c59ed95cf1ad9777329097886c5bed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164368"
---
# <a name="ev3-and-esv3-series"></a>Řada Ev3 a Esv3

Řada Ev3 a Esv3 je vybavena procesorem Intel® Xeon® 8171M 2.1 GHz (Skylake) nebo Procesor Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) v konfiguraci s hypervlákny, poskytuje lepší hodnotu pro většinu obecných pracovních zátěží a přináší Ev3 zarovnání s virtuálními virtuálními ms pro obecné účely většiny ostatních cloudů.  Paměť byla rozšířena (ze 7 GiB/vCPU na 8 GiB/vCPU), zatímco diskové a síťové limity byly upraveny na základě jádra tak, aby odpovídaly přechodu na hyperthreading. Ev3 je sledovat na velikosti virtuálních zařízení s vysokou pamětí rodiny D/Dv2.

## <a name="ev3-series"></a>Ev3-series

Instance řady Ev3 jsou založeny na funkci procesoru Intel® Xeon® 8171M 2.1 GHz (Skylake) nebo Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) procesor a Intel Turbo Boost Technology 2.0. Instance Ev3-series jsou ideální pro podnikové aplikace s vysokými nároky na paměť.

Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti ESv3. Měřiče cen a účtování pro velikosti ESv3 jsou stejné jako pro Ev3-series.

Funkce virtuálního rozhraní řady Ev3 Intel® Technologie hyperthreadingu.

ACU: 160–190

Úložiště Premium: Není podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

Migrace za provozu: Podporováno

Aktualizace pro zachování paměti: Podporováno

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
| Standard_E64i_v3 <sup>1,2</sup> | 64 | 432 | 1600 | 32 | 96 000 / 1 000 / 500 | 8/30000 |

<sup>1</sup> K dispozici jsou omezené velikosti jader.

<sup>2</sup> Instance je izolována na hardware vyhrazený pro jednoho zákazníka.

## <a name="esv3-series"></a>Řada Esv3

Instance řady Esv3 jsou založeny na funkci procesoru Intel® Xeon® 8171M 2.1 GHz (Skylake) nebo Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), Technologii Intel Turbo Boost 2.0 a používají prémiové úložiště. Instance řady Esv3 jsou ideální pro podnikové aplikace náročné na paměť.

Funkce virtuálního rozhraní řady Esv3 Intel® Technologie hyperthreadingu.

ACU: 160–190

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

Migrace za provozu: Podporováno

Aktualizace pro zachování paměti: Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a protemplovaná propustnost úložiště: IOPS/MBps (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS/MBps | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2/2000 |
| Standard_E8s_v3 <sup>1.</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8/8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |
| <sup>2.</sup> Standard_E64is_v3 | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

<sup>1</sup> K dispozici jsou omezené velikosti jader.

<sup>2</sup> Instance je izolována na hardware vyhrazený pro jednoho zákazníka.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.
