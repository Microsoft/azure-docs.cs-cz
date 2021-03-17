---
title: Vytvoření virtuálního počítače se statickou privátní IP adresou – Azure PowerShell
description: Naučte se, jak vytvořit virtuální počítač s privátní IP adresou pomocí PowerShellu.
services: virtual-network
documentationcenter: na
author: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 43c2e2c8905ca60e59caed3fdf1b4920f509fd88
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98783780"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Vytvoření virtuálního počítače se statickou privátní IP adresou pomocí PowerShellu

Virtuální počítač (VM) můžete vytvořit se statickou privátní IP adresou. Pokud chcete vybrat, která adresa z podsítě je přiřazená k virtuálnímu počítači, přiřaďte statickou privátní IP adresu, nikoli dynamickou adresu. Přečtěte si další informace o [statických privátních IP adresách](./public-ip-addresses.md#allocation-method). Chcete-li změnit soukromou IP adresu přiřazenou existujícímu virtuálnímu počítači z dynamického na statickou nebo pro práci s veřejnými IP adresami, přečtěte si téma [Přidání, změna nebo odebrání IP adres](virtual-network-network-interface-addresses.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Následující kroky můžete provést z místního počítače nebo pomocí Azure Cloud Shell. Pokud chcete použít místní počítač, ujistěte se, že máte [nainstalovanou Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud chcete použít Azure Cloud Shell, vyberte **vyzkoušet** v pravém horním rohu libovolného příkazového řádku, který následuje. Cloud Shell vás přihlásí k Azure.

1. Pokud používáte Cloud Shell, přejděte ke kroku 2. Otevřete relaci příkazu a přihlaste se k Azure pomocí `Connect-AzAccount` .
2. Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Následující příklad vytvoří skupinu prostředků v Východní USA oblasti Azure:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Vytvořte konfiguraci podsítě a virtuální síť s příkazy [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) :

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Vytvořte ve virtuální síti síťové rozhraní a přiřaďte privátní IP adresu z podsítě k síťovému rozhraní pomocí příkazů [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) :

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. Vytvořte konfiguraci virtuálního počítače pomocí [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig)a pak vytvořte virtuální počítač pomocí [New-AzVM](/powershell/module/az.Compute/New-azVM). Po zobrazení výzvy zadejte uživatelské jméno a heslo, které se použije jako přihlašovací údaje pro virtuální počítač:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> I když můžete do operačního systému přidat nastavení privátních IP adres, doporučujeme, abyste to neučinili, dokud nepřečtete [do operačního systému možnost Přidat privátní IP adresu](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> Pokud chcete získat přístup k virtuálnímu počítači z Internetu, musíte virtuálnímu počítači přiřadit veřejnou IP adresu. Můžete také změnit dynamické přiřazení privátní IP adresy na statické přiřazení. Podrobnosti najdete v tématu [Přidání nebo změna IP adres](virtual-network-network-interface-addresses.md). Kromě toho se doporučuje omezit síťový provoz na virtuální počítač tím, že přidružíte skupinu zabezpečení sítě k síťovému rozhraní, podsíti, ve které jste síťové rozhraní vytvořili, nebo obojí. Podrobnosti najdete v tématu [Správa skupin zabezpečení sítě](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít [příkaz Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [privátních IP adresách](./private-ip-addresses.md) a přiřazení [statických privátních IP adres](virtual-network-network-interface-addresses.md#add-ip-addresses) k virtuálnímu počítači Azure.
- Přečtěte si další informace o vytváření virtuálních počítačů se systémy [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .
