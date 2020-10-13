---
title: Řada Fsv2
description: Specifikace pro virtuální počítače řady Fsv2-Series.
author: brbell
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 039045b613e57ee396201ee93c3962c5970cf02b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651917"
---
# <a name="fsv2-series"></a>Řada Fsv2

Fsv2-Series běží na procesorech Intel® Xeon® Platinum 8272CL (Cascade Lake) a na procesorech Intel® Xeon® Platinum 8168 (Skylake). Nabízí trvalou veškerou rychlost Turbo, 3,4 GHz a maximální frekvenci s jedním jádrem 3,7 GHz. Pokyny pro procesory Intel® AVX-512 jsou novinkou na škálovatelných procesorech Intel. Tyto pokyny poskytují až dvojnásobné zvýšení výkonu pro úlohy vektorového zpracování na operacích s plovoucí desetinnou čárkou a dvojitou přesností. Jinými slovy jsou skutečně rychlé pro všechny výpočetní úlohy.

Virtuální počítače Fsv2-Series jsou součástí technologie Intel® Hyper-Threading.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
<br>

| Velikost | vCPU | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2|875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2|1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4|3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4|7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8|14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8|21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8|28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8|30000 |

<sup>1</sup> použití více než 64 vCPU vyžaduje jeden z těchto podporovaných hostovaných operačních systémů:

- Windows Server 2016 nebo novější
- Ubuntu 16,04 LTS nebo novější s vyladěným jádrem Azure (4,15 jádro nebo novější)
- SLES 12 SP2 nebo novější
- RHEL nebo CentOS verze 6,7 až 6,10 s nainstalovaným balíčkem LIS (nebo novějším) od Microsoftu
- RHEL nebo CentOS verze 7,3 s nainstalovaným balíčkem aplikace LIS verze 4.2.1 (nebo novější)
- RHEL nebo CentOS verze 7,6 nebo novější
- Oracle Linux s UEK4 nebo novějším
- Debian 9 s jádrem pro porty, Debian 10 nebo novější
- CoreOS s jádrem 4,14 nebo novějším

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