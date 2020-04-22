---
title: Sdílení imitací galerie mezi tenanty v Azure
description: Zjistěte, jak sdílet image virtuálních počítačí mezi klienty Azure pomocí sdílených galerií obrázků.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 4259ca01dbe45463b73cf1ec1c620c3921ab9459
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758462"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Sdílení ibi virtuálních počítačů galerie napříč tenanty Azure

Sdílené galerie obrázků umožňují sdílet obrázky pomocí RBAC. RBAC můžete použít ke sdílení bitových kopií v rámci vašeho tenanta, a dokonce i pro jednotlivce mimo vašeho tenanta. Další informace o této jednoduché možnosti sdílení naleznete v galerii [Sdílení](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Portál nelze použít k nasazení virtuálního počítače z image v jiném tenantovi Azure. Chcete-li vytvořit virtuální počítač z image sdílené mezi klienty, musíte použít Azure CLI nebo [Powershell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Vytvoření virtuálního počítače pomocí azure cli

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

Vytvořte virtuální hod. Nahraďte informace v příkladu vlastními.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Další kroky

Pokud narazíte na nějaké problémy, můžete [řešit sdílené galerie obrázků](troubleshooting-shared-images.md).