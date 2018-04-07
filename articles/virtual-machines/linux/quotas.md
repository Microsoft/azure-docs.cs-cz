---
title: virtuální procesory kvóty pro Azure | Microsoft Docs
description: Další informace o virtuálních procesorů kvóty pro Azure.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: a4e0bbe1c6d9b121dfb422934cdd67ff19f80482
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="virtual-machine-vcpu-quotas"></a>Virtuální počítač virtuální procesor kvóty

Virtuální procesory kvóty pro virtuální počítače a sady škálování virtuálního počítače jsou uspořádány do dvou vrstev pro každé předplatné, v každé oblasti. První vrstva je celkový počet regionální Vcpu a druhé vrstvy je různých virtuálních počítačů velikost rodiny jádrech například Vcpu standardní D rodiny. Při každém vytvoření nového virtuálního počítače je nasadit Vcpu pro nově nasazené virtuální počítač nesmí být delší než kvóta virtuálních procesorů pro konkrétní rodiny velikost virtuálního počítače nebo kvóta celkový regionální virtuální procesory. Pokud některý z těchto kvót překročení nebudou povolena nasazení virtuálního počítače. Je také kvóty pro celkový počet virtuálních počítačů v oblasti. Informace o každé z těchto kvót si můžete prohlédnout ve **využití + kvóty** části **předplatné** stránky v [portál Azure](https://portal.azure.com), nebo můžete dotazovat hodnotami pomocí rozhraní příkazového řádku Azure .


## <a name="check-usage"></a>Zkontrolujte využití

Můžete zkontrolovat pomocí kvóty využití [seznam využití virtuálních počítačů az](/cli/azure/vm#az_vm_list_usage).

```azurecli-interactive
az vm list-usage --location "East US"
[
  …
  {
    "currentValue": 4,
    "limit": 260,
    "name": {
      "localizedValue": "Total Regional vCPUs",
      "value": "cores"
    }
  },
  {
    "currentValue": 4,
    "limit": 10000,
    "name": {
      "localizedValue": "Virtual Machines",
      "value": "virtualMachines"
    }
  },
  {
    "currentValue": 1,
    "limit": 2000,
    "name": {
      "localizedValue": "Virtual Machine Scale Sets",
      "value": "virtualMachineScaleSets"
    }
  },
  {
    "currentValue": 1,
    "limit": 10,
    "name": {
      "localizedValue": "Standard B Family vCPUs",
      "value": "standardBFamily"
    }
  },
```
## <a name="reserved-vm-instances"></a>Reserved VM Instances
Vyhrazená instance virtuálních počítačů, která jsou omezená na v rámci jednoho předplatného, bude přidán nový aspekt kvóty virtuální procesory. Tyto hodnoty popisují se počet instancí velikosti stanovené, který musí být nasadit v rámci předplatného. Tyto funkce fungují jako zástupný znak v systému kvóty a zajistit tak, že této kvóty je vyhrazena zajistit vyhrazenou instancí nasadit v rámci předplatného. Například pokud konkrétní předplatné má 10 Standard_D1 vyhrazená instance použití omezení pro vyhrazené instance Standard_D1 bude 10. To způsobí, že Azure a zkontrolujte, zda nejsou vždy alespoň 10 Vcpu k dispozici v celkový místní kvótu Vcpu má být použit pro Standard_D1 instancí a nejsou k dispozici v rámci kvóty virtuální procesor standardní rodiny D má být použit pro instance Standard_D1 minimálně 10 Vcpu.

Pokud zvýšení kvóty je potřeba buď koupit jeden RI předplatné, můžete [požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) vaše předplatné.

## <a name="next-steps"></a>Další postup

Další informace o fakturaci a kvóty najdete v tématu [předplatného Azure a omezení služby, kvóty a omezení](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
