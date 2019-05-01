---
title: Propojení virtuálních sítí s využitím partnerského vztahu virtuální sítě – PowerShell | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak k propojení virtuálních sítí s využitím partnerského vztahu virtuálních sítí pomocí Azure Powershellu.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
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
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 0ee39e83ef49db1d6231b5c20eee4dbf984f9f13
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698960"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Propojení virtuálních sítí se partnerský vztah virtuální sítě pomocí Powershellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Virtuální sítě můžete mezi sebou propojit s využitím partnerského vztahu virtuálních sítí. Po vytvoření partnerského vztahu virtuálních sítí budou moct prostředky v obou virtuálních sítích komunikovat mezi sebou se stejnou latencí a šířkou pásma, jako kdyby byly ve stejné virtuální síti. V tomto článku získáte informace o těchto tématech:

* Vytvoření dvou virtuálních sítí
* Propojení dvou virtuálních sítí s využitím partnerského vztahu virtuálních sítí
* Nasazení virtuálního počítače do obou virtuálních sítí
* Komunikace mezi virtuálními počítači

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte modul Azure PowerShell verze 1.0.0 nebo novějším. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-virtual-networks"></a>Vytvoření virtuálních sítí

Před vytvořením virtuální sítě, je nutné vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky vytvořené v tomto článku. Vytvořte skupinu prostředků s [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Vytvoření virtuální sítě s [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork1* s předponou adresy *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Vytvořte konfiguraci podsítě pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Následující příklad vytvoří konfiguraci podsítě s předponou adresy 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Tuto konfiguraci podsítě zapište do virtuální sítě pomocí [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), která vytvoří podsíť:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Vytvoření virtuální sítě pomocí předpony adres 10.1.0.0/16 a jednu podsíť:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Vytvoření partnerského vztahu virtuálních sítí

Vytvoření partnerského vztahu s [přidat AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering). Následující příklad partnerské uzly *myVirtualNetwork1* k *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Ve výstupu vráceného po spuštění předchozího příkazu, uvidíte, že **PeeringState** je *iniciováno*. Vytvoření partnerského vztahu zůstává ve *iniciováno* stavu, dokud vytvoříte partnerské připojení z *myVirtualNetwork2* k *myVirtualNetwork1*. Vytvoření partnerského vztahu z *myVirtualNetwork2* k *myVirtualNetwork1*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Ve výstupu vráceného po spuštění předchozího příkazu, uvidíte, že **PeeringState** je *připojeno*. Azure také změnilo stav partnerského vztahu *myVirtualNetwork1-myVirtualNetwork2* partnerského vztahu pro *připojeno*. Ujistěte se, že stav partnerského vztahu pro *myVirtualNetwork1-myVirtualNetwork2* partnerský vztah změněn na *připojeno* s [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Prostředky v jedné virtuální síti nemůžou komunikovat s prostředky v jiné virtuální síti, dokud **PeeringState** pro partnerské vztahy v obou virtuálních sítích je *připojeno*.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte v obou virtuálních sítích virtuální počítač, abyste mezi nimi mohli v pozdějším kroku navázat komunikaci.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvoření virtuálního počítače s [nové AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač s názvem *myVm1* v *myVirtualNetwork1* virtuální sítě. `-AsJob` Možnost se virtuální počítač vytvoří na pozadí, takže můžete pokračovat k dalšímu kroku. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které chcete k přihlášení k virtuálnímu počítači s.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

Vytvoření virtuálního počítače trvá několik minut. Pozdější kroky nepokračujte, dokud Azure se virtuální počítač vytvoří a vrátí výstup do prostředí PowerShell.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

Na veřejnou IP adresu Virtuálního počítače můžete připojit z Internetu. Použití [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) vrátí veřejnou IP adresu virtuálního počítače. Následující příklad vrátí veřejnou IP adresu virtuálního počítače *myVm1*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Použijte následující příkaz k vytvoření relace vzdálené plochy s *myVm1* virtuálního počítače ze svého místního počítače. Nahraďte `<publicIpAddress>` IP adresou vrácenou předchozím příkazem.

```
mstsc /v:<publicIpAddress>
```

Remote Desktop Protocol (RDP) soubor je vytvořen, stáhnou do počítače a otevřít. Zadejte uživatelské jméno a heslo (je třeba vybrat **víc možností**, pak **použít jiný účet**, a zadejte přihlašovací údaje, které jste zadali při vytváření virtuálního počítače) a potom klikněte na tlačítko **OK** . Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a pokračujte v připojení.

Na *myVm1* virtuálního počítače, povolit tak příkazem ping tento virtuální počítač z brány firewall ovládací prvek zpráva ICMP (Internet Protocol) přes Windows *myVm2* v pozdějším kroku, pomocí prostředí PowerShell:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Přestože ke komunikaci mezi virtuálními počítači v tomto článku se používá příkaz ping, povolení průchodu protokolu ICMP bránou Windows Firewall v produkčních prostředích se nedoporučuje.

Pokud se chcete připojit k virtuálnímu počítači *myVm2*, zadejte následující příkaz na příkazový řádek na virtuálním počítači *myVm1*:

```
mstsc /v:10.1.0.4
```

Vzhledem k tomu, že jste povolili službu příkazem ping na *myVm1*, můžete teď příkazem ping se podle IP adresy z příkazového řádku na *myVm2* virtuálního počítače:

```
ping 10.0.0.4
```

Obdržíte čtyři odpovědi. Odpojte se z relací RDP k oběma virtuálním počítačům *myVm1* a *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, použijte [odebrat AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) k odebrání skupiny prostředků a všech prostředků, které obsahuje.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak propojit dvě sítě ve stejné oblasti Azure s využitím partnerského vztahu virtuální sítě. Můžete vytvářet také partnerské vztahy virtuálních sítí v různých [podporovaných oblastech](virtual-network-manage-peering.md#cross-region) a [různých předplatných Azure](create-peering-different-subscriptions.md#powershell), stejně jako vytvářet [návrhy hvězdicovitých sítí](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s partnerskými vztahy. Další informace o partnerských vztazích virtuálních sítí najdete v tématech [Přehled partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md) a [Správa partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md).

Je možné [připojení vlastního počítače k virtuální síti](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) prostřednictvím sítě VPN a práci s prostředky ve virtuální síti nebo v partnerských virtuálních sítích. Opakovaně použitelné skriptů k provedení mnoha z úlohy najdete v článcích virtuální sítě, naleznete v tématu [ukázkové skripty](powershell-samples.md).