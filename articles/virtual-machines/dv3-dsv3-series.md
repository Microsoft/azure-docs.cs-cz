---
title: Řada Dv3 a DSv3
description: Specifikace pro virtuální počítače s Dv3 a Dsv3-Series.
author: joelpelley
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: jushiman
ms.openlocfilehash: 22400164ac82df3ec0e0e24daf6d823eae219837
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566426"
---
# <a name="dv3-and-dsv3-series"></a>Řada Dv3 a DSv3

Dv3-Series běží na Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) v konfiguraci s více vlákny, které poskytují lepší umístění pro většinu úloh pro obecné účely. Paměť se rozšířila (od ~ 3,5 GiB/vCPU na 4 GiB/vCPU), zatímco limity disku a sítě se přizpůsobily na základě počtu jader, aby se přerovnaly k přechodu na vlákno. Řady Dv3-Series již nemají virtuální počítače s vysokou velikostí paměti řady D/Dv2-Series byly přesunuty do paměti optimalizované pro [Ev3 a Esv3-Series](ev3-esv3-series.md).

Příklady případů použití řady D-Series zahrnují aplikace na podnikové úrovni, relační databáze, ukládání do mezipaměti v paměti a analýzy.

## <a name="dv3-series"></a>Dv3-series

Velikosti řady Dv3-Series běží na Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) s [ &reg; technologií Intel Turbo zvyšovat 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html). Velikosti Dv3-series nabízejí kombinaci virtuálního procesoru, paměti a dočasného úložiště pro většinu produkčních úloh.

Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti Dsv3. Měřiče cen a účtování pro velikosti Dsv3 jsou stejné jako pro Dv3-series.

Virtuální počítače Dv3-Series jsou součástí technologie Intel® Hyper-Threading.

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): nepodporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): nepodporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): podporované (*vyžaduje minimálně 4 vCPU*)<br>
[Dočasné disky s operačním systémem](ephemeral-os-disks.md): nepodporované <br>
<br>

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

Velikosti řady Dsv3-Series běží na Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) s [ &reg; technologií Intel Turbo pro zvýšení úrovně 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) a používat Premium Storage. Velikosti Dsv3-series nabízejí kombinaci virtuálního procesoru, paměti a dočasného úložiště pro většinu produkčních úloh.

Virtuální počítače Dsv3-Series jsou součástí technologie Intel® Hyper-Threading.

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): podporované (*vyžaduje minimálně 4 vCPU*)<br>
[Dočasné disky s operačním systémem](ephemeral-os-disks.md): podporované <br>
<br>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost shluku a dočasného úložiště v mezipaměti: IOPS/MB/s<sup>1</sup> | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální propustnost disku s mezipamětí pro shluk: IOPS/MB/s<sup>1</sup> | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 4000/100    |3200/48    | 4000/100   | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 8000/200    |6400/96    | 8000/200   | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 16000/400   |12800/192  | 16000/400  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 32000/800   |25600/384  | 32000/800  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 64000/1600  |51200/768  | 64000/1600 | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 96000/2000  |76800/1152 | 80000/2000 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 128000/2000 |80000/1200 | 80000/2000 | 8/30000 |

<sup>1</sup>  virtuální počítače řady Dsv3- [Series můžou zvýšit](./disk-bursting.md) výkon svých disků a získat až 30 minut až po dobu až 30 minut.

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