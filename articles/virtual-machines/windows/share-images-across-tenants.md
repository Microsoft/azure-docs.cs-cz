---
title: Sdílení imagí Galerie napříč klienty v Azure
description: Naučte se sdílet image virtuálních počítačů napříč klienty Azure pomocí galerií sdílených imagí a PowerShellu.
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/15/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 84998182081c6a681668fbd9bc05446357beb959
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96781041"
---
# <a name="share-gallery-vm-images-across-azure-tenants-using-powershell"></a>Sdílení imagí virtuálních počítačů galerie v rámci tenantů Azure pomocí PowerShellu

Galerie sdílených imagí vám umožní sdílet Image pomocí Azure RBAC. Pomocí služby Azure RBAC můžete sdílet image v rámci vašeho tenanta, a to i u jednotlivců mimo vašeho tenanta. Další informace o této možnosti jednoduchého sdílení najdete v tématu [sdílení Galerie](./shared-images-portal.md#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> Portál nemůžete použít k nasazení virtuálního počítače z image v jiném tenantovi Azure. Pokud chcete vytvořit virtuální počítač z image sdílené mezi klienty, musíte použít [Azure CLI](../linux/share-images-across-tenants.md) nebo PowerShell.

## <a name="create-a-vm-using-powershell"></a>Vytvoření virtuálního počítače s využitím PowerShellu

Přihlaste se do obou tenantů pomocí ID aplikace, tajného klíče a ID tenanta. 

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

Vytvořte virtuální počítač ve skupině prostředků, která má oprávnění k registraci aplikace. Informace v tomto příkladu nahraďte vlastními.



```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Set a variable for the image version in Tenant 1 using the full image ID of the shared image version
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Networking pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using the $image variable to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $image | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="next-steps"></a>Další kroky

Pomocí [Azure Portal](shared-images-portal.md)můžete také vytvořit prostředky galerie sdílených imagí.
