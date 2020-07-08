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
ms.openlocfilehash: 279f4cb1d8891f64fb01f4f8abb6b23a004c2b4d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84269145"
---
## <a name="supported-distributions-and-drivers"></a>Podporované distribuce a ovladače

### <a name="nvidia-cuda-drivers"></a>Ovladače NVIDIA CUDA

Ovladače NVIDIA CUDA pro virtuální počítače s NC, NCv2, NCv3, ND a NDv2-Series (volitelné pro řady NV) se podporují jenom v distribucích systému Linux uvedených v následující tabulce. Informace o ovladači CUDA jsou aktuální v okamžiku publikování. Nejnovější ovladače CUDA a podporované operační systémy najdete na webu [NVIDIA](https://developer.nvidia.com/cuda-zone) . Ujistěte se, že jste nainstalovali nebo upgradovali nejnovější ovladače CUDA pro vaši distribuci. 

> [!TIP]
> Jako alternativu k ruční instalaci ovladače CUDA na virtuálním počítači se systémem Linux můžete nasadit image Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) . Edice DSVM pro Ubuntu 16,04 LTS nebo CentOS 7,4: předinstalace ovladačů NVIDIA CUDA, CUDA s hloubkou neuronové síťové knihovny a dalších nástrojů.


### <a name="nvidia-grid-drivers"></a>Ovladače pro MŘÍŽKu NVIDIA

Microsoft redistribuuje instalační programy ovladačů NVIDIA GRID pro virtuální počítače a virtuální počítače řady NVv3-Series používané jako virtuální pracovní stanice nebo pro virtuální aplikace. Nainstalujte jenom tyto ovladače mřížky na virtuální počítače Azure NV, a to jenom v operačních systémech uvedených v následující tabulce. Tyto ovladače zahrnují licencování pro software služby GRID Virtual GPU v Azure. Nemusíte nastavovat licenční server pro software NVIDIA vGPU.

| Distribuce | Ovladač |
| --- | -- |
|Ubuntu 18,04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7,0 až 7,7<br/><br/>CentOS-based 7,0 až 7,7<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA GRID 10,1, R440 větví ovladače|

> [!WARNING] 
> Instalace softwaru třetích stran na produkty Red Hat může mít vliv na podmínky podpory Red Hat. Informace najdete v [článku znalostní báze Red Hat](https://access.redhat.com/articles/1067).
>
