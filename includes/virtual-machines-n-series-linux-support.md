---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/08/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c829b8d6fedaabfb9b43c6352c8188128cf36701
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51333745"
---
## <a name="supported-distributions-and-drivers"></a>Podporované distribuce a ovladače

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA ovladače

NVIDIA CUDA ovladače síťového adaptéru, NCv2, NCv3 a virtuální počítače řady ND-series (volitelné pro NV-series) jsou podporovány pouze v Linuxových distribucích uvedené v následující tabulce. Informace o ovladači CUDA jsou aktuální k datu publikování. Nejnovější ovladače CUDA, najdete [NVIDIA](https://developer.nvidia.com/cuda-zone) webu. Ujistěte se, že instalaci nebo upgradu na nejnovější ovladače CUDA pro vaši distribuci. 

> [!TIP]
> Jako alternativu k ruční instalaci ovladačů CUDA na Linuxovém virtuálním počítači, můžete nasadit Azure [virtuální počítač pro datové vědy](../articles/machine-learning/data-science-virtual-machine/overview.md) bitové kopie. V edicích DSVM pro Ubuntu 16.04 LTS a CentOS 7.4 předem instalace ovladačů NVIDIA CUDA, CUDA hluboké Neuronové sítě knihovny a další nástroje.

| Distribuce | Ovladač |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 nebo 7.4<br/><br/> Založené na centOS 7.3 nebo 7.4, založené na CentOS 7.4 HPC | NVIDIA CUDA 10.0, větev ovladačů R410 |

### <a name="nvidia-grid-drivers"></a>Ovladače NVIDIA GRID

Microsoft přerozděluje instalace ovladačů NVIDIA GRID pro NV a virtuální počítače řady NVv2 použít jako virtuálních pracovních stanic nebo pro virtuální aplikace. Pouze tyto ovladače mřížky na virtuálních počítačích Azure NV, se nainstalují jenom na operačních systémů uvedených v následující tabulce. Tyto ovladače obsahují licenci pro Software mřížky virtuální GPU v Azure. Nemusíte nastavení serveru NVIDIA vGPU softwarové licence.

| Distribuce | Ovladač |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 nebo 7.4<br/><br/>Na základě centOS 7.3 nebo 7.4 | NVIDIA GRID 6.2, větev ovladačů R390|

> [!WARNING] 
> Instalace softwaru třetích stran na produkty Red Hat může mít vliv na podmínky podpory Red Hat. Informace najdete v [článku znalostní báze Red Hat](https://access.redhat.com/articles/1067).
>
