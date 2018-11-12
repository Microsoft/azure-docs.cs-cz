---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/24/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 2453cee167df3da9bf524fc446953b4304686564
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51293195"
---
## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

### <a name="nvidia-tesla-cuda-drivers"></a>Ovladače NVIDIA Tesla (CUDA)

Ovladače NVIDIA Tesla (CUDA) pro síťový adaptér, NCv2, virtuální počítače i řada ND-series (volitelné pro NV-series) a NCv3 jsou podporovány pouze na operačních systémů uvedených v následující tabulce. Odkazy na stažení ovladače jsou aktuální k datu publikování. Nejnovější ovladače najdete na webu [NVIDIA](http://www.nvidia.com/).

> [!TIP]
> Jako alternativu k ruční instalaci ovladačů CUDA na virtuální počítač s Windows serverem, můžete nasadit Azure [virtuální počítač pro datové vědy](../articles/machine-learning/data-science-virtual-machine/overview.md) bitové kopie. V edicích DSVM pro Windows Server 2016 předem instalace ovladačů NVIDIA CUDA, CUDA hluboké Neuronové sítě knihovny a další nástroje.


| OS | Ovladač |
| -------- |------------- |
| Windows Server 2016 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |


### <a name="nvidia-grid-drivers"></a>Ovladače NVIDIA GRID

Microsoft přerozděluje instalace ovladačů NVIDIA GRID pro NV a virtuální počítače řady NVv2 použít jako virtuálních pracovních stanic nebo pro virtuální aplikace. Pouze tyto ovladače mřížky na virtuálních počítačích Azure NV, se nainstalují jenom na operačních systémů uvedených v následující tabulce. Tyto ovladače obsahují licenci pro Software mřížky virtuální GPU v Azure.

| OS | Ovladač |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [MŘÍŽKA 6.2 (391.81)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe) |
| Windows Server 2012 R2 | [MŘÍŽKA 6.2 (391.81)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)  |
