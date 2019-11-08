---
title: vCPU kvóty pro Azure | Microsoft Docs
description: Přečtěte si o kvótách vCPU pro Azure.
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
ms.openlocfilehash: 15773ce54370c4557bd760249d4490896f3f5dea
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749273"
---
# <a name="virtual-machine-vcpu-quotas"></a>Kvóty virtuálních procesorů pro virtuální počítače

Kvóty vCPU pro virtuální počítače a sady škálování virtuálních počítačů jsou seřazené do dvou vrstev pro každé předplatné v každé oblasti. První z nich je celková oblast vCPU a druhá úroveň je různými jádry velikosti virtuálních počítačů, jako je vCPU řady D-Series. Kdykoliv je nasazený nový virtuální počítač, vCPU pro virtuální počítač nesmí překročit kvótu vCPU pro rodinu velikosti virtuálního počítače nebo celkovou kvótu vCPU v oblasti regionálního provozu. Pokud dojde k překročení některé z těchto kvót, nasazení virtuálního počítače se nepovolí. K dispozici je také kvóta pro celkový počet virtuálních počítačů v dané oblasti. Podrobnosti o každé z těchto kvót najdete v části **využití a kvóty** stránky **předplatného** v [Azure Portal](https://portal.azure.com)nebo se můžete dotazovat na hodnoty pomocí PowerShellu.

   
 
## <a name="check-usage"></a>Kontrola využití

Pomocí rutiny [Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) můžete ověřit využití kvóty.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

Výstup bude vypadat nějak takto:

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
Pro rezervované instance virtuálních počítačů, které jsou vymezené na jedno předplatné bez flexibilní velikosti virtuálních počítačů, se do kvót vCPU přidá nový aspekt. Tyto hodnoty popisují počet instancí uvedené velikosti, které je nutné nasadit v předplatném. Fungují jako zástupný symbol v systému kvót, aby bylo zajištěno, že je kvóta vyhrazena pro zajištění nasazení rezervovaných instancí virtuálních počítačů v rámci předplatného. Pokud má například konkrétní předplatné 10 Standard_D1 rezervovaných instancí virtuálních počítačů, omezení využití pro Standard_D1 rezervované instance virtuálních počítačů bude 10. Tím dojde k tomu, že Azure zajistí, že bude k dispozici vždy aspoň 10 vCPU v celkové místní kvótě vCPU, která se má použít pro instance Standard_D1, a v standardní kvótě pro vCPU řady D je k dispozici aspoň 10 vCPU, které se dají použít pro instance Standard_D1.

Pokud se k nákupu jediného předplatného na rezervovaném počítači vyžaduje zvýšení kvóty, můžete [požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) u svého předplatného.

## <a name="next-steps"></a>Další kroky

Další informace o fakturaci a kvótách najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
