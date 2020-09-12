---
title: NCas T4 V3-Series
description: Specifikace pro virtuální počítače s NCas T4 řady V3
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: af9f7eb21d533bc5fb365e7cbf1fb8fc18184fa7
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89375223"
---
# <a name="ncast4_v3-series-in-preview"></a>NCasT4_v3-Series (ve verzi Preview) 

Virtuální počítače řady NCasT4_v3-Series využívají GPU a procesory AMD EPYC 7V12 (Řím) s technologií [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) . Virtuální počítače mají až 4 GPU T4 T4 s 16 GB paměti, a to až 64 procesorových jader AMD EPYC 7V12 (Řím) a 440 GiB systémové paměti. Tyto virtuální počítače jsou ideální pro spouštění úloh ML a AI s využitím CUDA, TensorFlow, Pytorch, Caffe a dalších platforem nebo grafických úloh pomocí technologie NVIDIA GRID. NCasT4_v3-Series jsou ideální pro spouštění odvozených úloh.

Žádost můžete [Odeslat](https://aka.ms/NCT4v3Preview) jako součást programu Preview.

<br>

ACU: 230-260

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Migrace za provozu: nepodporováno

Aktualizace pro zachování paměti: nepodporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Aby bylo možné využít možnosti GPU pro virtuální počítače Azure NCasT4_v3-Series se systémem Windows nebo Linux, je nutné nainstalovat ovladače GPU NVIDIA.

Ruční instalace ovladačů NVIDIA GPU najdete v tématu [instalace ovladače GPU řady N-Series pro Windows](./windows/n-series-driver-setup.md) pro podporované operační systémy, ovladače, instalace a postupy ověření.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
