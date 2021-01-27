---
title: Ev4 a Esv4-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače s Ev4 a Esv4-Series.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 9918c978ecb98b7586272da714bdc8d25920d114
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920320"
---
# <a name="ev4-and-esv4-series"></a>Řada Ev4 a Esv4

Ev4 a Esv4-Series běží na &reg; procesorech Intel Xeon &reg; Platinum 8272CL (Cascade Lake) v konfiguraci s více vlákny, jsou ideální pro různé podnikové aplikace náročné na paměť a funkce až na 504GiB paměti RAM. Nabízí veškerou základní rychlost Turbo 3,4 GHz.

> [!NOTE]
> Nejčastější dotazy najdete v tématu  [velikosti virtuálních počítačů Azure bez místního dočasného disku](azure-vms-no-temp-disk.md).

## <a name="ev4-series"></a>Ev4-Series

Velikosti řady Ev4-Series běží na Intel Xeon &reg; Platinum 8272CL (Cascade Lake). Instance Ev4-Series jsou ideální pro podnikové aplikace náročné na paměť. Virtuální počítače Ev4-Series funkce &reg; technologie Intel Hyper-Threading.

Úložiště vzdálených datových disků se fakturuje odděleně od virtuálních počítačů. Pokud chcete použít disky Premium Storage, použijte velikosti Esv4. Měřiče cen a účtování pro velikosti Esv4 jsou stejné jako pro Ev4-Series.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): nepodporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): nepodporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): podporované<br>
<br>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | Jenom vzdálené úložiště | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | Jenom vzdálené úložiště | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | Jenom vzdálené úložiště | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | Jenom vzdálené úložiště | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | Jenom vzdálené úložiště | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | Jenom vzdálené úložiště | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | Jenom vzdálené úložiště | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | Jenom vzdálené úložiště | 32| 8|30000 |


## <a name="esv4-series"></a>Esv4-Series

Velikosti řady Esv4-Series běží na Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). Instance Esv4-Series jsou ideální pro podnikové aplikace náročné na paměť. Virtuální počítače Evs4-Series funkce &reg; technologie Intel Hyper-Threading. Úložiště vzdálených datových disků se fakturuje odděleně od virtuálních počítačů.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): podporované<br>
<br>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | Jenom vzdálené úložiště | 4 | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | Jenom vzdálené úložiště | 8 | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | Jenom vzdálené úložiště | 16 | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | Jenom vzdálené úložiště | 32 | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | Jenom vzdálené úložiště | 32 | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | Jenom vzdálené úložiště | 32 | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Jenom vzdálené úložiště | 32 | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| Jenom vzdálené úložiště | 32 | 80000/1200 | 8|30000 |
| Standard_E80is_v4 <sup>2</sup> | 80 | 504 | Jenom vzdálené úložiště | 32 | 80000/1200 | 8|30000 |

počet [dostupných jader s omezením](./constrained-vcpu.md): <sup>1</sup> .

<sup>2</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.

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
