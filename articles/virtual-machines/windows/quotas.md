---
title: kvóty virtuálních procesorů pro Azure | Dokumentace Microsoftu
description: Další informace o kvóty virtuálních procesorů pro Azure.
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
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 92fee850b6ba013c759d3441219f1946a0faedb3
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984121"
---
# <a name="virtual-machine-vcpu-quotas"></a>Kvóty virtuálních procesorů virtuálního počítače

Kvóty virtuálních procesorů pro virtuální počítače a škálovací sady virtuálních počítačů jsou uspořádány ve dvou úrovních pro každé předplatné, v jednotlivých oblastech. První vrstva je celkový počet místních virtuálních procesorů a druhé vrstvy je různé velikost řady počet jader virtuálního počítače jako je například virtuální procesory řady D-series. Pokaždé, když je nový virtuální počítač nasazený virtuálních procesorů pro virtuální počítač nesmí překročit kvóty virtuálních procesorů pro řadu velikostí virtuálních počítačů nebo celkový počet virtuálních procesorů regionální kvóta. Pokud některý z těchto kvót je překročena, nasazení virtuálního počítače nebude možné. Je také kvóty pro celkový počet virtuálních počítačů v oblasti. Podrobnosti o každé z těchto kvót si můžete prohlédnout ve **využití a kvóty** část **předplatné** stránku [webu Azure portal](https://portal.azure.com), nebo můžete zadat dotaz na hodnoty pomocí Prostředí PowerShell.

 [!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)] 
 
## <a name="check-usage"></a>Kontrola využití

Můžete použít [Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) rutiny můžete zkontrolovat na využití kvóty.

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
Rezervované instance virtuálních počítačů, které mají rozsah v rámci jednoho předplatného bez flexibilitu velikost virtuálního počítače, bude přidán nový aspekt do kvóty virtuálních procesorů. Tyto hodnoty popisuje počet instancí je deklarovaná velikost, který musí být nasaditelný v rámci předplatného. Fungují jako zástupný symbol v kvóty systému a zajistit tak, že tato kvóta je vyhrazený pro Ujistěte se, že je možné nasadit v rámci předplatného rezervované instance virtuálních počítačů. Například pokud má 10 Standard_D1 rezervované instance Virtuálních limit využití pro konkrétní předplatné Standard_D1 rezervované instance virtuálních počítačů bude 10. To způsobí, že Azure a zajistěte, aby vždy alespoň 10 virtuálních procesorů ve nejsou k dispozici celkový počet překročení kvóty virtuálních procesorů se použije pro instance Standard_D1 a nejsou k dispozici v rámci kvóty virtuálních procesorů Standard, řada D se použije pro instance Standard_D1 minimálně 10 virtuálních procesorů.

Pokud k nákupu rezervované instance jednoho předplatného se vyžaduje zvýšení kvóty, můžete si [požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) v rámci předplatného.

## <a name="next-steps"></a>Další postup

Další informace o fakturaci a kvóty najdete v tématu [předplatného Azure a limity, kvóty a omezení](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
