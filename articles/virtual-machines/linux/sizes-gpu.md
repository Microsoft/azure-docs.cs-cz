---
title: Velikosti virtuálních počítačů Azure Linux – akcelerované výpočetní prostředky
description: Obsahuje seznam různých velikostí GPU, které jsou dostupné pro virtuální počítače Linux v Azure. Uvádí informace o počtu vCPU, datových discích a síťových rozhraních a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 983e85dc0ebefb27804c0f3a794360def6050ba9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034911"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Velikosti virtuálních počítačů optimalizované pro GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>Podporované distribuce a ovladače

Aby bylo možné využívat možnosti GPU pro virtuální počítače řady Azure N-Series se systémem Linux, je nutné nainstalovat ovladače GPU NVIDIA. [Rozšíření ovladače NVIDIA GPU](../extensions/hpccompute-gpu-linux.md) nainstaluje vhodné ovladače NVIDIA CUDA nebo Grid na virtuální počítač řady N-Series. Nainstalujte nebo spravujte rozšíření pomocí Azure Portal nebo nástrojů, jako je Azure CLI nebo šablony Azure Resource Manager. Podporované distribuce a kroky nasazení najdete v [dokumentaci k rozšíření ovladače GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) . Obecné informace o rozšířeních virtuálních počítačů najdete v tématu [rozšíření a funkce virtuálních počítačů Azure](../extensions/overview.md).

Pokud se rozhodnete nainstalovat ovladače GPU NVIDIA ručně, přečtěte si téma [nastavení ovladače GPU N-Series pro Linux](n-series-driver-setup.md) pro podporované distribuce, ovladače a kroky instalace a ověření.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Neměli byste instalovat X server nebo jiné systémy, které používají `Nouveau` ovladače na virtuálních počítačích Ubuntu NC. Před instalací ovladačů NVIDIA GPU je potřeba zakázat ovladač `Nouveau`.  

## <a name="other-sizes"></a>Jiné velikosti
- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.