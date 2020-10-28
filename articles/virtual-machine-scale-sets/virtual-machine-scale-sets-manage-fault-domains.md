---
title: Správa domén selhání ve službě Azure Virtual Machine Scale Sets
description: Naučte se, jak vybrat správný počet doménami selhání při vytváření sady škálování virtuálního počítače.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 12/18/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 4df340fa80ac4b9c4ff2a5e560580e09e01237e1
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745934"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Výběr správného počtu domén selhání pro škálovací sadu virtuálních počítačů
Služba Virtual Machine Scale Sets se ve výchozím nastavení vytváří s pěti doménami selhání v oblastech Azure bez zón. Pro oblasti, které podporují nasazení geografických sad virtuálních počítačů a tuto možnost, je výchozí hodnota počtu domén selhání 1 pro každou z těchto zón. FD = 1 v tomto případě předpokládá, že instance virtuálních počítačů patřící do sady škálování budou rozloženy mezi mnoho skříní na nejvyšší úrovni.

Můžete také zvážit zarovnání domén selhání sady škálování s počtem Managed Disks domén selhání. Toto zarovnání může zabránit ztrátě kvora v případě výpadku celé Managed Disks domény selhání. Počet položek FD může být nastaven na hodnotu menší nebo rovna počtu Managed Disks domén selhání dostupných v každé oblasti. V tomto [dokumentu](../virtual-machines/manage-availability.md) najdete informace o počtu Managed disks domén selhání podle oblasti.

## <a name="rest-api"></a>REST API
Vlastnost lze nastavit na hodnotu `properties.platformFaultDomainCount` 1, 2 nebo 3 (výchozí hodnota je 3, pokud není zadána). Další [informace najdete v dokumentaci k REST API.](/rest/api/compute/virtualmachinescalesets/createorupdate)

## <a name="azure-cli"></a>Azure CLI
Parametr lze nastavit na hodnotu `--platform-fault-domain-count` 1, 2 nebo 3 (výchozí hodnota je 3, pokud není zadána). Informace najdete [tady](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)v dokumentaci k Azure CLI.

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

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [funkcích dostupnosti a redundance](../virtual-machines/availability.md) pro prostředí Azure.
