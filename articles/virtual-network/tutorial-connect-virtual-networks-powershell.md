---
title: Připojit virtuální sítě pomocí virtuální sítě partnerský vztah – prostředí PowerShell | Microsoft Docs
description: V tomto článku zjistěte, jak připojit virtuální sítě pomocí virtuální sítě partnerský vztah, pomocí Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 2490b96716519ef749dd1e3a1fbe6846c6b5d999
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Připojit virtuální sítě pomocí virtuální sítě pomocí prostředí PowerShell vytvoření partnerského vztahu.

Virtuální sítě můžete připojit k sobě navzájem s partnerský vztah virtuální sítě. Jakmile se kterými mají partnerský virtuální sítě, prostředky v obě virtuální sítě jsou komunikovat s mezi sebou, se stejnou latenci a šířky pásma, jako kdyby byly prostředky ve stejné virtuální síti. V tomto článku se dozvíte, jak:

* Vytvořte dvě virtuální sítě
* Připojení dvě virtuální sítě pomocí virtuální sítě partnerského vztahu
* Nasazení virtuálního počítače (VM) do každé virtuální sítě
* Komunikace mezi virtuálními počítači

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a použití prostředí PowerShell místně, v tomto článku vyžaduje prostředí Azure PowerShell verze modulu 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure. 

## <a name="create-virtual-networks"></a>Vytvoření virtuálních sítí

Před vytvořením virtuální sítě, budete muset vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky, které jsou vytvořené v tomto článku. Vytvořte skupinu prostředků s [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Vytvořte virtuální síť pomocí rutiny [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork1* s předponou adresy *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Vytvoření konfigurací podsítě s [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Následující příklad vytvoří konfiguraci podsítí s předponou adresy 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Zapsat konfiguraci podsítě virtuální sítě s [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), která vytvoří podsíť:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

Vytvoření virtuální sítě s předponu adresy 10.1.0.0/16 a jednu podsíť:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzureRmVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Sdílené virtuální sítě

Vytvoření partnerského vztahu s [přidat AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering). Následující příklad partnerské uzly *myVirtualNetwork1* k *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Ve výstupu vráceného po provedení předchozí příkaz, který uvidíte **PeeringState** je *získaných*. Partnerského vztahu zůstane v aplikaci *získaných* stavu, dokud nevytvoříte partnerského vztahu z *myVirtualNetwork2* k *myVirtualNetwork1*. Vytvoření partnerského vztahu z *myVirtualNetwork2* k *myVirtualNetwork1*. 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Ve výstupu vráceného po provedení předchozí příkaz, který uvidíte **PeeringState** je *připojeno*. Azure také změnit stav partnerského vztahu *myVirtualNetwork1 myVirtualNetwork2* partnerského vztahu k *připojeno*. Potvrďte, že stav partnerského vztahu *myVirtualNetwork1 myVirtualNetwork2* partnerský vztah změnit tak, aby *připojeno* s [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Prostředky v jedné virtuální sítě nemůže komunikovat s prostředky v jiné virtuální síti, dokud **PeeringState** pro partnerské vztahy v obě virtuální sítě je *připojeno*. 

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvoření virtuálního počítače v každé virtuální sítě, aby mohl komunikovat mezi nimi později.

### <a name="create-the-first-vm"></a>Vytvoření první virtuální počítač

Vytvoření virtuálního počítače s [nové AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač s názvem *myVm1* v *myVirtualNetwork1* virtuální sítě. `-AsJob` Možnost vytvoří virtuální počítač na pozadí, abyste mohli pokračovat k dalšímu kroku. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které chcete k přihlášení do virtuálního počítače s.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>Vytvořit druhý virtuální počítač

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

Virtuální počítač trvá několik minut pro vytvoření. Dalších krocích nepokračujte, dokud se vytvoří virtuální počítač Azure a vrátí výstup do prostředí PowerShell.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

Na veřejnou IP adresu Virtuálního počítače se můžete připojit z Internetu. Použití [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vrátit veřejnou IP adresu virtuálního počítače. Následující příklad vrací veřejnou IP adresu *myVm1* virtuálních počítačů:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Chcete-li vytvořit relaci vzdálené plochy s použijte následující příkaz *myVm1* virtuálního počítače z místního počítače. Nahraďte `<publicIpAddress>` s IP adresou vrácená z předchozí příkaz.

```
mstsc /v:<publicIpAddress>
```

Soubor Remote Desktop Protocol (.rdp) je vytvořen, stažena do počítače a otevřít. Zadejte uživatelské jméno a heslo (budete muset vybrat možnost **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače) a pak klikněte na tlačítko **OK** . Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a pokračujte v připojení.

Na *myVm1* virtuálních počítačů, povolit tak může odeslat příkaz ping tento virtuální počítač z brány firewall zprávu protokolu ICMP (Internet Control) prostřednictvím Windows *Můjvp2* v pozdější fázi, pomocí prostředí PowerShell:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

I když je příkazu ping použít ke komunikaci mezi virtuálními počítači v tomto článku, což ICMP přes bránu Windows Firewall pro nasazení v produkčním prostředí se nedoporučuje.

Pro připojení k *Můjvp2* virtuální počítač, zadejte následující příkaz z příkazového řádku *myVm1* virtuálních počítačů:

```
mstsc /v:10.1.0.4
```

Vzhledem k tomu, že jste povolili příkazu ping na *myVm1*, vám může nyní odeslat příkaz ping ho podle IP adresy z příkazového řádku na *Můjvp2* virtuálních počítačů:

```
ping 10.0.0.4
```

Obdržíte čtyři odpovědi. Vaše relace protokolu RDP na obě odpojit *myVm1* a *Můjvp2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, použijte [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak připojit ve stejné oblasti Azure, dvě sítě pomocí virtuální sítě partnerský vztah. Můžete také peer virtuální sítě v různých [podporované oblasti](virtual-network-manage-peering.md#cross-region) a v [různých předplatných Azure](create-peering-different-subscriptions.md#powershell), stejně, jako je vytváření [hvězdicové sítě návrhů](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s partnerský vztah. Další informace o partnerském vztahu virtuální sítě najdete v tématu [partnerského vztahu Přehled virtuálních sítí](virtual-network-peering-overview.md) a [spravovat virtuální sítě partnerských vztahů](virtual-network-manage-peering.md).

Můžete [svého počítače připojit k virtuální síti](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) prostřednictvím sítě VPN a komunikovat s prostředky ve virtuální síti, nebo peered virtuální sítě. Znovu použitelné skripty k dokončení mnoho úloh, které jsou popsané v článcích virtuální sítě, naleznete v části [skript ukázky](powershell-samples.md).
