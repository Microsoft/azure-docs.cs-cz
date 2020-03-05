---
title: NVv4-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady NVv4-Series.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 0295ed7d44d64fcc1aeb68e1beaa37987b177edb
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273848"
---
# <a name="nvv4-series"></a>Řada NVv4 

Virtuální počítače řady NVv4-Series využívají procesory [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU a AMD EPYC 7V12 (Řím). S NVv4-Series Azure zavádí virtuální počítače s částečnými procesory GPU. Vyberte správnou velikost virtuálního počítače pro grafické aplikace s grafickým procesorem (GPU) a virtuální plochy od 1.8. GiB vyrovnávací paměti pro plný grafický procesor s 16 GiB vyrovnávací pamětí snímků. Virtuální počítače s NVv4 v současné době podporují jenom hostovaný operační systém Windows.

<br>

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Migrace za provozu: nepodporováno

Aktualizace pro zachování paměti: nepodporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> NVv4-Series virtuální počítače s technologií AMD s více vlákny

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Aby bylo možné využívat možnosti GPU pro virtuální počítače Azure NVv4-Series s Windows, musí být nainstalované ovladače AMD GPU.

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
