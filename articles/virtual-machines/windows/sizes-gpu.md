---
title: Velikosti virtuálních počítačů Azure s Windows – GPU
description: Obsahuje seznam různých velikostí GPU, které jsou dostupné pro virtuální počítače s Windows v Azure. Uvádí informace o počtu vCPU, datových discích a síťových rozhraních a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 1f26ad56734dfec0dbf7fc8198b8a91a706a843b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75371067"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Velikosti virtuálních počítačů optimalizované pro GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Aby bylo možné využívat možnosti GPU pro virtuální počítače řady Azure N-Series s Windows, musí být nainstalované ovladače NVIDIA nebo AMD GPU. 

[Rozšíření ovladače NVIDIA GPU](../extensions/hpccompute-gpu-windows.md) nainstaluje vhodné ovladače NVIDIA CUDA nebo Grid na virtuální počítač řady N-Series. Nainstalujte nebo spravujte rozšíření pomocí Azure Portal nebo nástrojů, jako jsou šablony Azure PowerShell nebo Azure Resource Manager. Podporované operační systémy a kroky nasazení najdete v [dokumentaci k rozšíření ovladače GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) . Obecné informace o rozšířeních virtuálních počítačů najdete v tématu [rozšíření a funkce virtuálních počítačů Azure](../extensions/overview.md).

Pokud se rozhodnete nainstalovat ovladače GPU NVIDIA ručně, přečtěte si téma [nastavení ovladače GPU N-Series pro Windows](n-series-driver-setup.md) pro podporované operační systémy, ovladače a postup instalace a ověření.

Ruční instalace ovladačů AMD GPU najdete v tématu [instalace ovladačů AMD GPU řady N-Series pro Windows](n-series-amd-driver-setup.md) pro podporované operační systémy, ovladače a postup instalace a ověření.

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="other-sizes"></a>Jiné velikosti
- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

