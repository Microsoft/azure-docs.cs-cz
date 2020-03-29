---
title: Řada NC – virtuální počítače Azure
description: Specifikace pro virtuální chody NC-series.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: eab7dfe79aa5cdf234c8bc9472387214f7df3563
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164521"
---
# <a name="nc-series"></a>Řada NC

Virtuální zařízení řady NC jsou poháněna kartou [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) a procesorem Intel Xeon E5-2690 v3 (Haswell). Uživatelé mohou rychleji procházet data využitím CUDA pro aplikace pro průzkum energie, simulace selhání, vykreslování paprskových trasování, hluboké učení a další. Konfigurace NC24r poskytuje síťové rozhraní s nízkou latencí a vysokou propustností optimalizované pro úzce propojené paralelní výpočetní úlohy.

Úložiště Premium: Není podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

Migrace za provozu: Není podporována

Aktualizace pro zachování paměti: Není podporováno

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

Chcete-li využít funkce GPU virtuálních počítačů řady Azure N, musí být nainstalovány ovladače GPU NVIDIA.

[Rozšíření ovladače GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) nainstaluje příslušné ovladače NVIDIA CUDA nebo GRID na virtuální počítač řady N. Nainstalujte nebo spravujte rozšíření pomocí portálu Azure nebo nástrojů, jako jsou šablony Azure PowerShellnebo Azure Resource Manager. Informace o podporovaných operačních systémech a krocích nasazení naleznete v [dokumentaci k rozšíření ovladače GPU NVIDIA.](./extensions/hpccompute-gpu-windows.md) Obecné informace o rozšířeních virtuálních virtuálních zařízení najdete v tématu [Rozšíření a funkce virtuálních strojů Azure](./extensions/overview.md).

Pokud se rozhodnete nainstalovat ovladače GPU NVIDIA ručně, přečtěte si informace [o nastavení ovladače GPU řady N pro Windows](./windows/n-series-driver-setup.md) nebo [n-series pro Linux](./linux/n-series-driver-setup.md) pro podporované operační systémy, ovladače, instalaci a kroky ověření.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.
