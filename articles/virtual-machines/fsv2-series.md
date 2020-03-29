---
title: Fsv2-series – virtuální počítače Azure
description: Specifikace pro virtuální chod řady Fsv2.
services: virtual-machines
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f28c6b61aee3c8cbc078db1c2cfb48ed1fba4554
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164844"
---
# <a name="fsv2-series"></a>Řada Fsv2

Řada Fsv2 je založena na procesoru Intel® Xeon® Platinum 8168. Je vybaven trvalým jádrem Turbo taktovací rychlost 3,4 GHz a maximální jednojádrovou turbo frekvencí 3,7 GHz. Intel® AVX-512 pokyny jsou nové na Intel scalable procesory. Tyto pokyny poskytují až 2x zvýšení výkonu pro úlohy vektorového zpracování na operacích s jednou i dvojitou přesností s plovoucí desetinnou desetinnou desetinnou desetinnou desetinnou desetinnou desetinnou desetinnou desetinnou desetinnou desetinnou desetinnou stou. Jinými slovy, jsou opravdu rychlé pro všechny výpočetní úlohy.

Virtuální aplikace řady Fsv2 jsou vybaveny technologií Intel® Technologie hypervláken.

ACU: 195 - 210

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

Migrace za provozu: Podporováno

Aktualizace pro zachování paměti: Podporováno

| Velikost | virtuálního procesoru | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a protemplovaná propustnost úložiště: IOPS/MBps (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS/MBps | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2/875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2/1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4/3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4/7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8/14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8/21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8/28000 |
| Standard_F72s_v2<sup>1, 2.</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8/30000 |

<sup>1</sup> Použití více než 64 virtuálních procesorů vyžaduje jeden z těchto podporovaných hostovacích operačních systémů:

- Windows Server 2016 nebo novější
- Ubuntu 16.04 LTS nebo novější, s jádrem vyladěným v Azure (jádro 4.15 nebo novější)
- SLES 12 SP2 nebo novější
- RHEL nebo CentOS verze 6.7 až 6.10, s nainstalovaným balíčkem LIS dodaným společností Microsoft 4.3.1 (nebo novějším)
- RHEL nebo CentOS verze 7.3 s nainstalovaným balíčkem LIS dodaným společností Microsoft 4.2.1 (nebo novějším)
- RHEL nebo CentOS verze 7.6 nebo novější
- Oracle Linux s UEK4 nebo novějším
- Debian 9 s jádrem backports, Debian 10 nebo novější
- CoreOS s jádrem 4.14 nebo novějším

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
