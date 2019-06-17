---
title: Image z Galerie sdílet mezi tenanty v Azure | Dokumentace Microsoftu
description: Zjistěte, jak sdílet napříč Azure tenanty používající sdílený Image Galerie imagí virtuálních počítačů.
services: virtual-machine-scale-sets
author: cynthn
manager: jeconnoc
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: cbaaac629fd013602eed75cc7dc357f13a62e3b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160122"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Sdílet mezi tenanty Azure Galerie imagí virtuálních počítačů

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Vytvoření škálovací sady pomocí Azure CLI

Přihlaste se instanční objekt služby pro tenanta 1 pomocí appID, klíče aplikace a ID tenanta 1. Můžete použít `az account show --query "tenantId"` k získání ID tenanta v případě potřeby.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Přihlaste se instanční objekt služby pro tenanta 2 pomocí appID, klíče aplikace a ID tenanta 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Vytvoření škálovací sady. Informace v tomto příkladu nahraďte vlastními.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Další postup

Pokud narazíte na nějaké problémy, můžete si [řešení potíží s galerií sdílené bitové kopie](troubleshooting-shared-images.md).