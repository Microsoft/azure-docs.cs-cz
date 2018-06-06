---
title: virtuální procesory kvóty pro Azure | Microsoft Docs
description: Další informace o virtuálních procesorů kvóty pro Azure.
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
ms.openlocfilehash: a880ee18bb13b2cd8471cc58157469555397b872
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716512"
---
# <a name="virtual-machine-vcpu-quotas"></a>Virtuální počítač virtuální procesor kvóty

Virtuální procesory kvóty pro virtuální počítače a sady škálování virtuálního počítače jsou uspořádány do dvou vrstev pro každé předplatné, v každé oblasti. První vrstva je celkový počet regionální Vcpu a druhé vrstvy je různých virtuálních počítačů velikost rodiny jádrech například Vcpu D-series. Při každém vytvoření nového virtuálního počítače je nasadit Vcpu pro virtuální počítač nesmí být delší než kvóta virtuálních procesorů pro rodinu velikost virtuálního počítače nebo kvóta celkový regionální virtuální procesory. Pokud některý z těchto kvót překročení nebudou povolena, nasazení virtuálního počítače. Je také kvóty pro celkový počet virtuálních počítačů v oblasti. Informace o každé z těchto kvót si můžete prohlédnout ve **využití + kvóty** části **předplatné** stránky v [portál Azure](https://portal.azure.com), nebo můžete zadat dotaz pro hodnoty pomocí Azure ROZHRANÍ PŘÍKAZOVÉHO ŘÁDKU.


## <a name="check-usage"></a>Zkontrolujte využití

Můžete zkontrolovat pomocí kvóty využití [seznam využití virtuálních počítačů az](/cli/azure/vm#az_vm_list_usage).

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
Vyhrazená instance virtuálních počítačů, která jsou omezená na v rámci jednoho předplatného, bude přidán nový aspekt kvóty virtuální procesory. Tyto hodnoty popisují se počet instancí velikosti stanovené, který musí být nasadit v rámci předplatného. Tyto funkce fungují jako zástupný znak v systému kvóty a zajistit tak, že této kvóty je vyhrazena zajistit vyhrazenou instancí nasadit v rámci předplatného. Například pokud konkrétní předplatné má 10 Standard_D1 vyhrazená instance použití omezení pro vyhrazené instance Standard_D1 bude 10. To způsobí, že Azure a zkontrolujte, zda nejsou vždy alespoň 10 Vcpu k dispozici v celkový místní kvótu Vcpu má být použit pro Standard_D1 instancí a nejsou k dispozici v rámci kvóty virtuální procesor standardní rodiny D má být použit pro instance Standard_D1 minimálně 10 Vcpu.

Pokud zvýšení kvóty je potřeba buď koupit jeden RI předplatné, můžete [požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) vaše předplatné.

## <a name="next-steps"></a>Další postup

Další informace o fakturaci a kvóty najdete v tématu [předplatného Azure a omezení služby, kvóty a omezení](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
