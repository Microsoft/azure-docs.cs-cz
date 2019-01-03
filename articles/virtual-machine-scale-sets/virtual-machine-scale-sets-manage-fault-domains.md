---
title: Správa domén selhání ve škálovacích sadách virtuálních počítačů Azure | Dokumentace Microsoftu
description: Zjistěte, jak zvolit správný počet doménami selhání při vytvoření virtuálního počítače škálovací sady.
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: drewm
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/18
ms.author: rajraj
ms.openlocfilehash: 3b5568e0eed06684667b8aeefa389b6d0aab2a5f
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53636164"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Výběr správný počet domén selhání pro škálovací sadu virtuálních počítačů
Škálovací sady virtuálních počítačů vytvářejí s pěti doménami selhání ve výchozím nastavení v oblastech Azure, bez zón. Pro oblasti, které podporují oblastmi nasazení škálovací sady virtuálních počítačů je počet domén selhání na výchozí hodnotu 1 pro každý ze zón. FD = 1 v tomto případě znamená, že instance virtuálních počítačů, které patří ke škálovací sadě bude možné rozdělit do mnoha stojany na jak kapacita systému dovolí.

Můžete také zvážit zarovnání počet domén selhání škálovací sady pomocí počet domén selhání spravovaných disků. Toto uspořádání může zabránit ztrátě kvora, pokud celé doméně selhání spravovaných disků ocitne mimo provoz. Počet FD můžete nastavit na hodnotu menší než nebo rovna počtu Managed Disks domén selhání v každé oblasti k dispozici. Projít tento [dokumentu](../virtual-machines/windows/manage-availability.md) Další informace o počtu domén selhání spravovaných disků podle oblasti.

## <a name="rest-api"></a>REST API
Můžete nastavit vlastnost `properties.platformFaultDomainCount` 1, 2 nebo 3 (výchozí je 5, není-li zadán). Přečtěte si dokumentaci k rozhraní REST API [tady](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Azure CLI
Můžete nastavit parametr `--platform-fault-domain-count` 1, 2 nebo 3 (výchozí je 5, není-li zadán). Naleznete v dokumentaci pro Azure CLI [tady](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.

## <a name="next-steps"></a>Další postup
- Další informace o [funkcí redundance a dostupnosti](../virtual-machines/windows/regions-and-availability.md) pro prostředí Azure.
