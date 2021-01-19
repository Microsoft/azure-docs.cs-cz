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
ms.openlocfilehash: 7a6962a0fa1374edb5a9f43641a0adf398708acf
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570825"
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

Ovladače mřížky distribuované pomocí Azure nefungují na virtuálních počítačích řady bez NV, jako jsou virtuální počítače NCv2, NCv3, ND a NDv2-Series. Jedinou výjimkou jsou NCas_T4_V3 řady virtuálních počítačů, ve kterých budou ovladače mřížky umožňovat fungování grafiky podobně jako u řady NV.

NC-Series s grafickými procesory NVIDIA K80 nepodporuje aplikace mřížky a grafiky.  

Počítejte s tím, že rozšíření NVIDIA bude vždycky instalovat nejnovější ovladač. Odkazy na předchozí verzi poskytujeme pro zákazníky, kteří mají závislost na starší verzi.

Pro Windows Server 2019, Windows Server 2016 a Windows 10 (až do buildu 2004):
- [Mřížka 11,3 (452,77)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Mřížka 11,2 (452,57)](https://download.microsoft.com/download/1/b/1/1b15516a-de49-4ba4-8651-6abda4f7fb82/452.57_grid_win10_server2016_server2019_64bit_international.exe) (. exe) 

Pro Windows Server 2012 R2: 
- [Mřížka 11,3 (452,77)](https://download.microsoft.com/download/5/4/3/54323644-3c84-4aa1-97ec-35491f94c866/452.77_grid_server2012R2_64bit_azure_swl.exe) (. exe) 
- [Mřížka 11,0 (451,48)](https://download.microsoft.com/download/f/7/2/f729e28b-57b8-4141-b577-38d2390973ef/451.48_grid_server2012R2_64bit_international.exe) (. exe)

Úplný seznam všech předchozích odkazů ovladačů NVIDIA GRID najdete na [GitHubu](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) .
