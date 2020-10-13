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
ms.openlocfilehash: 00661043d1ec9769adbf4119a2c9c1925dcd29fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88186321"
---
## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

### <a name="nvidia-tesla-cuda-drivers"></a>Ovladače NVIDIA Tesla (CUDA)

Ovladače NVIDIA Tesla (CUDA) pro virtuální počítače s NC, NCv2, NCv3, NCasT4_v3, ND a NDv2 Series (volitelné pro řady NV) se podporují jenom v operačních systémech uvedených v následující tabulce. Odkazy na stažení ovladače jsou aktuální v době publikace. Nejnovější ovladače najdete na webu [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Jako alternativu k ruční instalaci ovladače CUDA na virtuálním počítači s Windows serverem můžete nasadit image Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) . Edice DSVM pro Windows Server 2016 předem instalují ovladače NVIDIA CUDA, síťovou knihovnu CUDA hluboké neuronové a další nástroje.


| Operační systém | Ovladač |
| -------- |------------- |
| Windows Server 2019 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |
| Windows Server 2016 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>Ovladače pro MŘÍŽKu NVIDIA

Microsoft redistribuuje instalační programy ovladačů NVIDIA GRID pro virtuální počítače a virtuální počítače řady NVv3-Series používané jako virtuální pracovní stanice nebo pro virtuální aplikace. Nainstalujte jenom tyto ovladače mřížky na virtuální počítače Azure NV-Series jenom v operačních systémech uvedených v následující tabulce. Tyto ovladače zahrnují licencování pro software služby GRID Virtual GPU v Azure. Nemusíte nastavovat licenční server pro software NVIDIA vGPU.

Ovladače mřížky znovu distribuované pomocí Azure nefungují na virtuálních počítačích bez řady NV, jako jsou například NC, NCv2, NCv3, ND a NDv2-Series VM.

Počítejte s tím, že rozšíření NVIDIA bude vždycky instalovat nejnovější ovladač. Odkazy na předchozí verzi poskytujeme pro zákazníky, kteří mají závislost na starší verzi.

Pro Windows Server 2019, Windows Server 2016 a Windows 10 (až do buildu 2004):
- [Mřížka 11 (451,48)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Mřížka 10,1 (442,06)](https://download.microsoft.com/download/b/8/f/b8f5ecec-b8f9-47de-b007-ac40adc88dc8/442.06_grid_win10_64bit_international_whql.exe) (. exe) 

Pro Windows Server 2012 R2: 
- [Mřížka 11 (451,48)](https://go.microsoft.com/fwlink/?linkid=874184) (. exe)
- [Mřížka 10,1 (442,66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (. exe)  
