---
title: Virtuální počítač Azure s Linuxem velikostí - GPU | Microsoft Docs
description: Seznamy různých GPU optimalizované velikosti, které jsou k dispozici pro virtuální počítače s Linuxem v Azure. Uvádí informace o počtu Vcpu, datové disky a síťové adaptéry, jakož i úložiště propustnost a šířku pásma sítě pro velikosti této série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jonbeck
ms.openlocfilehash: c4704dd461ae96600fa812fdfe8d9b0e59e93d72
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Grafický procesor optimalizované velikosti virtuálních počítačů

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

Postup instalace a ověření ovladačů najdete v tématu [instalaci ovladačů N-series pro Linux](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Neinstalujte X server nebo jiné systémy, které používají `Nouveau` ovladač na virtuálních počítačích Ubuntu názvového kontextu. Před instalací NVIDIA GPU ovladače, je nutné zakázat `Nouveau` ovladačů.  

## <a name="other-sizes"></a>Další velikosti
- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)

## <a name="next-steps"></a>Další postup
Další informace o [Azure výpočetní jednotky (ACU)](acu.md) můžete porovnat výpočetní výkon v Azure SKU.