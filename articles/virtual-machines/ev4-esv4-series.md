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
ms.openlocfilehash: 94c47369d7b638640fab1971801177103779e896
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648934"
---
# <a name="ev4-and-esv4-series"></a>Řada Ev4 a Esv4

Ev4 a Esv4-Series běží na &reg; procesorech Intel Xeon &reg; Platinum 8272CL (Cascade Lake) v konfiguraci s více vlákny, jsou ideální pro různé podnikové aplikace náročné na paměť a funkce až na 504GiB paměti RAM.

> [!NOTE]
> Nejčastější dotazy najdete v tématu  [velikosti virtuálních počítačů Azure bez místního dočasného disku](azure-vms-no-temp-disk.md).

## <a name="ev4-series"></a>Ev4-Series

Velikosti řady Ev4-Series běží na Intel Xeon &reg; Platinum 8272CL (Cascade Lake). Instance Ev4-Series jsou ideální pro podnikové aplikace náročné na paměť. Virtuální počítače Ev4-Series mají &reg; technologii Intel Hyper-Threading.

Úložiště vzdálených datových disků se fakturuje odděleně od virtuálních počítačů. Pokud chcete použít disky Premium Storage, použijte velikosti Esv4. Měřiče cen a účtování pro velikosti Esv4 jsou stejné jako pro Ev4-Series.

> [!IMPORTANT]
> Tyto nové velikosti jsou momentálně pod Public Preview. K těmto Ev4 a Esv4-Series se můžete zaregistrovat [tady](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 

ACU: 195-210

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Migrace za provozu: podporováno

Aktualizace pro zachování paměti: podporováno

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

Velikosti řady Esv4-Series běží na Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). Instance Esv4-Series jsou ideální pro podnikové aplikace náročné na paměť. Virtuální počítače Evs4-Series mají &reg; technologii Intel Hyper-Threading. Úložiště vzdálených datových disků se fakturuje odděleně od virtuálních počítačů.

> [!IMPORTANT]
> Tyto nové velikosti jsou momentálně pod Public Preview. K těmto Ev4 a Esv4-Series se můžete zaregistrovat [tady](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 

ACU: 195-210

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Migrace za provozu: podporováno

Aktualizace pro zachování paměti: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost v mezipaměti: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | Jenom vzdálené úložiště | 4 | 19000/120 (50) | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | Jenom vzdálené úložiště | 8 | 38500/242 (100) | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | Jenom vzdálené úložiště | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | Jenom vzdálené úložiště | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | Jenom vzdálené úložiště | 32 | 193000/1211 (500) | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | Jenom vzdálené úložiště | 32 | 308000/1936 (800) | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Jenom vzdálené úložiště | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| Jenom vzdálené úložiště | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |

<sup>1</sup> [jsou k dispozici 1 omezené základní velikosti](./windows/constrained-vcpu.md).

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