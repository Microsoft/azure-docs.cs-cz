---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bab282fded7e4b30d2eca6ed51ceaecf22206869
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166693"
---
## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

### <a name="nvidia-tesla-cuda-drivers"></a>Ovladače NVIDIA Tesla (CUDA)

Ovladače NVIDIA Tesla (CUDA) pro virtuální počítače NC, NCv2, NCv3, ND a NDv2-Series (volitelné pro řady NV) se podporují jenom v operačních systémech uvedených v následující tabulce. Odkazy na stažení ovladače jsou aktuální v době publikace. Nejnovější ovladače najdete na webu [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Jako alternativu k ruční instalaci ovladače CUDA na virtuálním počítači s Windows serverem můžete nasadit image Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) . Edice DSVM pro Windows Server 2016 předem instalují ovladače NVIDIA CUDA, síťovou knihovnu CUDA hluboké neuronové a další nástroje.


| OS | Ovladač |
| -------- |------------- |
| Windows Server 2016 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (. exe) |
| Windows Server 2012 R2 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>Ovladače pro MŘÍŽKu NVIDIA

Microsoft redistribuuje instalační programy ovladačů NVIDIA GRID pro virtuální počítače a virtuální počítače řady NVv3-Series používané jako virtuální pracovní stanice nebo pro virtuální aplikace. Nainstalujte jenom tyto ovladače mřížky na virtuální počítače Azure NV-Series jenom v operačních systémech uvedených v následující tabulce. Tyto ovladače zahrnují licencování pro software služby GRID Virtual GPU v Azure. Nemusíte nastavovat licenční server pro software NVIDIA vGPU.

Počítejte s tím, že rozšíření NVIDIA bude vždycky instalovat ovladač latst. Odkazy na předchozí verzi poskytujeme pro zákazníky, kteří mají závislost na starší verzi.

| OS | Ovladač |
| -------- |------------- |
| Windows Server. 2019<br/><br/>Windows Server 2016<br/><br/>Windows 10 | [Mřížka 9,0 (431,02)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe) <br/><br/> [Mřížka 8,0 (425,31)](https://download.microsoft.com/download/4/8/C/48C2D46E-EB64-460E-A8D9-0F55737D0D68/425.31_grid_win10_server2016_64bit_international.exe) (. exe)  |
| Windows Server 2012 R2<br/><br/>Windows Server 2008 R2<br/><br/>Windows 8<br/><br/>Windows 7 | [Mřížka 9,0 (431,02)](https://go.microsoft.com/fwlink/?linkid=874184) (. exe)<br/><br/> [Mřížka 8,0 (425,31)](https://download.microsoft.com/download/6/D/7/6D73C628-B5FB-4243-9520-DAEF363223CB/425.31_grid_win8_win7_server2012R2_server2008R2_64bit_international.exe) (. exe)  |
