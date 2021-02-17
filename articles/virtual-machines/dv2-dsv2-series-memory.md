---
title: Paměťově optimalizované virtuální počítače s Dv2 a DSv2-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače s Dv2 a DSv2-Series.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 8450a4bf172abcc57f93ccd20bc84642946e7ba9
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555064"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>Paměťově optimalizovaná Dv2 a Dsv2-Series

Dv2 a Dsv2-Series, následné až do původní řady D-Series, nabízí výkonnější procesor. Velikosti řady DSv2-Series běží na Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) nebo Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell). Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

## <a name="dv2-series-11-15"></a>Dv2-Series 11-15

Velikosti řady Dv2-Series běží na Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) nebo Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell).

[ACU](acu.md): 210-250<br>
[Premium Storage](premium-storage-performance.md): nepodporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): nepodporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): podporované (*vyžaduje minimálně 4 vCPU*)<br>
[Dočasné disky s operačním systémem](ephemeral-os-disks.md): nepodporované <br>
<br> 

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6 000 / 93 / 46    | 8/8x500   | 2|1 500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12 000 / 187 / 93  | 16/16x500 | 4|3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24 000 / 375 / 187 | 32/32x500 | 8|6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48 000 / 750 / 375 | 64/64x500 | 8|12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8|25000 <sup>2</sup> |

<sup>1</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.
<sup>2</sup> 25000 MB/s s akcelerovanými síťovými službami.

## <a name="dsv2-series-11-15"></a>DSv2-Series 11-15

Velikosti řady DSv2-Series běží na Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) nebo Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell).

[ACU](acu.md): 210 – 250 <sup>1</sup><br>
[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): podporované (*vyžaduje minimálně 4 vCPU*)<br>
[Dočasné disky s operačním systémem](ephemeral-os-disks.md): podporované <br>
<br> 

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |---|
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2|1 500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4|3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8|6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8|12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8|25000 <sup>4</sup> |

<sup>1</sup> maximální propustnost disku (IOPS nebo MB/s), která je možné použít u virtuálního počítače DSv2 Series, může být omezená počtem, velikostí a prokládáním připojených disků.  Podrobnosti najdete v tématu [navrhování pro vysoký výkon](./premium-storage-performance.md).
<sup>2</sup>  instance je izolovaná na hardwaru založeném na Intel Haswell a vyhrazeném pro jediného zákazníka.  
jsou k dispozici <sup>3</sup> omezené základní velikosti.  
<sup>4</sup> 25000 MB/s s akcelerovanými síťovými službami.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Další velikosti a informace

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU.](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

Cenová kalkulačka: [Cenová Kalkulačka](https://azure.microsoft.com/pricing/calculator/)

Další informace o typech disků: [typy disků](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.