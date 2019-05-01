---
title: Vytvoření virtuálního počítače se statickou privátní IP adresou – prostředí Azure PowerShell | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač s použitím privátní IP adresy pomocí prostředí PowerShell.
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
ms.openlocfilehash: 9115386b0543e1ac840aec29fc7f57e7c98c03bb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685337"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Vytvoření virtuálního počítače se statickou privátní IP adresou pomocí Powershellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vytvoření virtuálního počítače (VM) se statickou privátní IP adresou. Pokud chcete vybrat adresu z podsítě, která je přiřazena k virtuálnímu počítači přiřadíte statickou privátní IP adresou, nikoli s dynamickou adresou. Další informace o [statické privátní IP adresy](virtual-network-ip-addresses-overview-arm.md#allocation-method). Chcete-li změnit přiřazené k existujícímu virtuálnímu počítači z dynamického na statické privátní IP adresu, nebo pro práci s veřejnými IP adresami, přečtěte si téma [přidání, změna nebo odebrání IP adres](virtual-network-network-interface-addresses.md).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Ze svého místního počítače nebo s použitím Azure Cloud Shell, můžete provést následující kroky. Použití místního počítače, ujistěte se, máte [nainstalovaný Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud chcete použít Azure Cloud Shell, vyberte **vyzkoušet** v pravém horním rohu pole libovolný příkaz, který následuje. Cloud Shell se přihlásíte do Azure.

1. Pokud používáte Cloud Shell, přejděte ke kroku 2. Otevřete relaci příkazového řádku a přihlášení do Azure s využitím `Connect-AzAccount`.
2. Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) příkazu. Následující příklad vytvoří skupinu prostředků v oblasti Azure USA – východ:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Vytvořte konfiguraci podsítě a virtuální síť s [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) příkazy:

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

4. Vytvořte síťové rozhraní ve virtuální síti a přiřadit k síťovému rozhraní s privátní IP adresu z podsítě [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) příkazy:

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

5. Vytvořte konfiguraci virtuálního počítače pomocí [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig)a pak vytvořte virtuální počítač s [rutiny New-AzVM](/powershell/module/az.Compute/New-azVM). Po zobrazení výzvy zadejte uživatelské jméno a heslo, které má být použit jako přihlašovací údaje pro virtuální počítač:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> I když nastavení privátní IP adresy můžete přidat do operačního systému, doporučujeme, pokud to neuděláte, dokud se po přečtení [přidejte privátní IP adresu pro operační systém](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> Pro přístup k virtuálnímu počítači z Internetu, musíte přiřadit veřejnou IP adresu k virtuálnímu počítači. Můžete také změnit dynamické přiřazení privátní adresy IP na statické přiřazení. Podrobnosti najdete v tématu [přidání nebo změna IP adresy](virtual-network-network-interface-addresses.md). Kromě toho se doporučuje, tím, že přidružíte skupinu zabezpečení sítě pro síťové rozhraní a podsítě, kterou jste vytvořili síťové rozhraní v omezení síťového provozu směřujícího do svého virtuálního počítače. Podrobnosti najdete v tématu [Správa skupin zabezpečení sítě](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete použít [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) k odebrání skupiny prostředků a všech prostředků, které obsahuje:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

- Další informace o [privátních IP adres](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) a přiřazení [statickou privátní IP adresou](virtual-network-network-interface-addresses.md#add-ip-addresses) na virtuálním počítači Azure.
- Další informace o vytváření [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuálních počítačů.
