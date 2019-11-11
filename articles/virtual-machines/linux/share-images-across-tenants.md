---
title: Sdílení imagí Galerie napříč klienty v Azure | Microsoft Docs
description: Naučte se sdílet image virtuálních počítačů napříč klienty Azure pomocí galerií sdílených imagí.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: b63bc1089b113edaac637df0a7e55c39f3a11f1a
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904981"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Sdílení imagí virtuálních počítačů galerie v klientech Azure

Galerie sdílených imagí vám umožňují sdílet Image pomocí RBAC. Ke sdílení imagí v rámci tenanta a dokonce i jednotlivcům mimo vašeho tenanta můžete použít RBAC. Další informace o této možnosti jednoduchého sdílení najdete v tématu [sdílení Galerie](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Portál nemůžete použít k nasazení virtuálního počítače z image v jiném tenantovi Azure. Pokud chcete vytvořit virtuální počítač z image sdílené mezi klienty, musíte použít Azure CLI nebo [PowerShell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Vytvoření virtuálního počítače pomocí Azure CLI

Přihlaste se k objektu služby pro tenanta 1 pomocí appID, klíče aplikace a ID tenanta 1. V případě potřeby můžete pomocí `az account show --query "tenantId"` získat ID tenanta.

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

Pokud narazíte na nějaké problémy, můžete [řešit problémy s galerií sdílených imagí](troubleshooting-shared-images.md).