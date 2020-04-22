---
title: Kvóty virtuálních procesorů
description: Přečtěte si o kvótách virtuálních procesorů pro Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 4f8d181194328725c2e7c02f6e73752cf568ef4e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759181"
---
# <a name="virtual-machine-vcpu-quotas"></a>Kvóty virtuálních procesorů pro virtuální počítače

Kvóty virtuálních procesorů pro virtuální počítače a škálovací sady virtuálních počítačů jsou uspořádány do dvou úrovní pro každé předplatné v každé oblasti. První vrstva je celková regionální virtuální procesory a druhá vrstva je různé jádra rodiny velikosti virtuálních počítače, jako jsou virtuální procesory řady D. Pokaždé, když se nasadí nový virtuální virtuální procesor, nesmí virtuální procesory pro virtuální počítače překročit kvótu virtuálního procesoru pro rodinu velikosti virtuálního počítače nebo celkovou kvótu místního virtuálního procesoru. Pokud je překročena některá z těchto kvót, nasazení virtuálního počítače nebude povoleno. K dispozici je také kvóta pro celkový počet virtuálních počítačů v oblasti. Podrobnosti o každé z těchto kvót najdete v části **Využití + kvóty** na stránce **Předplatného** na webu [Azure Portal](https://portal.azure.com)nebo můžete dotazovat na hodnoty pomocí příkazového příkazu k příkazu k onomu Azure.


## <a name="check-usage"></a>Kontrola využití

Využití kvótmůžete i pomocí [využití seznamu vz vm](/cli/azure/vm).

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

## <a name="reserved-vm-instances"></a>Rezervované instance virtuálních počítačů
Rezervované instance virtuálních počítače, které jsou vymezeny na jedno předplatné bez flexibility velikosti virtuálního počítače, přidá nový aspekt kvót virtuálních procesorů. Tyto hodnoty popisují počet instancí uvedené velikosti, které musí být nasaditelné v předplatném. Pracují jako zástupný symbol v systému kvót, aby bylo zajištěno, že kvóta je vyhrazena k zajištění rezervace Azure jsou nasaditelné v předplatném. Například pokud konkrétní předplatné má 10 Standard_D1 rezervace, omezení využití pro rezervace Standard_D1 bude 10. To způsobí, že Azure zajistí, že v kvótě virtuálních procesorů Total Regional, která se má použít pro Standard_D1 instance, bude vždy k dispozici alespoň 10 virtuálních procesorů a že v kvótě virtuálních procesorů řady Standard D bude k dispozici alespoň 10 virtuálních procesorů, které se mají použít pro Standard_D1 instance.

Pokud je ke zakoupení ri jednoho předplatného vyžadováno zvýšení kvóty, můžete [požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) u předplatného.

## <a name="next-steps"></a>Další kroky

Další informace o fakturaci a kvótách najdete v [tématu Limity předplatného a služeb Azure, kvóty a omezení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
