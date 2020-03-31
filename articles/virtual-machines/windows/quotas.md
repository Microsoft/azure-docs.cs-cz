---
title: Kvóty virtuálních procesorů pro Azure
description: Přečtěte si o kvótách virtuálních procesorů pro Azure.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 20017f42031e126bbdae40f66bf4c36efd12f17c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893464"
---
# <a name="virtual-machine-vcpu-quotas"></a>Kvóty virtuálních procesorů pro virtuální počítače

Kvóty virtuálních procesorů pro virtuální počítače a škálovací sady virtuálních počítačů jsou uspořádány do dvou úrovní pro každé předplatné v každé oblasti. První vrstva je celková regionální virtuální procesory a druhá vrstva je různé jádra rodiny velikosti virtuálních počítače, jako jsou virtuální procesory řady D. Pokaždé, když se nasadí nový virtuální virtuální procesor, nesmí virtuální procesory pro virtuální počítače překročit kvótu virtuálního procesoru pro rodinu velikosti virtuálního počítače nebo celkovou kvótu místního virtuálního procesoru. Pokud je překročena některá z těchto kvót, nasazení virtuálního počítače nebude povoleno. K dispozici je také kvóta pro celkový počet virtuálních počítačů v oblasti. Podrobnosti o každé z těchto kvót najdete v části **Využití + kvóty** na stránce **Předplatné** na webu [Azure Portal](https://portal.azure.com)nebo můžete dotazovat na hodnoty pomocí PowerShellu.

   
 
## <a name="check-usage"></a>Kontrola využití

Rutina [Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) můžete zkontrolovat využití kvóty.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

Výstup bude vypadat podobně jako tento:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>Rezervované instance virtuálních počítačů
Rezervované instance virtuálních počítače, které jsou vymezeny na jedno předplatné bez flexibility velikosti virtuálního počítače, přidá nový aspekt kvót virtuálních procesorů. Tyto hodnoty popisují počet instancí uvedené velikosti, které musí být nasaditelné v předplatném. Pracují jako zástupný symbol v systému kvót, aby bylo zajištěno, že kvóta je vyhrazena pro zajištění vyhrazených instancí virtuálních aplikací, které lze nasadit v předplatném. Například pokud konkrétní předplatné má 10 Standard_D1 rezervované instance virtuálních počítače omezení využití pro Standard_D1 rezervované instance virtuálních počítače bude 10. To způsobí, že Azure zajistí, že v kvótě virtuálních procesorů Total Regional, která se má použít pro Standard_D1 instance, bude vždy k dispozici alespoň 10 virtuálních procesorů a že v kvótě virtuálních procesorů řady Standard D bude k dispozici alespoň 10 virtuálních procesorů, které se mají použít pro Standard_D1 instance.

Pokud je k zakoupení ri jednoho předplatného vyžadováno zvýšení kvóty, můžete [požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) u předplatného.

## <a name="next-steps"></a>Další kroky

Další informace o fakturaci a kvótách najdete v [tématu Limity předplatného a služeb Azure, kvóty a omezení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
