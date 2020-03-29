---
title: Dv2 a Dsv2-series – virtuální počítače Azure
description: Specifikace pro virtuální chody řady Dv2 a Dsv2.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 808b14f118e842cb9e52d110075f92ba25a343c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164419"
---
# <a name="dv2-and-dsv2-series"></a>Řada Dv2 a DSv2

Dv2 a Dsv2-series, pokračování původní řady D, jsou vybaveny výkonnějším procesorem a optimální konfigurací cpu-to-memory, což je vhodná pro většinu produkčních úloh. Řada Dv2 je o 35 % rychlejší než řada D. Dv2-série běží na Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), nebo Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) procesory s Intel Turbo Boost Technology 2.0. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

## <a name="dv2-series"></a>Dv2-series

Velikosti řady Dv2 běží na procesorech Intel® Xeon® 8171M 2.1GHz (Skylake) nebo Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) nebo Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) s technologií Intel Turbo Boost 2.0.

ACU: 210–250

Úložiště Premium: Není podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

Migrace za provozu: Podporováno

Aktualizace pro zachování paměti: Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/Read MBps/Write MB/B/b/s | Max. datových disků | Propustnost: VOPS | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3,5 | 50  | 3 000 / 46 / 23    | 4  | 4×500  | 2/750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6 000 / 93 / 46    | 8  | 8×500  | 2/1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12 000 / 187 / 93  | 16 | 16×500 | 4/3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24 000 / 375 / 187 | 32 | 32×500 | 8/6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48 000 / 750 / 375 | 64 | 64x500 | 8/12000 |

## <a name="dsv2-series"></a>DSv2-series

Velikosti řady DSv2 běží na procesorech Intel® Xeon® 8171M 2.1GHz (Skylake) nebo Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) nebo Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) s technologií Intel Turbo Boost 2.0 a používají prémiové úložiště.

ACU: 210–250

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

Migrace za provozu: Podporováno

Aktualizace pro zachování paměti: Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a protemplovaná propustnost úložiště: IOPS/MBps (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS/MBps | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3,5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2/750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2/1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4/3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8/6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8/12000 |

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
