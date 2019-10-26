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
ms.openlocfilehash: 224180bdebdf94c6d2360df8ea6615add4675921
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72902141"
---
## <a name="supported-distributions-and-drivers"></a>Podporované distribuce a ovladače

### <a name="nvidia-cuda-drivers"></a>Ovladače NVIDIA CUDA

Ovladače NVIDIA CUDA pro virtuální počítače s NC, NCv2, NCv3, ND a NDv2-Series (volitelné pro řady NV) se podporují jenom v distribucích systému Linux uvedených v následující tabulce. Informace o ovladači CUDA jsou aktuální v okamžiku publikování. Nejnovější ovladače CUDA najdete na webu [NVIDIA](https://developer.nvidia.com/cuda-zone) . Ujistěte se, že jste nainstalovali nebo upgradovali nejnovější ovladače CUDA pro vaši distribuci. 

> [!TIP]
> Jako alternativu k ruční instalaci ovladače CUDA na virtuálním počítači se systémem Linux můžete nasadit image Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) . Edice DSVM pro Ubuntu 16,04 LTS nebo CentOS 7,4: předinstalace ovladačů NVIDIA CUDA, CUDA s hloubkou neuronové síťové knihovny a dalších nástrojů.

| Distribuce | Ovladač |
| --- | -- | 
| Ubuntu 16,04 LTS, 18,04 LTS<br/><br/> Red Hat Enterprise Linux 7,3, 7,4, 7,5, 7,6<br/><br/> CentOS-based 7,3, 7,4, 7,5, 7,6, CentOS 7,4 prostředí HPC | NVIDIA CUDA 10,1, R418 Branch ovladačů |

### <a name="nvidia-grid-drivers"></a>Ovladače pro MŘÍŽKu NVIDIA

Microsoft redistribuuje instalační programy ovladačů NVIDIA GRID pro virtuální počítače a virtuální počítače řady NVv3-Series používané jako virtuální pracovní stanice nebo pro virtuální aplikace. Nainstalujte jenom tyto ovladače mřížky na virtuální počítače Azure NV, a to jenom v operačních systémech uvedených v následující tabulce. Tyto ovladače zahrnují licencování pro software služby GRID Virtual GPU v Azure. Nemusíte nastavovat licenční server pro software NVIDIA vGPU.

| Distribuce | Ovladač |
| --- | -- |
|Ubuntu 18,04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7,0 až 7,6<br/><br/>CentOS-based 7,0 až 7,6<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA GRID 9,1, R430 větví ovladače|

> [!WARNING] 
> Instalace softwaru třetích stran na produkty Red Hat může mít vliv na podmínky podpory Red Hat. Informace najdete v [článku znalostní báze Red Hat](https://access.redhat.com/articles/1067).
>
