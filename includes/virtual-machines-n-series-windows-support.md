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
ms.openlocfilehash: 40e5a1bf940e46aed566a1e3fa6dcb4e6b2d9230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77135043"
---
## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

### <a name="nvidia-tesla-cuda-drivers"></a>Ovladače NVIDIA Tesla (CUDA)

Ovladače NVIDIA Tesla (CUDA) pro virtuální počítače řady NC, NCv2, NCv3, ND a NDv2 (volitelné pro řady NV) jsou podporovány pouze v operačních systémech uvedených v následující tabulce. Odkazy ke stažení ovladačů jsou aktuální v době zveřejnění. Nejnovější ovladače najdete na webu [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Jako alternativu k ruční instalaci ovladače CUDA na virtuálním počítači se systémem Windows Server můžete nasadit image virtuálního počítače Azure [Data Science.](../articles/machine-learning/data-science-virtual-machine/overview.md) Edice DSVM pro Windows Server 2016 předinstalují ovladače NVIDIA CUDA, knihovnu CUDA Deep Neural Network Library a další nástroje.


| Operační systém | Ovladač |
| -------- |------------- |
| Windows Server 2016 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>Ovladače NVIDIA GRID

Společnost Microsoft redistribuuje instalační programy ovladačů NVIDIA GRID pro virtuální počítače řady NV a NVv3 používané jako virtuální pracovní stanice nebo pro virtuální aplikace. Nainstalujte jenom tyto ovladače GRID na virtuální počítače řady Azure NV, pouze v operačních systémech uvedených v následující tabulce. Tyto ovladače zahrnují licencování gridového virtuálního softwaru GPU v Azure. Není nutné nastavit licenční server softwaru NVIDIA vGPU.

Vezměte prosím na vědomí, že rozšíření Nvidia bude vždy instalovat nejnovější ovladač. Poskytujeme odkazy na předchozí verzi zde pro zákazníky, kteří mají závislost na starší verzi.

Pro Windows Server 2019, Windows Server 2016 a Windows 10 (až sestavení 1909):
- [MŘÍŽKA 10,1 (442,06)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [MŘÍŽKA 10,0 (441,66)](https://download.microsoft.com/download/2/a/3/2a316e62-3be9-4ddb-ae8e-c04b6df6e22d/441.66_grid_win10_server2016_server2019_64bit_international.exe) (.exe) 

Pro Windows Server 2012 R2, Windows Server 2008 R2, Windows 8 a Windows 7: 
- [MŘÍŽKA 10,1 (442,06)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)
- [MŘÍŽKA 10,0 (441,66)](https://download.microsoft.com/download/d/8/0/d80091f8-0d55-47c2-958a-bacd136f432a/441.66_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) (.exe)  
