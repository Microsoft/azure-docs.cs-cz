---
title: NCas T4 V3-Series
description: Specifikace pro virtuální počítače s NCas T4 řady V3
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: vikancha
ms.openlocfilehash: 41ab057bdbf31667e45a73b2c5ded4993e716a66
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131555"
---
# <a name="ncast4_v3-series"></a>Řada NCasT4_v3 

Virtuální počítače řady NCasT4_v3-Series využívají GPU a procesory AMD EPYC 7V12 (Řím) s technologií [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) . Virtuální počítače mají až 4 GPU T4 T4 s 16 GB paměti, a to až 64 procesorových jader AMD EPYC 7V12 (Řím) a 440 GiB systémové paměti. Tyto virtuální počítače jsou ideální pro nasazení služeb AI – například Inferencing v reálném čase, vygenerované požadavky uživatelů, nebo pro interaktivní úlohy grafiky a vizualizace pomocí ovladače grafiky NVIDIA a technologie Virtual GPU. Standardní výpočetní úlohy GPU založené na CUDA, TensorRT, Caffe, ONNX a dalších rozhraních nebo OpenGL grafické aplikace založené na GPU a rozhraní DirectX se dají v řadě NCasT4_v3 nasazovat ekonomicky s blízkými uživateli.

<br>

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): nepodporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): nepodporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
Akcelerované síťové služby: podporované<br>
NVIDIA NVLink Interconnect: nepodporováno<br>
<br>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 / 8000 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4 / 8 000  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8 / 8000  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8 / 32000  |


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
