---
title: DC-series – virtuální počítače Azure
description: Specifikace pro virtuální servery řady DC.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: d35e37e53b84d317446a93a2301fc3b703b426b7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085718"
---
# <a name="dcsv2-series"></a>Řada DCsv2


Řada DCsv2 může pomoci chránit důvěrnost a integritu vašich dat a kódu při jejich zpracování ve veřejném cloudu. Tyto stroje jsou podpořeny nejnovější generací procesoru Intel XEON E-2288G s technologií SGX. S technologií Intel Turbo Boost mohou tyto stroje sjet až na 5,0 GHz. Instance řady DCsv2 umožňují zákazníkům vytvářet zabezpečené aplikace založené na enklávě, které chrání jejich kód a data, když jsou používány.

Příklady případů použití zahrnují: důvěrné sdílení dat více stran, odhalování podvodů, boj proti praní špinavých peněz, blockchain, důvěrné analýzy využití, analýzu zpravodajských informací a důvěrné strojové učení.

Úložiště Premium: Podporováno*

Ukládání do mezipaměti úložiště Premium: Podporováno*

Migrace za provozu: Není podporována

Aktualizace pro zachování paměti: Není podporováno

*Kromě Standard_DC8_v2



| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových připojení / Očekávaná šířka pásma sítě (Mb/s) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- Virtuální počítače řady DCsv2 jsou virtuální počítače generace `Gen2` [2](./linux/generation-2.md#creating-a-generation-2-vm) a podporují jenom image.
- V současné době k dispozici pouze v usa jih, Kanada – střed a USA – východ.
- Předchozí generace důvěrných výpočetních virtuálních počítače: [řada DC](sizes-previous-gen.md#preview-dc-series)
- Vytváření virtuálních počítačů DCsv2 pomocí [portálu Azure nebo](./linux/quick-create-portal.md) [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.
