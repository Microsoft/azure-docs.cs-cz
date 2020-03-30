---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 5dccdb6c357635e78b076b1560bf6c0c62c03753
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77135100"
---
## <a name="supported-distributions-and-drivers"></a>Podporované distribuce a ovladače

### <a name="nvidia-cuda-drivers"></a>Ovladače NVIDIA CUDA

Ovladače NVIDIA CUDA pro virtuální počítače řady NC, NCv2, NCv3, ND a NDv2 (volitelné pro řady NV) jsou podporovány pouze v distribucích Linuxu uvedených v následující tabulce. Informace o ovladači CUDA jsou aktuální v době zveřejnění. Nejnovější ovladače CUDA naleznete na webových stránkách [společnosti NVIDIA.](https://developer.nvidia.com/cuda-zone) Ujistěte se, že nainstalujete nebo upgradujete na nejnovější ovladače CUDA pro vaši distribuci. 

> [!TIP]
> Jako alternativu k ruční instalaci ovladače CUDA na virtuálním počítači SUS můžete nasadit image [virtuálního počítače Azure Data Science.](../articles/machine-learning/data-science-virtual-machine/overview.md) Edice DSVM pro Ubuntu 16.04 LTS nebo CentOS 7.4 předinstalují ovladače NVIDIA CUDA, knihovnu CUDA Deep Neural Network Library a další nástroje.

| Distribuce | Ovladač |
| --- | -- | 
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/> 7.3 na bázi CentOS, 7,4, 7,5, 7,6, 7,4 HPC na bázi CentOS | NVIDIA CUDA 10.1, pobočka ovladače R418 |

### <a name="nvidia-grid-drivers"></a>Ovladače NVIDIA GRID

Společnost Microsoft redistribuuje instalační programy ovladačů NVIDIA GRID pro virtuální počítače řady NV a NVv3 používané jako virtuální pracovní stanice nebo pro virtuální aplikace. Nainstalujte jenom tyto ovladače GRID na virtuálních počítačích Azure NV, pouze v operačních systémech uvedených v následující tabulce. Tyto ovladače zahrnují licencování gridového virtuálního softwaru GPU v Azure. Není nutné nastavit licenční server softwaru NVIDIA vGPU.

| Distribuce | Ovladač |
| --- | -- |
|Ubuntu 18.04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.0 až 7.6<br/><br/>CentOS-založené 7,0 až 7,6<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA GRID 10.1, pobočka ovladače R440|

> [!WARNING] 
> Instalace softwaru třetích stran na produkty Red Hat může mít vliv na podmínky podpory Red Hat. Informace najdete v [článku znalostní báze Red Hat](https://access.redhat.com/articles/1067).
>
