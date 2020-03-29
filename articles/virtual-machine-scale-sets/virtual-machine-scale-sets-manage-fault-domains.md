---
title: Správa domén selhání ve škálovacích sadách virtuálních strojů Azure
description: Přečtěte si, jak vybrat správný počet fds při vytváření škálovací sady virtuálních strojů.
author: rajsqr
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: 297837354cea4bb5ccdcc03261810dcffd144243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275725"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Výběr správného počtu domén selhání pro škálovací sadu virtuálních počítačů
Škálovací sady virtuálních strojů se ve výchozím nastavení vytvářejí s pěti doménami selhání v oblastech Azure bez zón. Pro oblasti, které podporují zónové nasazení škálovacích sad virtuálních strojů a tato možnost je vybrána, výchozí hodnota počet domén selhání je 1 pro každou zónu. FD = 1 v tomto případě znamená, že instance virtuálních virtuálních stránek, které patří do škálovací sady bude rozložena do mnoha racků na základě nejlepší úsilí.

Můžete také zvážit zarovnání počtu domén selhání škálovací sady s počtem domén selhání spravovanédisky. Toto zarovnání může pomoci zabránit ztrátě kvora, pokud dojde k výpadku celé domény selhání spravovaného disku. Počet FD lze nastavit na méně nebo rovno počet domén selhání spravované disky k dispozici v každé z oblastí. Informace o počtu domén selhání spravovaných disků podle oblastí naleznete v tomto [dokumentu.](../virtual-machines/windows/manage-availability.md)

## <a name="rest-api"></a>REST API
Vlastnost můžete nastavit `properties.platformFaultDomainCount` na 1, 2 nebo 3 (výchozí hodnota 5, pokud není zadána). Naleznete v dokumentaci k rozhraní REST API [zde](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Azure CLI
Parametr můžete nastavit `--platform-fault-domain-count` na 1, 2 nebo 3 (výchozí hodnota 5, pokud není zadán). V dokumentaci k azure cli [najdete tady](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

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
- Přečtěte si další informace o [dostupnosti a redundanci funkcí](../virtual-machines/windows/availability.md) pro prostředí Azure.
