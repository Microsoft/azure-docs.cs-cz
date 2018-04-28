---
title: virtuální procesory kvóty pro Azure | Microsoft Docs
description: Další informace o virtuálních procesorů kvóty pro Azure.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: cynthn
ms.openlocfilehash: 50f28fcb2d7945949134d89f6bcc4238cf625bd1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="virtual-machine-vcpu-quotas"></a>Virtuální počítač virtuální procesor kvóty

Virtuální procesory kvóty pro virtuální počítače a sady škálování virtuálního počítače jsou uspořádány do dvou vrstev pro každé předplatné, v každé oblasti. První vrstva je celkový počet regionální Vcpu a druhé vrstvy je různých virtuálních počítačů velikost rodiny jádrech například Vcpu standardní D rodiny. Při každém vytvoření nového virtuálního počítače je nasadit Vcpu pro nově nasazené virtuální počítač nesmí být delší než kvóta virtuálních procesorů pro konkrétní rodiny velikost virtuálního počítače nebo kvóta celkový regionální virtuální procesory. Pokud některý z těchto kvót překročení nebudou povolena nasazení virtuálního počítače. Je také kvóty pro celkový počet virtuálních počítačů v oblasti. Informace o každé z těchto kvót si můžete prohlédnout ve **využití + kvóty** části **předplatné** stránky v [portál Azure](https://portal.azure.com), nebo můžete zadat dotaz pro hodnoty pomocí Prostředí PowerShell.

 
## <a name="check-usage"></a>Zkontrolujte využití

Můžete použít [Get-AzureRmVMUsage](/powershell/module/azurerm.compute/get-azurermvmusage) rutiny zkontrolovat využití vaší kvóty.

```azurepowershell-interactive
Get-AzureRmVMUsage -Location "East US"
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


## <a name="reserved-vm-instances"></a>Reserved VM Instances
Vyhrazená instance virtuálních počítačů, která jsou omezená na v rámci jednoho předplatného, bude přidán nový aspekt kvóty virtuální procesory. Tyto hodnoty popisují se počet instancí velikosti stanovené, který musí být nasadit v rámci předplatného. Tyto funkce fungují jako zástupný znak v systému kvóty a zajistit tak, že této kvóty je vyhrazena zajistit vyhrazenou instancí nasadit v rámci předplatného. Například pokud konkrétní předplatné má 10 Standard_D1 vyhrazená instance použití omezení pro vyhrazené instance Standard_D1 bude 10. To způsobí, že Azure a zkontrolujte, zda nejsou vždy alespoň 10 Vcpu k dispozici v celkový místní kvótu Vcpu má být použit pro Standard_D1 instancí a nejsou k dispozici v rámci kvóty virtuální procesor standardní rodiny D má být použit pro instance Standard_D1 minimálně 10 Vcpu.

Pokud je zvýšení kvóty je nutný k nákupu jeden RI předplatné, můžete [požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) vaše předplatné.

## <a name="next-steps"></a>Další postup

Další informace o fakturaci a kvóty najdete v tématu [předplatného Azure a omezení služby, kvóty a omezení](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
