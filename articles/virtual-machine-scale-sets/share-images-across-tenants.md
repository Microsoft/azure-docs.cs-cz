---
title: Sdílení imitací galerie mezi tenanty v Azure
description: Zjistěte, jak sdílet image virtuálních počítačí mezi klienty Azure pomocí sdílených galerií obrázků.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: a29999102ad8a10d8965145b31a7d804675e0e57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276345"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Sdílení ibi virtuálních počítačů galerie napříč tenanty Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Vytvoření škálovací sady s použitím Azure CLI

Přihlaste se k instančnímu objektu pro klienta 1 pomocí id aplikace, klíče aplikace a ID klienta 1. V případě `az account show --query "tenantId"` potřeby můžete získat ID klienta.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Přihlaste se k instančnímu objektu pro klienta 2 pomocí id aplikace, klíče aplikace a ID klienta 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Vytvořte škálovací sadu. Nahraďte informace v příkladu vlastními.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Další kroky

Pokud narazíte na nějaké problémy, můžete [řešit sdílené galerie obrázků](troubleshooting-shared-images.md).
