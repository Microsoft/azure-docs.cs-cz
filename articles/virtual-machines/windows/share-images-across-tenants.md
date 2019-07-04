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
ms.openlocfilehash: 5b3c4e5380c65b2ab6c736e7fabe1813fe32afc2
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466487"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Sdílet mezi tenanty Azure Galerie imagí virtuálních počítačů

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> Na portálu nelze použít k nasazení virtuálního počítače z image v jiném tenantovi azure. Vytvoření virtuálního počítače pomocí bitové kopie sdíleny mezi klienty, je nutné použít [rozhraní příkazového řádku Azure](../linux/share-images-across-tenants.md) nebo prostředí Powershell.

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