---
title: Image z Galerie sdílet mezi tenanty v Azure | Dokumentace Microsoftu
description: Zjistěte, jak sdílet napříč Azure tenanty používající sdílený Image Galerie imagí virtuálních počítačů.
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 1578ba840c6dca93feb68754863439811d7ef099
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158727"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Sdílet mezi tenanty Azure Galerie imagí virtuálních počítačů

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-vm-using-azure-cli"></a>Vytvoření virtuálního počítače pomocí Azure CLI

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

Vytvoření virtuálního počítače. Informace v tomto příkladu nahraďte vlastními.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Další postup

Pokud narazíte na nějaké problémy, můžete si [řešení potíží s galerií sdílené bitové kopie](troubleshooting-shared-images.md).