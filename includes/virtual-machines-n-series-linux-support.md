---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a1200494eac2d60083a6608d1af91379d8298070
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201495"
---
## <a name="supported-distributions-and-drivers"></a>Podporované distribuce a ovladače

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA ovladače

NVIDIA CUDA ovladače síťového adaptéru, NCv2, NCv3, ND a virtuální počítače řady NDv2 (volitelné pro NV-series) jsou podporovány pouze v Linuxových distribucích uvedené v následující tabulce. Informace o ovladači CUDA jsou aktuální k datu publikování. Nejnovější ovladače CUDA, najdete [NVIDIA](https://developer.nvidia.com/cuda-zone) webu. Ujistěte se, že instalaci nebo upgradu na nejnovější ovladače CUDA pro vaši distribuci. 

> [!TIP]
> Jako alternativu k ruční instalaci ovladačů CUDA na Linuxovém virtuálním počítači, můžete nasadit Azure [virtuální počítač pro datové vědy](../articles/machine-learning/data-science-virtual-machine/overview.md) bitové kopie. V edicích DSVM pro Ubuntu 16.04 LTS a CentOS 7.4 předem instalace ovladačů NVIDIA CUDA, CUDA hluboké Neuronové sítě knihovny a další nástroje.

| Distribuce | Ovladač |
| --- | -- | 
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/> Založené na centOS 7.3, 7.4, 7.5, 7.6, založené na CentOS 7.4 HPC | NVIDIA CUDA 10.0, větev ovladačů R410 |

### <a name="nvidia-grid-drivers"></a>Ovladače NVIDIA GRID

Microsoft přerozděluje instalace ovladačů NVIDIA GRID pro NV a virtuální počítače řady NVv2 použít jako virtuálních pracovních stanic nebo pro virtuální aplikace. Pouze tyto ovladače mřížky na virtuálních počítačích Azure NV, se nainstalují jenom na operačních systémů uvedených v následující tabulce. Tyto ovladače obsahují licenci pro Software mřížky virtuální GPU v Azure. Nemusíte nastavení serveru NVIDIA vGPU softwarové licence.

| Distribuce | Ovladač |
| --- | -- |
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/>Založené na centOS 7.3, 7.4, 7.5, 7.6 | NVIDIA GRID 7, větev ovladačů R410|

> [!WARNING] 
> Instalace softwaru třetích stran na produkty Red Hat může mít vliv na podmínky podpory Red Hat. Informace najdete v [článku znalostní báze Red Hat](https://access.redhat.com/articles/1067).
>
