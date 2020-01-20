---
title: Správa domén selhání ve službě Azure Virtual Machine Scale Sets
description: Naučte se, jak vybrat správný počet doménami selhání při vytváření sady škálování virtuálního počítače.
author: rajsqr
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: 297837354cea4bb5ccdcc03261810dcffd144243
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275725"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Volba správného počtu domén selhání pro sadu škálování virtuálního počítače
Služba Virtual Machine Scale Sets se ve výchozím nastavení vytváří s pěti doménami selhání v oblastech Azure bez zón. Pro oblasti, které podporují nasazení geografických sad virtuálních počítačů a tuto možnost, je výchozí hodnota počtu domén selhání 1 pro každou z těchto zón. FD = 1 v tomto případě předpokládá, že instance virtuálních počítačů patřící do sady škálování budou rozloženy mezi mnoho skříní na nejvyšší úrovni.

Můžete také zvážit zarovnání domén selhání sady škálování s počtem Managed Disks domén selhání. Toto zarovnání může zabránit ztrátě kvora v případě výpadku celé Managed Disks domény selhání. Počet položek FD může být nastaven na hodnotu menší nebo rovna počtu Managed Disks domén selhání dostupných v každé oblasti. V tomto [dokumentu](../virtual-machines/windows/manage-availability.md) najdete informace o počtu Managed disks domén selhání podle oblasti.

## <a name="rest-api"></a>Rozhraní REST API
Vlastnost lze nastavit `properties.platformFaultDomainCount` na hodnotu 1, 2 nebo 3 (výchozí hodnota je 5, pokud není zadána). Další [informace najdete v dokumentaci k REST API.](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)

## <a name="azure-cli"></a>Azure CLI
Parametr lze nastavit `--platform-fault-domain-count` na hodnotu 1, 2 nebo 3 (výchozí hodnota je 5, pokud není zadána). Informace najdete [tady](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)v dokumentaci k Azure CLI.

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
- Přečtěte si další informace o [funkcích dostupnosti a redundance](../virtual-machines/windows/availability.md) pro prostředí Azure.
