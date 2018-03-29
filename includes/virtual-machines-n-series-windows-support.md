---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 426340a5b452124dcba4f51f2ec2c1e2ec0f54b2
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-cuda-drivers"></a>NC, NCv2, NCv3 a a series - ovladače NVIDIA tesla – měrná (CUDA)

Odkazy na stažení ovladače v následující tabulce jsou aktuální v době publikace. Nejnovější ovladače najdete na webu [NVIDIA](http://www.nvidia.com/).

> [!TIP]
> Jako alternativu k ruční instalaci ovladačů CUDA na virtuální počítač Windows serveru, můžete nasadit Azure [datové vědy virtuálního počítače](../articles/machine-learning/data-science-virtual-machine/overview.md) bitové kopie. V edicích DSVM pro Windows Server 2016 předinstalační ovladače NVIDIA CUDA, CUDA hluboké Neuronové sítě knihovny a další nástroje.


| Operační systém | Ovladač |
| -------- |------------- |
| Windows Server 2016 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nv-series---nvidia-grid-drivers"></a>VS series - ovladače NVIDIA mřížky

Microsoft znovu distribuuje NVIDIA mřížky ovladač instalačních programů pro virtuální počítače vs. Nainstalujte pouze tyto ovladače mřížky na virtuálních počítačích Azure vs. Tyto ovladače zahrnují licencování pro mřížky virtuální grafický procesor Software v Azure.

| Operační systém | Ovladač |
| -------- |------------- |
| Windows Server 2016 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |