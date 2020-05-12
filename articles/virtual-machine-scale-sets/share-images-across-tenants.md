---
title: Sdílení imagí Galerie napříč klienty v Azure
description: Naučte se sdílet image virtuálních počítačů napříč klienty Azure pomocí galerií sdílených imagí.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 5b86335ab8bcc3af75dbd8af39e3d913f2461e58
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119837"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Sdílení imagí virtuálních počítačů galerie v klientech Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Vytvoření škálovací sady s použitím Azure CLI

Přihlaste se k objektu služby pro tenanta 1 pomocí appID, klíče aplikace a ID tenanta 1. `az account show --query "tenantId"`V případě potřeby můžete použít k získání ID tenanta.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Přihlaste se k instančnímu objektu pro tenanta 2 pomocí appID, klíče aplikace a ID tenanta 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Vytvořte sadu škálování. Informace v příkladu nahraďte vlastními.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Další kroky

Pokud narazíte na nějaké problémy, můžete [řešit problémy s galerií sdílených imagí](troubleshooting-shared-images.md).
