---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 05/29/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 1813367a2d143f75fb51a3160dd00219c709c57b
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935791"
---
## <a name="supported-distributions-and-drivers"></a>Podporované distribuce a ovladače

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA ovladače

NVIDIA CUDA ovladače síťového adaptéru, NCv2, NCv3 a virtuální počítače řady ND-series (volitelné pro NV-series) jsou podporovány pouze v Linuxových distribucích uvedené v následující tabulce. Informace o ovladači CUDA jsou aktuální k datu publikování. Nejnovější ovladače CUDA, najdete [NVIDIA](https://developer.nvidia.com/cuda-zone) webu. Ujistěte se, že instalaci nebo upgradu na nejnovější ovladače CUDA pro vaši distribuci. 

> [!TIP]
> Jako alternativu k ruční instalaci ovladačů CUDA na Linuxovém virtuálním počítači, můžete nasadit Azure [virtuální počítač pro datové vědy](../articles/machine-learning/data-science-virtual-machine/overview.md) bitové kopie. V edicích DSVM pro Ubuntu 16.04 LTS a CentOS 7.4 předem instalace ovladačů NVIDIA CUDA, CUDA hluboké Neuronové sítě knihovny a další nástroje.

| Distribuce | Ovladač |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 nebo 7.4<br/><br/> Založené na centOS 7.3 nebo 7.4, založené na CentOS 7.4 HPC | NVIDIA CUDA 9.1, větev ovladačů R390 |

### <a name="nvidia-grid-drivers"></a>Ovladače NVIDIA GRID

Microsoft přerozděluje instalace ovladačů NVIDIA GRID pro virtuální počítače NV-series používá jako virtuálních pracovních stanic nebo pro virtuální aplikace. Instalovat jenom tyto ovladače mřížky na virtuálních počítačích Azure NV, pouze v distribucích uvedené v následující tabulce. Tyto ovladače obsahují licenci pro Software mřížky virtuální GPU v Azure.

| Distribuce | Ovladač |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 nebo 7.4<br/><br/>Na základě centOS 7.3 nebo 7.4 | NVIDIA GRID 6.2, větev ovladačů R390|



> [!WARNING] 
> Instalace softwaru třetích stran na produkty Red Hat může mít vliv na podmínky podpory Red Hat. Informace najdete v [článku znalostní báze Red Hat](https://access.redhat.com/articles/1067).
>
