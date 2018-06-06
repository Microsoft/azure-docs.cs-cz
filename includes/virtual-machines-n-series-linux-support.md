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
ms.openlocfilehash: 0264f92fa10bd503a2811ce40ee0b8d4edd5f3b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34669828"
---
## <a name="supported-distributions-and-drivers"></a>Podporované distribuce a ovladače

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA ovladače

Ovladače NVIDIA CUDA NC, NCv2, NCv3 a virtuální počítače a series (volitelné pro vs series) jsou podporovány pouze na Linuxových distribucích uvedené v následující tabulce. Informací o ovladači CUDA je aktuální v době publikace. Nejnovější ovladače CUDA, najdete v článku [NVIDIA](https://developer.nvidia.com/cuda-zone) webu. Ujistěte se, že instalaci nebo upgrade na nejnovější ovladače CUDA pro distribuční. 

> [!TIP]
> Jako alternativu k ruční instalaci ovladačů CUDA na virtuální počítač s Linuxem můžete nasadit Azure [datové vědy virtuálního počítače](../articles/machine-learning/data-science-virtual-machine/overview.md) bitové kopie. V edicích DSVM Ubuntu 16.04 LTS nebo CentOS 7.4 předinstalační ovladače NVIDIA CUDA, CUDA hluboké Neuronové sítě knihovny a další nástroje.

| Distribuce | Ovladač |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 nebo 7.4<br/><br/> Na základě centOS 7.3 nebo 7.4, na základě CentOS 7.4 HPC | NVIDIA CUDA 9.1, ovladač větve R390 |

### <a name="nvidia-grid-drivers"></a>Ovladače NVIDIA mřížky

Microsoft znovu distribuuje NVIDIA mřížky ovladač instalačních programů pro virtuální počítače vs series používá jako virtuální pracovní stanice nebo pro virtuální aplikace. Nainstalujte pouze tyto ovladače mřížky na virtuálních počítačích Azure vs, jenom na distribuce uvedené v následující tabulce. Tyto ovladače zahrnují licencování pro mřížky virtuální grafický procesor Software v Azure.

| Distribuce | Ovladač |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 nebo 7.4<br/><br/>Na základě centOS 7.3 nebo 7.4 | NVIDIA mřížky 6.0, ovladač větve R390|



> [!WARNING] 
> Instalace softwaru třetích stran na produkty Red Hat může mít vliv na podmínky podpory Red Hat. Informace najdete v [článku znalostní báze Red Hat](https://access.redhat.com/articles/1067).
>
