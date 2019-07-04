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
ms.openlocfilehash: 88596a4bda3c9ebae99403e95b3539f068cad665
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465477"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Sdílet mezi tenanty Azure Galerie imagí virtuálních počítačů

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Na portálu nelze použít k nasazení virtuálního počítače z image v jiném tenantovi azure. Vytvoření virtuálního počítače pomocí bitové kopie sdíleny mezi klienty, musíte použít rozhraní příkazového řádku Azure nebo [Powershellu](../windows/share-images-across-tenants.md).

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