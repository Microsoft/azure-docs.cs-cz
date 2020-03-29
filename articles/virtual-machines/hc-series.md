---
title: Řada HC – virtuální počítače Azure
description: Specifikace pro virtuální počítače řady HC.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cc25fb9b21d535ef07bcfae673be48216427b370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164776"
---
# <a name="hc-series"></a>Řada HC

Virtuální počítače řady HC jsou optimalizované pro aplikace řízené hustými výpočty, jako je implicitní analýza konečných prvků, molekulární dynamika a výpočetní chemie. Virtuální počítače HC jsou vybaveny procesorovými jádry 44 Intel Xeon Platinum 8168, 8 GB paměti RAM na jádro procesoru a bez hyperthreadingu. Platforma Intel Xeon Platinum podporuje bohatý ekosystém softwarových nástrojů společnosti Intel, jako je knihovna jádra Intel Math.

ACU: 297-315

Úložiště Premium: Podporováno

Ukládání úložiště Premium: Podporováno

Migrace za provozu: Není podporována

Aktualizace pro zachování paměti: Není podporováno

| Velikost | Virtuální procesory | Procesor | Paměť (GB) | Šířka pásma paměti GB/s | Základní frekvence procesoru (GHz) | Frekvence všech jader (GHz, špička) | Jednojádrová frekvence (GHz, špička) | Výkon RDMA (Gb/s) | Podpora MPI | Dočasné úložiště (GB) | Max. datových disků | Maximální síťové karty Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Všechny | 700 | 4 | 1 |

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