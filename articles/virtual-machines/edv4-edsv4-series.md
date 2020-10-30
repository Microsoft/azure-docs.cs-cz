---
title: Řada Edv4 a Edsv4
description: Specifikace pro virtuální počítače s Ev4, Edv4, Esv4 a Edsv4-Series.
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 5d02085fe4850dacf6e876b42cf301df2ea76713
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043335"
---
# <a name="edv4-and-edsv4-series"></a>Řada Edv4 a Edsv4

Edv4 a Edsv4-Series běží na &reg; procesorech Intel Xeon &reg; Platinum 8272CL (Cascade Lake) v konfiguraci s vlákny typu Hyper a jsou ideální pro různé podnikové aplikace náročné na paměť a funkce až 504 GIB paměti RAM, [Intel Turbo Technology &reg; 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [ &reg; technologie Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) a [Intel &reg; Advanced Vector Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Podporují taky [ &reg; zvýšení úrovně Intel s hloubkovým učením](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Tyto nové velikosti virtuálních počítačů budou mít 50% větší místní úložiště a také lepší počet vstupně-výstupních operací pro čtení i zápis ve srovnání s [Ev3/Esv3mi](./ev3-esv3-series.md) [virtuálními počítači Gen2](./generation-2.md). Nabízí veškerou základní rychlost Turbo 3,4 GHz. 

## <a name="edv4-series"></a>Edv4-Series

Velikosti řady Edv4-Series běží na &reg; &reg; procesorech Intel Xeon Platinum 8272CL (Cascade Lake). Velikost virtuálních počítačů s Edv4 je součástí až 504 GiB paměti RAM, navíc k rychlému a velkému místnímu úložišti SSD (až 2 400 GiB). Tyto virtuální počítače jsou ideální pro podnikové aplikace náročné na paměť a aplikace, které využívají nízkou latenci a vysoce rychlé místní úložiště. K virtuálním počítačům s Edv4 můžete připojit standardní SSD a standardní disková úložiště HDD. 

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): nepodporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): nepodporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
<br>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | <sup>**</sup> Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8|10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8|30000 |


<sup>**</sup>Tyto hodnoty IOPs se dají zaručit pomocí [virtuálních počítačů s Gen2](generation-2.md) .

## <a name="edsv4-series"></a>Edsv4-Series

Velikosti řady Edsv4-Series běží na &reg; &reg; procesorech Intel Xeon Platinum 8272CL (Cascade Lake). Velikost virtuálních počítačů s Edsv4 je součástí až 504 GiB paměti RAM, navíc k rychlému a velkému místnímu úložišti SSD (až 2 400 GiB). Tyto virtuální počítače jsou ideální pro podnikové aplikace náročné na paměť a aplikace, které využívají nízkou latenci a vysoce rychlé místní úložiště.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
<br>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | <sup>**</sup> Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120 (50) | 3200/48 | 2|1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242 (100) | 6400/96 | 2|2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211 (500)  | 32000/480  | 8|10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936 (800) | 51200/768  | 8|16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |

počet [dostupných jader s omezením](./constrained-vcpu.md): <sup>1</sup> .


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
