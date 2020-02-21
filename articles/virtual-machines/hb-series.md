---
title: V-řadách – Azure Virtual Machines
description: Specifikace pro virtuální počítače s velmi řadou.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 124bdecac900655ed8f6ad0aaaa51fadf342f2c6
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492835"
---
# <a name="hb-series"></a>Řada HB

Virtuální počítače s velmi řadou jsou optimalizované pro aplikace, které jsou založené na šířce pásma paměti, jako je například kapalinová dynamika, explicitní konečná analýza elementu a modelování počasí. Virtuální počítače s funkcí 60 AMD EPYC 7551 procesory, 4 GB paměti RAM na jádro procesoru a žádné souběžné multithreadingy. Virtuální počítač s pamětí typu "a" poskytuje šířku pásma až 260 GB/s.

ACU: 199-216

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

| Velikost | Virtuální procesory | Procesor | Paměť (GB) | Propustnost paměti GB/s | Základní frekvence procesoru (GHz) | Frekvence všech jader (GHz, špička) | Frekvence s jedním jádrem (GHz, špička) | Výkon RDMA (GB/s) | Podpora MPI | Dočasné úložiště (GB) | Max. datových disků | Maximální počet síťových karet sítě Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Všechny | 700 | 4 | 1 |

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