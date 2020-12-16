---
title: NC-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady NC-Series.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 97b5d6bc6f19e07ea7c980b98c1e7986c59dc546
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585407"
---
# <a name="nc-series"></a>Řada NC

Virtuální počítače řady NC-Series jsou napájené kartou [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) a procesorem Intel Xeon E5-2690 V3 (Haswell). Uživatelé můžou zpracovávejte data rychleji tím, že využívají CUDA pro aplikace pro průzkumy energie, simulace havárií, trasování paprsků, hloubkové učení a další. Konfigurace NC24r poskytuje síťové rozhraní s nízkou latencí a vysokou propustností optimalizované pro vysoce provázané úlohy paralelního zpracování.

[Premium Storage](premium-storage-performance.md): nepodporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): nepodporováno<br>
[Migrace za provozu](maintenance-and-updates.md): nepodporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): nepodporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1<br>
NVIDIA NVLink Interconnect: nepodporováno<br>
<br>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = polovina karty K80.

*Podpora RDMA

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Aby bylo možné využít možnosti GPU pro virtuální počítače řady Azure N-Series, je nutné nainstalovat ovladače GPU NVIDIA.

[Rozšíření ovladače NVIDIA GPU](./extensions/hpccompute-gpu-windows.md) nainstaluje vhodné ovladače NVIDIA CUDA nebo Grid na virtuální počítač řady N-Series. Nainstalujte nebo spravujte rozšíření pomocí Azure Portal nebo nástrojů, jako jsou šablony Azure PowerShell nebo Azure Resource Manager. Podporované operační systémy a kroky nasazení najdete v [dokumentaci k rozšíření ovladače GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) . Obecné informace o rozšířeních virtuálních počítačů najdete v tématu [rozšíření a funkce virtuálních počítačů Azure](./extensions/overview.md).

Pokud se rozhodnete nainstalovat ovladače GPU NVIDIA ručně, přečtěte si téma Instalace ovladače GPU [řady n-Series pro Windows](./windows/n-series-driver-setup.md) nebo [n-Series Setup Driver Setup pro Linux](./linux/n-series-driver-setup.md) pro podporované operační systémy, ovladače, instalace a postupy ověření.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
