---
title: HC-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady HC-Series.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cc25fb9b21d535ef07bcfae673be48216427b370
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164776"
---
# <a name="hc-series"></a>Řada HC

Virtuální počítače řady HC-Series jsou optimalizované pro aplikace, které jsou založené na hustém výpočtu, jako je například implicitní nekonečná analýza elementu, molekulová dynamika a výpočetovaná chemie. Virtuální počítače HC – funkce 44 Intel Xeon Platinum 8168, 8 GB paměti RAM na jádro procesoru a žádné podprocesy. Platforma Intel Xeon Platinum podporuje bohatě bohatý ekosystém softwarových nástrojů od společnosti Intel, jako je například knihovna Intel Math kernel.

ACU: 297-315

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Migrace za provozu: nepodporováno

Aktualizace pro zachování paměti: nepodporováno

| Velikost | Virtuální procesory | Procesor | Paměť (GB) | Propustnost paměti GB/s | Základní frekvence procesoru (GHz) | Frekvence všech jader (GHz, špička) | Frekvence s jedním jádrem (GHz, špička) | Výkon RDMA (GB/s) | Podpora MPI | Dočasné úložiště (GB) | Max. datových disků | Maximální počet síťových karet sítě Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Vše | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.