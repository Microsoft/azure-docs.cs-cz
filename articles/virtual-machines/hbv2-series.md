---
title: Řada HBv2 – virtuální počítače Azure
description: Specifikace pro virtuální chod y řady HBv2.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: eea649610ca53ccbb98b5ca361555280dcd3dafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164793"
---
# <a name="hbv2-series"></a>Řada HBv2

Virtuální počítače řady HBv2 jsou optimalizované pro aplikace řízené šířkou pásma paměti, jako je dynamika tekutin, analýza konečných prvků a simulace zásobníku. Virtuální počítače HBv2 jsou vybaveny 120 procesorovými jádry AMD EPYC 7742, 4 GB paměti RAM na jádro procesoru a bez souběžného multithreadingu. Každý virtuální počítač HBv2 poskytuje až 340 GB/s šířky pásma paměti a až 4 teraFLOPS fp64 výpočetních prostředků.

Úložiště Premium: Podporováno

Migrace za provozu: Není podporována

Aktualizace pro zachování paměti: Není podporováno

| Velikost | Virtuální procesory | Procesor | Paměť (GB) | Šířka pásma paměti GB/s | Základní frekvence procesoru (GHz) | Frekvence všech jader (GHz, špička) | Jednojádrová frekvence (GHz, špička) | Výkon RDMA (Gb/s) | Podpora MPI | Dočasné úložiště (GB) | Max. datových disků | Maximální síťové karty Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | Všechny | 480 + 960 | 8 | 1 |


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