---
title: Image z Galerie sdílet mezi tenanty v Azure | Dokumentace Microsoftu
description: Zjistěte, jak sdílet napříč Azure tenanty používající sdílený Image Galerie imagí virtuálních počítačů.
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 56c0e906203a010a1356beae4b6e5d66e34cfc2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159837"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Sdílet mezi tenanty Azure Galerie imagí virtuálních počítačů

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-vm-using-powershell"></a>Vytvoření virtuálního počítače pomocí Powershellu


Přihlásit se do obou tenanty s použitím ID aplikace ID tajný klíč a tenanta. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

Vytvoření virtuálního počítače ve skupině prostředků, který má oprávnění pro registraci aplikace. Informace v tomto příkladu nahraďte vlastními.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $image `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

## <a name="next-steps"></a>Další postup

Můžete také vytvořit sdílené bitové kopie pomocí prostředků Galerie [webu Azure portal](shared-images-portal.md).