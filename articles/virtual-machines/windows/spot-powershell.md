---
title: Použití PowerShellu k nasazení Virtual Machines na místě Azure
description: Naučte se používat Azure PowerShell k nasazení Virtual Machines na místě Azure, abyste ušetřili náklady.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: b7a4dc65a3f9285c27b4bf34f65244c37aaf850c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672143"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Nasazení služby Azure spot Virtual Machines pomocí Azure PowerShell


Použití [Azure Spot Virtual Machines](../spot-vms.md) umožňuje využít výhod naší nevyužité kapacity s významnou úsporou nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure místo Virtual Machines Azure. Proto jsou Azure spot Virtual Machines Skvělé pro úlohy, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, velké výpočetní úlohy a další.

Ceny za Azure na místě Virtual Machines jsou proměnné na základě oblastí a SKU. Další informace najdete v tématu ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Další informace o nastavení maximální ceny najdete v tématu [Azure Spot Virtual Machines – ceny](../spot-vms.md#pricing).

Máte možnost nastavit maximální cenu, kterou jste ochotni zaplatit za hodinu pro virtuální počítač. Maximální cena pro virtuální počítač se službou Azure se dá nastavit v amerických dolarech (USD), a to s využitím až 5 desetinných míst. Hodnota by měla být například `0.98765` maximální cena $0,98765 USD za hodinu. Pokud nastavíte maximální cenu `-1` , nebude se virtuální počítač vyřadit podle ceny. Cena za virtuální počítač bude aktuální cena za bod nebo cena za standardní virtuální počítač, který je stále menší, pokud je dostupná kapacita a kvóta.


## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Vytvořte spotVM pomocí [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) pro vytvoření konfigurace. Zahrňte `-Priority Spot` a nastavte `-MaxPrice` buď na:
- `-1` virtuální počítač proto nebude vyřazení na základě ceny.
- hodnota dolaru, maximálně 5 číslic. Například `-MaxPrice .98765` znamená, že se virtuální počítač bude uvolnit, jakmile cena za spotVM bude přibližně o 98765 za hodinu.


Tento příklad vytvoří spotVM, který se nevrátí na základě cen (jenom v případě, že Azure potřebuje kapacitu zpátky). Zásada vyřazení je nastavená tak, aby se virtuální počítač nastavil jako neplatný, aby se mohl později restartovat. Pokud chcete odstranit virtuální počítač a příslušný disk, když je virtuální počítač vyřazený, nastavte `-EvictionPolicy` na `Delete` v `New-AzVMConfig` .


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Po vytvoření virtuálního počítače se můžete dotazovat, aby se zobrazila maximální cena za všechny virtuální počítače ve skupině prostředků.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>Simulace vyřazení

Můžete [simulovat vyřazení](/rest/api/compute/virtualmachines/simulateeviction) virtuálních počítačů se systémem Azure na místě, abyste mohli otestovat, jak dobře bude vaše aplikace schopná vyřadit do náhlého vyřazení. 

Pro vaše informace nahraďte následující údaje: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>Další kroky

Virtuální počítač se službou Azure se dá vytvořit také pomocí [Azure CLI](../linux/spot-cli.md), [portálu](../spot-portal.md) nebo [šablony](../linux/spot-template.md).

Dotaz na aktuální informace o cenách pomocí [API maloobchodních cen Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) najdete v informacích o cenách virtuálních počítačů Azure na místě. `meterName`A `skuName` bude obsahovat oba `Spot` .

Pokud dojde k chybě, přečtěte si [kódy chyb](../error-codes-spot.md).