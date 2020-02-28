---
title: NVv4-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady NVv4-Series.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 2afec4dc89c8d8892356888ec3404d36ba929d23
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669637"
---
# <a name="nvv4-series-preview"></a>NVv4-Series (Preview)

Virtuální počítače řady NVv4-Series využívají procesory [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU a AMD EPYC 7V12 (Řím). S NVv4-Series Azure zavádí virtuální počítače s částečnými procesory GPU. Vyberte správnou velikost virtuálního počítače pro grafické aplikace s grafickým procesorem (GPU) a virtuální plochy od 1.8. GiB vyrovnávací paměti pro plný grafický procesor s 16 GiB vyrovnávací pamětí snímků. Virtuální počítače s NVv4 v současné době podporují jenom hostovaný operační systém Windows.

[Zaregistrujte se a získejte přístup k těmto počítačům ve verzi Preview](https://aka.ms/nvv4signup).
<br>

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> NVv4-Series virtuální počítače s technologií AMD s více vlákny

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Aby bylo možné využívat možnosti GPU pro virtuální počítače řady Azure N-Series s Windows, musí být nainstalované ovladače NVIDIA nebo AMD GPU.

[Rozšíření ovladače NVIDIA GPU](./extensions/hpccompute-gpu-windows.md) nainstaluje vhodné ovladače NVIDIA CUDA nebo Grid na virtuální počítač s Windows N-Series. Nainstalujte nebo spravujte rozšíření pomocí Azure Portal nebo nástrojů, jako jsou šablony Azure PowerShell nebo Azure Resource Manager. Podporované operační systémy a kroky nasazení najdete v [dokumentaci k rozšíření ovladače GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) . Obecné informace o rozšířeních virtuálních počítačů najdete v tématu [rozšíření a funkce virtuálních počítačů Azure](/.extensions/overview.md).

Pokud se rozhodnete nainstalovat ovladače GPU NVIDIA ručně, přečtěte si téma [nastavení ovladače GPU N-Series pro Windows](./windows/n-series-driver-setup.md) pro podporované operační systémy, ovladače, instalace a postupy ověření.

Ruční instalace ovladačů AMD GPU najdete v tématu [instalace ovladačů AMD GPU řady N-Series pro Windows](./windows/n-series-amd-driver-setup.md) pro podporované operační systémy, ovladače, instalace a postupy ověření.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.