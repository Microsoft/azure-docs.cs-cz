---
title: Kvóty virtuálních procesorů
description: Přečtěte si o kvótách vCPU pro Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 028da1fa9d2b16b4d87c345d8435c99b565e1774
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88949690"
---
# <a name="check-vcpu-quotas-using-the-azure-cli"></a>Ověření vCPU kvót pomocí Azure CLI

Kvóty vCPU pro virtuální počítače a sady škálování virtuálních počítačů jsou seřazené do dvou vrstev pro každé předplatné v každé oblasti. První z nich je celková oblast vCPU a druhá úroveň je různými jádry velikosti virtuálních počítačů, jako je vCPU řady D-Series. Kdykoliv je nasazený nový virtuální počítač, vCPU pro virtuální počítač nesmí překročit kvótu vCPU pro rodinu velikosti virtuálního počítače nebo celkovou kvótu vCPU v oblasti regionálního provozu. Pokud dojde k překročení některé z těchto kvót, nasazení virtuálního počítače se nepovolí. K dispozici je také kvóta pro celkový počet virtuálních počítačů v dané oblasti. Podrobnosti o každé z těchto kvót najdete v části **využití a kvóty** stránky **předplatné** v [Azure Portal](https://portal.azure.com), nebo můžete zadat dotaz na hodnoty pomocí Azure CLI.

> [!NOTE]
> Kvóta se počítá na základě celkového počtu jader používaných i přidělených. Pokud potřebujete další jádra, [požádejte o zvýšení kvóty](../../azure-portal/supportability/resource-manager-core-quotas-request.md) nebo odstraňte virtuální počítače, které už nepotřebujete. 


## <a name="check-usage"></a>Kontrola využití

Využití kvóty můžete ověřit pomocí [AZ VM list-Usage](/cli/azure/vm).

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
Pro rezervované instance virtuálních počítačů, které jsou vymezené na jedno předplatné bez flexibilní velikosti virtuálních počítačů, se do kvót vCPU přidá nový aspekt. Tyto hodnoty popisují počet instancí uvedené velikosti, které je nutné nasadit v předplatném. V systému kvót fungují jako zástupný symbol, aby bylo zajištěno, že je kvóta vyhrazena pro zajištění nasazení rezervací Azure v rámci předplatného. Pokud má například konkrétní předplatné 10 Standard_D1 rezervací, omezení využití pro Standard_D1 rezervace budou 10. Tím dojde k tomu, že Azure zajistí, že bude k dispozici vždy aspoň 10 vCPU v celkové místní kvótě vCPU, která se má použít pro instance Standard_D1 a k dispozici je aspoň 10 vCPU v úrovni Standard řady D Family vCPU, která se bude používat pro Standard_D1 instance.

Pokud se vyžaduje navýšení kvóty k nákupu jediného předplatného na rezervovaném počítači, můžete [požádat o zvýšení kvóty](../../azure-portal/supportability/resource-manager-core-quotas-request.md) u svého předplatného.

## <a name="next-steps"></a>Další kroky

Další informace o fakturaci a kvótách najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=/azure/billing/TOC.json).
