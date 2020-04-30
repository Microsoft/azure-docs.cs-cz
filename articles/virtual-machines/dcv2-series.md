---
title: DC-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače DC-Series.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: d35e37e53b84d317446a93a2301fc3b703b426b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085718"
---
# <a name="dcsv2-series"></a>DCsv2-Series


DCsv2-Series může přispět k ochraně důvěrnosti a integrity vašich dat a kódu při jejich zpracování ve veřejném cloudu. Tyto počítače jsou založené na nejnovější generaci procesorů Intel XEON E-2288G s využitím technologie SGX. Technologie Intel Turbo zvyšuje tyto počítače až do verze 5.0 GHz. Instance řad DCsv2 umožňují zákazníkům vytvářet zabezpečené aplikace založené na enklávy pro ochranu kódu a dat, když se používají.

Příklady případů použití: důvěrné sdílení dat s více částmi, odhalování podvodů, praní proti podvodným materiálům, blockchainí, analýzy důvěrných údajů, analýza informací a důvěrný strojové učení.

Premium Storage: podporované *

Ukládání Premium Storage do mezipaměti: podporováno *

Migrace za provozu: nepodporováno

Aktualizace pro zachování paměti: nepodporováno

* S výjimkou Standard_DC8_v2



| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- Virtuální počítače řady DCsv2-Series jsou [virtuální počítače generace 2](./linux/generation-2.md#creating-a-generation-2-vm) a podporují `Gen2` jenom image.
- Aktuálně k dispozici pouze v Velká Británie – jih, Kanadě Central a USA – východ.
- Předchozí generace důvěrných výpočetních virtuálních počítačů: [DC Series](sizes-previous-gen.md#preview-dc-series)
- Vytvoření virtuálních počítačů s DCsv2 pomocí [Azure Portal](./linux/quick-create-portal.md) nebo [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
