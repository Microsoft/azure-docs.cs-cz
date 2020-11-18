---
title: Sdílení imagí Galerie mezi klienty
description: Naučte se sdílet image virtuálních počítačů napříč klienty Azure pomocí galerií sdílených imagí pomocí ukázek pro Linux.
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: a5e71f09179d414be84896a49a66480f19a0665d
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843583"
---
# <a name="share-gallery-vm-images-across-azure-tenants---linux-examples"></a>Sdílení imagí virtuálních počítačů galerie v klientech Azure – příklady pro Linux

Galerie sdílených imagí vám umožní sdílet Image pomocí Azure RBAC. Pomocí služby Azure RBAC můžete sdílet image v rámci vašeho tenanta, a to i u jednotlivců mimo vašeho tenanta. Další informace o této možnosti jednoduchého sdílení najdete v tématu [sdílení Galerie](./shared-images-portal.md#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Portál nemůžete použít k nasazení virtuálního počítače z image v jiném tenantovi Azure. Pokud chcete vytvořit virtuální počítač z image sdílené mezi klienty, musíte použít Azure CLI nebo [PowerShell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Vytvoření virtuálního počítače pomocí Azure CLI

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

Vytvořte virtuální počítač. Informace v příkladu nahraďte vlastními.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Další kroky

Pokud narazíte na nějaké problémy, můžete [řešit problémy s galerií sdílených imagí](../troubleshooting-shared-images.md).
