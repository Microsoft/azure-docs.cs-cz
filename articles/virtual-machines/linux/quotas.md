---
title: kvóty virtuálních procesorů pro Azure | Dokumentace Microsoftu
description: Další informace o kvóty virtuálních procesorů pro Azure.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 56ee63e15c429c5a6212be36d420ae59afa48546
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629085"
---
# <a name="virtual-machine-vcpu-quotas"></a>Kvóty virtuálních procesorů virtuálního počítače

Kvóty virtuálních procesorů pro virtuální počítače a škálovací sady virtuálních počítačů jsou uspořádány ve dvou úrovních pro každé předplatné, v jednotlivých oblastech. První vrstva je celkový počet místních virtuálních procesorů a druhé vrstvy je různé velikost řady počet jader virtuálního počítače jako je například virtuální procesory řady D-series. Pokaždé, když je nový virtuální počítač nasazený virtuálních procesorů pro virtuální počítač nesmí překročit kvóty virtuálních procesorů pro řadu velikostí virtuálních počítačů nebo celkový počet virtuálních procesorů regionální kvóta. Pokud některý z těchto kvót je překročena, nasazení virtuálního počítače nebude možné. Je také kvóty pro celkový počet virtuálních počítačů v oblasti. Podrobnosti o každé z těchto kvót si můžete prohlédnout ve **využití a kvóty** část **předplatné** stránku [webu Azure portal](https://portal.azure.com), nebo můžete zadat dotaz na hodnoty pomocí Azure ROZHRANÍ PŘÍKAZOVÉHO ŘÁDKU.


## <a name="check-usage"></a>Kontrola využití

Můžete zkontrolovat pomocí využití kvóty [az vm-používání seznamu](/cli/azure/vm#az_vm_list_usage).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

Výstup by měl vypadat přibližně takto:


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Reserved VM Instances
Rezervované instance virtuálních počítačů, které mají rozsah pro jedno předplatné, bude přidán nový aspekt do kvóty virtuálních procesorů. Tyto hodnoty popisuje počet instancí je deklarovaná velikost, který musí být nasaditelný v rámci předplatného. Fungují jako zástupný symbol v kvóta systému a zajistit tak, že tato kvóta je vyhrazený pro Ujistěte se, že je možné nasadit v rámci předplatného Azure rezervace. Například pokud konkrétní předplatné má 10 rezervace Standard_D1 maximálního použití Standard_D1 rezervace bude 10. To způsobí, že Azure a zajistěte, aby vždy alespoň 10 virtuálních procesorů ve nejsou k dispozici celkový počet překročení kvóty virtuálních procesorů se použije pro instance Standard_D1 a nejsou k dispozici v rámci kvóty virtuálních procesorů Standard, řada D se použije pro instance Standard_D1 minimálně 10 virtuálních procesorů.

Pokud je potřeba koupit rezervované instance jednoho předplatného zvýšení kvóty, můžete si [požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) v rámci předplatného.

## <a name="next-steps"></a>Další postup

Další informace o fakturaci a kvóty najdete v tématu [předplatného Azure a limity, kvóty a omezení](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
