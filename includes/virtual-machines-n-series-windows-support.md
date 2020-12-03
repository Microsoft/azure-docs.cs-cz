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
ms.openlocfilehash: caad292f06710fca8d6f64476eead5dfcb164e9d
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96537079"
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

Ovladače mřížky znovu distribuované pomocí Azure nefungují na virtuálních počítačích bez řady NV, jako jsou například NC, NCv2, NCv3, ND a NDv2-Series VM. Jedinou výjimkou jsou NCas_T4_V3 řady virtuálních počítačů, ve kterých budou ovladače mřížky umožňovat fungování grafiky podobně jako u řady NV.

Počítejte s tím, že rozšíření NVIDIA bude vždycky instalovat nejnovější ovladač. Odkazy na předchozí verzi poskytujeme pro zákazníky, kteří mají závislost na starší verzi.

Pro Windows Server 2019, Windows Server 2016 a Windows 10 (až do buildu 2004):
- [Mřížka 11,2 (452,57)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Mřížka 11,1 (452,39)](https://download.microsoft.com/download/9/9/1/99186e1b-d27d-47d5-9957-175c88f4efbe/452.39_grid_win10_64bit_whql.exe) (. exe) 

Pro Windows Server 2012 R2: 
- [Mřížka 11,0 (451,48)](https://download.microsoft.com/download/f/7/2/f729e28b-57b8-4141-b577-38d2390973ef/451.48_grid_server2012R2_64bit_international.exe) (. exe) 
- [Mřížka 10,1 (442,66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (. exe)  

Úplný seznam všech předchozích odkazů ovladačů NVIDIA GRID najdete na [GitHubu](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) .
