---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 22d37ca30f1319f46a52b96be1c527f6f56719ab
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
## <a name="supported-distributions-and-drivers"></a>Podporované distribuce a ovladače

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>NC, NCv2, NCv3 a a series - NVIDIA CUDA ovladače
| Distribuce | Ovladač |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 nebo 7.4<br/><br/> CentOS 7.3 nebo 7.4 | NVIDIA CUDA 9.1, ovladač větve R390 |

> [!IMPORTANT]
> Ujistěte se, že instalaci nebo upgrade na nejnovější ovladače CUDA pro distribuční. Ovladače, které jsou starší než verze R390 mohou mít problémy s aktualizované jádra systému Linux.
>

### <a name="nv-series---nvidia-grid-drivers"></a>VS series - ovladače NVIDIA mřížky

| Distribuce | Ovladač |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>Distribuce založené na CentOS 7.3 | 5.2 NVIDIA mřížky, ovladač větve R384|

> [!NOTE]
> Microsoft znovu distribuuje NVIDIA mřížky ovladač instalačních programů pro virtuální počítače vs. Nainstalujte pouze tyto ovladače mřížky na virtuálních počítačích Azure vs. Tyto ovladače zahrnují licencování pro mřížky virtuální grafický procesor Software v Azure.
>

> [!WARNING] 
> Instalace softwaru třetích stran na produkty Red Hat může mít vliv na podmínky podpory Red Hat. Informace najdete v [článku znalostní báze Red Hat](https://access.redhat.com/articles/1067).
>
