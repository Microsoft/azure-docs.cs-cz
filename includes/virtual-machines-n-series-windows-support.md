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
ms.openlocfilehash: b3e097f1c41f3047dc4e9d6cae2a05a6b19dea9d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554925"
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

Pro Windows Server 2019, Windows Server 2016 1607, 1709 a Windows 10 (až do sestavení 20H2):
- [Mřížka 12,1 (461,33)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Mřížka 12,0 (461,09)](https://download.microsoft.com/download/4/8/d/48d2d45b-bebc-44ad-9c58-e0b79a9d4ff2/461.09_grid_win10_server2016_server2019_64bit_azure_swl.exe) (. exe) 

Pro Windows Server 2012 R2: 
- [Mřížka 12,1 (461,33)](https://download.microsoft.com/download/9/9/c/99caf5c6-af9f-48b2-bcb0-af5ec64b8592/461.33_grid_server2012R2_64bit_azure_swl.exe) (. exe)
- [Mřížka 12,0 (461,09)](https://download.microsoft.com/download/c/5/e/c5e7df99-364d-45f5-bff7-c253d59121f1/461.09_grid_server2012R2_64bit_azure_swl.exe) (. exe) 


Úplný seznam všech předchozích odkazů ovladačů NVIDIA GRID najdete na [GitHubu](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) .
