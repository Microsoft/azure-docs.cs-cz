---
title: Virtuální počítače řady Dv2 a DSv2 optimalizované pro paměť – virtuální počítače Azure
description: Specifikace pro virtuální chody řady Dv2 a DSv2.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 66e415070e60676df5602078aff50c7b68920f1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914037"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>Paměť optimalizovaná řada Dv2 a Dsv2

Dv2 a Dsv2-series, pokračování původní řady D, nabízí výkonnější procesor. Velikosti řady DSv2 běží na procesorech Intel® Xeon® 8171M 2.1 GHz (Skylake) nebo Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) nebo intel® Xeon® E5-2673 v3 2.4 GHz (Haswell). Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

## <a name="dv2-series-11-15"></a>Řada Dv2 11-15

Dv2-série velikosti běží na Intel® Xeon® 8171M 2.1 GHz (Skylake) nebo Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) nebo Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) procesory.

ACU: 210–250

Úložiště Premium: Není podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/Read MBps/Write MB/B/b/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6 000 / 93 / 46    | 8/8x500   | 2/1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12 000 / 187 / 93  | 16/16x500 | 4/3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24 000 / 375 / 187 | 32/32x500 | 8/6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48 000 / 750 / 375 | 64/64x500 | 8/12000 |
| Standard_D15_v2 <sup>1.</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8/25000 <sup>2</sup> |

<sup>1</sup> Instance je izolována na hardware vyhrazený pro jednoho zákazníka.
<sup>2</sup> 25000 Mb/s se zrychlenou sítí.

## <a name="dsv2-series-11-15"></a>Řada DSv2 11-15

Velikosti řady DSv2 běží na procesorech Intel® Xeon® 8171M 2.1 GHz (Skylake) nebo Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) nebo intel® Xeon® E5-2673 v3 2.4 GHz (Haswell).

ACU: 210 - 250 <sup>1</sup>

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a protemplovaná propustnost úložiště: IOPS/MBps (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS/MBps | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| <sup>Standard_DS11_v2 3</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2/1500  |
| <sup>3.</sup> Standard_DS12_v2 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4/3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8/6000  |
| <sup>Standard_DS14_v2 3</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8/12000 |
| <sup>2. Standard_DS15_v2</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8/25000 <sup>4</sup> |

<sup>1</sup> Maximální propustnost disku (IOPS nebo MBps) možná s virtuálním počítačem řady DSv2 může být omezena počtem, velikostí a prokládáním připojených disků.  Podrobnosti naleznete v [tématu Návrh vysokého výkonu](./windows/premium-storage-performance.md).
<sup>2</sup> Instance je izolována na hardware založené na Intel Haswell a je vyhrazena pro jednoho zákazníka.  
<sup>3</sup> K dispozici jsou omezené velikosti jader.  
<sup>4</sup> 25000 Mb/s se zrychlenou sítí.

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
