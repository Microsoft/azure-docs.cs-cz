---
title: DC-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače DC-Series.
author: susaxen
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: 677f4df0873f8b72d40dd373035111e2e0002491
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549226"
---
# <a name="dcsv2-series"></a>DCsv2-Series


DCsv2-Series může přispět k ochraně důvěrnosti a integrity vašich dat a kódu při jejich zpracování ve veřejném cloudu. Tyto počítače jsou založené na nejnovější generaci procesorů Intel XEON E-2288G s využitím technologie SGX. Technologie Intel Turbo zvyšuje tyto počítače až do verze 5.0 GHz. Instance řad DCsv2 umožňují zákazníkům vytvářet zabezpečené aplikace založené na enklávy pro ochranu kódu a dat, když se používají.

Příklady případů použití: důvěrné sdílení dat s více částmi, odhalování podvodů, praní proti podvodným materiálům, blockchainí, analýzy důvěrných údajů, analýza informací a důvěrný strojové učení.

[Premium Storage](premium-storage-performance.md): podporované *<br> 
 [Premium Storage ukládání do mezipaměti](premium-storage-performance.md): podporované <br> 
 [migrace za provozu](maintenance-and-updates.md): Nepodporovaná <br> 
 [paměť pro zachovávání aktualizací](maintenance-and-updates.md): Nepodporovaná <br> 
 [Podpora generování virtuálních počítačů](generation-2.md): <br> 
 podporovaná [síť](../virtual-network/create-vm-accelerated-networking-cli.md)2. generace: podporováno (* vyžaduje minimálně 4 vCPU *) <br>
[Dočasné disky s operačním systémem](ephemeral-os-disks.md): podporované <br>

* S výjimkou Standard_DC8_v2 <br>

| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) | Paměť EPC (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |

- Virtuální počítače řady DCsv2-Series jsou [virtuální počítače generace 2](./generation-2.md#creating-a-generation-2-vm) a podporují jenom `Gen2` image.
- Aktuálně k dispozici v oblastech, které jsou [zde](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)uvedeny.
- Předchozí generace důvěrných výpočetních virtuálních počítačů: [DC Series](sizes-previous-gen.md#preview-dc-series)
- Vytvoření virtuálních počítačů s DCsv2 pomocí [Azure Portal](./linux/quick-create-portal.md) nebo [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes-and-information"></a>Další velikosti a informace

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU.](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

Cenová kalkulačka: [Cenová Kalkulačka](https://azure.microsoft.com/pricing/calculator/)

Další informace o typech disků: [typy disků](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
