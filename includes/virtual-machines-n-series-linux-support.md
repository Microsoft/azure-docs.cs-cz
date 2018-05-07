---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 05/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 7139ec67536a1c0e41c991db6d867b956f995c11
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
## <a name="supported-distributions-and-drivers"></a>Podporované distribuce a ovladače

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>NC, NCv2, NCv3 a a series - NVIDIA CUDA ovladače

Je aktuální v době publikování informací o ovladači CUDA v následující tabulce. Nejnovější ovladače CUDA, najdete v článku [NVIDIA](https://developer.nvidia.com/cuda-zone) webu. Ujistěte se, že instalaci nebo upgrade na nejnovější ovladače CUDA pro distribuční. 

> [!TIP]
> Jako alternativu k ruční instalaci ovladačů CUDA na virtuální počítač s Linuxem můžete nasadit Azure [datové vědy virtuálního počítače](../articles/machine-learning/data-science-virtual-machine/overview.md) bitové kopie. V edicích DSVM Ubuntu 16.04 LTS nebo CentOS 7.4 předinstalační ovladače NVIDIA CUDA, CUDA hluboké Neuronové sítě knihovny a další nástroje.

| Distribuce | Ovladač |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 nebo 7.4<br/><br/> Na základě centOS 7.3 nebo 7.4, na základě CentOS 7.4 HPC | NVIDIA CUDA 9.1, ovladač větve R390 |

### <a name="nv-series---nvidia-grid-drivers"></a>VS series - ovladače NVIDIA mřížky

Microsoft znovu distribuuje NVIDIA mřížky ovladač instalačních programů pro virtuální počítače vs. Nainstalujte pouze tyto ovladače mřížky na virtuálních počítačích Azure vs. Tyto ovladače zahrnují licencování pro mřížky virtuální grafický procesor Software v Azure.

| Distribuce | Ovladač |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 nebo 7.4<br/><br/>Na základě centOS 7.3 nebo 7.4 | NVIDIA mřížky 6.0, ovladač větve R390|



> [!WARNING] 
> Instalace softwaru třetích stran na produkty Red Hat může mít vliv na podmínky podpory Red Hat. Informace najdete v [článku znalostní báze Red Hat](https://access.redhat.com/articles/1067).
>
