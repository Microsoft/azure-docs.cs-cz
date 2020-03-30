---
title: Vytvoření virtuálního počítače se statickou privátní IP adresou – Azure PowerShell
description: Zjistěte, jak vytvořit virtuální počítač s privátní IP adresou pomocí PowerShellu.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1745ca176fac18b4903686cb556670531ee40a1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244755"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Vytvoření virtuálního počítače se statickou privátní IP adresou pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete vytvořit virtuální počítač (VM) se statickou privátní IP adresou. Přiřaďte statickou privátní IP adresu, nikoli dynamickou adresu, pokud chcete vybrat, která adresa z podsítě je přiřazena virtuálnímu soudu. Další informace o [statických privátních IP adresách](virtual-network-ip-addresses-overview-arm.md#allocation-method). Pokud chcete změnit privátní IP adresu přiřazenou existujícímu virtuálnímu virtuálnímu ms z dynamického na statický nebo pracovat s veřejnými IP adresami, přečtěte si informace o [přidání, změně nebo odebrání IP adres](virtual-network-network-interface-addresses.md).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Můžete provést následující kroky z místního počítače nebo pomocí Azure Cloud Shell. Pokud chcete používat místní počítač, ujistěte se, že máte [nainstalovaný Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud chcete použít Prostředí Azure Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu libovolného příkazového pole, které následuje. Cloud Shell vás přihlásí do Azure.

1. Pokud používáte cloudové prostředí, přejděte ke kroku 2. Otevřete relaci příkazů a `Connect-AzAccount`přihlaste se do Azure pomocí aplikace .
2. Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Následující příklad vytvoří skupinu prostředků v oblasti East US Azure:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Vytvořte konfiguraci podsítě a virtuální síť pomocí příkazů [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) a [New-AzVirtualNetwork:](/powershell/module/az.network/new-azvirtualnetwork)

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

4. Vytvořte síťové rozhraní ve virtuální síti a přiřaďte privátní IP adresu z podsítě do síťového rozhraní pomocí příkazů [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) a [New-AzNetworkInterface:](/powershell/module/az.network/new-aznetworkinterface)

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

5. Vytvořte konfiguraci virtuálního počítače pomocí [new-azvmconfig](/powershell/module/Az.Compute/New-AzVMConfig)a pak vytvořte virtuální hosto v novém [azvm](/powershell/module/az.Compute/New-azVM). Po zobrazení výzvy zadejte uživatelské jméno a heslo, které se mají použít jako přihlašovací pověření pro virtuální počítače:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> I když můžete do operačního systému přidat nastavení privátní IP adresy, doporučujeme, abyste tak neučinili, dokud nepřečtete [přidat soukromou IP adresu do operačního systému](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> Chcete-li získat přístup k virtuálnímu virtuálnímu síti z Internetu, musíte virtuálnímu hokvadům přiřadit veřejnou IP adresu. Můžete také změnit přiřazení dynamické privátní IP adresy na statické přiřazení. Podrobnosti naleznete v tématu [Přidání nebo změna IP adres](virtual-network-network-interface-addresses.md). Dále se doporučuje omezit síťový provoz na virtuální počítač a připojovat skupinu zabezpečení sítě k síťovému rozhraní, podsíti, ve které jste vytvořili síťové rozhraní, nebo k oběma. Podrobnosti naleznete v [tématu Správa skupin zabezpečení sítě](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, můžete [odebrat-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) odebrat skupinu prostředků a všechny prostředky, které obsahuje:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [privátních IP adresách](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) a přiřazení [statické privátní IP adresy](virtual-network-network-interface-addresses.md#add-ip-addresses) virtuálnímu počítači Azure.
- Přečtěte si další informace o vytváření virtuálních počítačů [s Linuxem](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Windows.](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
