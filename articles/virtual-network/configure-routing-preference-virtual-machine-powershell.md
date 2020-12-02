---
title: Konfigurace předvolby směrování pro virtuální počítač Azure PowerShell
description: Přečtěte si, jak vytvořit virtuální počítač s veřejnou IP adresou s volbou předvolby směrování pomocí Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: mnayak
ms.openlocfilehash: 65f5b27ecc7b526c4b3e4f5ed62ac90149df1ce1
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510603"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-powershell"></a>Konfigurace předvolby směrování pro virtuální počítač pomocí Azure PowerShell

V tomto článku se dozvíte, jak nakonfigurovat předvolby směrování pro virtuální počítač. Internetový provoz z virtuálního počítače bude směrován přes síť poskytovatele internetových služeb, pokud zvolíte možnost **Internet** jako předvolby směrování. Výchozí směrování je prostřednictvím globální sítě Microsoftu.

V tomto článku se dozvíte, jak vytvořit virtuální počítač s veřejnou IP adresou, která je nastavená na směrování provozu přes síť poskytovatele internetových služeb pomocí Azure PowerShell.

> [!IMPORTANT]
> Předvolby směrování jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
1. Pokud používáte Cloud Shell, přejděte ke kroku 2. Otevřete relaci příkazu a přihlaste se k Azure pomocí `Connect-AzAccount` .
2. Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Následující příklad vytvoří skupinu prostředků v Východní USA oblasti Azure:

    ```azurepowershell
    $rg = New-AzResourceGroup -Name MyResourceGroup -Location EastUS
    ```

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Pokud chcete získat přístup k virtuálním počítačům z Internetu, budete potřebovat veřejné IP adresy. Vytvořte veřejné IP adresy pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Následující příklad vytvoří veřejnou IP adresu IPv4 s názvem *MyPublicIP* typu předvolby směrování na *Internetu* ve skupině prostředků *MyResourceGroup* v *východní USA* oblasti:

```azurepowershell-interactive
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Než nasadíte virtuální počítač, musíte vytvořit podpůrné síťové prostředky – skupinu zabezpečení sítě, virtuální síť a virtuální síťovou kartu.

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). Následující příklad vytvoří NSG s názvem *myNSG*

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myNSG"
```

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVNET* s *mySubNet*:

### <a name="create-a-subnet"></a>Vytvoření podsítě

```azurepowershell
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "mySubnet" `
-AddressPrefix "10.0.0.0/24"
```

```azurepowershell
# Create a virtual network
$vnet = New-AzVirtualNetwork `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myVNET" `
-AddressPrefix "10.0.0.0/16" `
-Subnet $subnet
```

### <a name="create-a-nic"></a>Vytvoření síťové karty

Vytvoření virtuálních síťových karet pomocí [New-AzNetworkInterface] (/PowerShell/Module/AZ.Network/New-aznetworkinterface. Následující příklad vytvoří virtuální síťovou kartu.

```azurepowershell
# Create an IP Config
$ipconfig=New-AzNetworkInterfaceIpConfig `
-Name myIpConfig `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-PublicIpAddress  $publicIp

# Create a NIC
$nic = New-AzNetworkInterface `
-Name "mynic" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $ipconfig 
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálních počítačů:

```azurepowershell
 $cred = get-credential -Message "Routing Preference SAMPLE:  Please enter the Administrator credential to log into the VM."
```

Nyní můžete vytvořit virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří dva virtuální počítače a požadované součásti virtuální sítě, pokud ještě neexistují.

```azurepowershell
 $vmsize = "Standard_A2"
 $ImagePublisher = "MicrosoftWindowsServer"
 $imageOffer = "WindowsServer"
 $imageSKU = "2019-Datacenter"

 $vmName= "myVM"
 $vmconfig = New-AzVMConfig -VMName $vmName -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption "FromImage" | Add-AzVMNetworkInterface -Id $nic.Id 
 $VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $vmconfig
```

## <a name="allow-network-traffic-to-the-vm"></a>Povolení síťového provozu virtuálního počítače

Než se budete moct připojit k veřejné IP adrese z Internetu, ujistěte se, že máte potřebné porty otevřené v libovolné skupině zabezpečení sítě, kterou jste mohli přidružit k síťovému rozhraní, podsíti, ve které je síťové rozhraní, nebo obojí. Platná pravidla zabezpečení pro síťové rozhraní a jeho podsíť můžete zobrazit pomocí [portálu](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), rozhraní příkazového [řádku](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)nebo [PowerShellu](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

 ```azurepowershell
 Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [předvolbách směrování ve veřejných IP adresách](routing-preference-overview.md).
* Přečtěte si další informace o [veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure.
* Přečtěte si další informace o [nastavení veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address).
