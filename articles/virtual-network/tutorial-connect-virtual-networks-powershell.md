---
title: Připojení virtuálních sítí pomocí partnerského vztahu virtuální sítě – Azure PowerShell
description: V tomto článku se dozvíte, jak připojit virtuální sítě s partnerským vztahem virtuální sítě pomocí Azure PowerShellu.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
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
ms.openlocfilehash: caa620c2389bb6e2387636bc262ceb2de99d8e34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201284"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Připojení virtuálních sítí pomocí partnerského vztahu virtuální sítě pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Virtuální sítě můžete mezi sebou propojit s využitím partnerského vztahu virtuálních sítí. Po vytvoření partnerského vztahu virtuálních sítí budou moct prostředky v obou virtuálních sítích komunikovat mezi sebou se stejnou latencí a šířkou pásma, jako kdyby byly ve stejné virtuální síti. V tomto článku získáte informace o těchto tématech:

* Vytvoření dvou virtuálních sítí
* Propojení dvou virtuálních sítí s využitím partnerského vztahu virtuálních sítí
* Nasazení virtuálního počítače do obou virtuálních sítí
* Komunikace mezi virtuálními počítači

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, tento článek vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-virtual-networks"></a>Vytvoření virtuálních sítí

Před vytvořením virtuální sítě je třeba vytvořit skupinu prostředků pro virtuální síť a všechny ostatní prostředky vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí [skupiny New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Vytvořte virtuální síť s [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork1* s předponou adresy *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Vytvořte konfiguraci podsítě pomocí [nástroje New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Následující příklad vytvoří konfiguraci podsítě s předponou adresy 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Zápis konfigurace podsítě do virtuální sítě pomocí [programu Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), který vytvoří podsíť:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Vytvořte virtuální síť s předponou adresy 10.1.0.0/16 a jednou podsítí:

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

Vytvořte partnerský vztah pomocí [programu Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering). Následující příklad partnery *myVirtualNetwork1* *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Ve výstupu vráceném po provedení předchozího příkazu uvidíte, že **PeeringState** je *Initiated*. Partnerský vztah zůstane ve stavu *Zahájeno,* dokud nevytvoříte partnerský vztah z *myVirtualNetwork2* na *myVirtualNetwork1*. Vytvořte partnerský vztah z *myVirtualNetwork2* do *myVirtualNetwork1*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Ve výstupu vráceném po provedení předchozího příkazu uvidíte, že **PeeringState** je *připojen*. Azure také změnil stav partnerského vztahu partnerského vztahu partnerského vztahu *myVirtualNetwork1-myVirtualNetwork2* na *Connected*. Zkontrolujte, zda se stav partnerského vztahu pro partnerský vztah *myVirtualNetwork1-myVirtualNetwork2* změnil na *Připojeno* pomocí [programu Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Prostředky v jedné virtuální síti nemohou komunikovat s prostředky v jiné virtuální síti, dokud **peeringState** pro partnerských společností v obou virtuálních sítích je *připojen*.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte v obou virtuálních sítích virtuální počítač, abyste mezi nimi mohli v pozdějším kroku navázat komunikaci.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvořte virtuální hosti s [novým AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač s názvem *myVm1* ve virtuální síti *myVirtualNetwork1.* Tato `-AsJob` možnost vytvoří virtuální hovirtuální ho na pozadí, takže můžete pokračovat k dalšímu kroku. Po zobrazení výzvy zadejte uživatelské jméno a heslo, pomocí kterého se chcete přihlásit k virtuálnímu počítače.

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

Vytvoření virtuálního počítače trvá několik minut. Nepokračujte v pozdějších krocích, dokud Azure nevytvoří virtuální počítač a nevrátí výstup do Prostředí PowerShell.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

K veřejné IP adrese virtuálního uživatele se můžete připojit z internetu. Pomocí [get-azPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) vrátit veřejnou IP adresu virtuálního počítačů. Následující příklad vrátí veřejnou IP adresu virtuálního počítače *myVm1*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Pomocí následujícího příkazu můžete vytvořit relaci vzdálené plochy s virtuálním *počítačem myVm1* z místního počítače. Nahraďte `<publicIpAddress>` IP adresou vrácenou předchozím příkazem.

```
mstsc /v:<publicIpAddress>
```

Soubor RDP (Rdp) programu rdp (Rdp) je vytvořen, stažen do počítače a otevřen. Zadejte uživatelské jméno a heslo (možná budete muset vybrat **Další volby**, potom **použijte jiný účet**, určete přihlašovací údaje, které jste zadali při vytváření virtuálního počítače), a pak klikněte na **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a pokračujte v připojení.

Na virtuálním počítači *myVm1* povolte protokol ICMP (Internet Control Message Protocol) prostřednictvím brány firewall systému Windows, abyste mohli tento virtuální počítač příkazem ping z *myVm2* v pozdějším kroku pomocí prostředí PowerShell:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Přestože ping se používá ke komunikaci mezi virtuálními počítačemi v tomto článku, povolení ICMP prostřednictvím brány Windows Firewall pro produkční nasazení se nedoporučuje.

Pokud se chcete připojit k virtuálnímu počítači *myVm2*, zadejte následující příkaz na příkazový řádek na virtuálním počítači *myVm1*:

```
mstsc /v:10.1.0.4
```

Vzhledem k tomu, že jste povolili ping na *myVm1*, můžete nyní ping podle IP adresy z příkazového řádku na *myVm2* VM:

```
ping 10.0.0.4
```

Obdržíte čtyři odpovědi. Odpojte se z relací RDP k oběma virtuálním počítačům *myVm1* a *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, použijte [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak připojit dvě sítě ve stejné oblasti Azure, s partnerským vztahem virtuální sítě. Můžete vytvářet také partnerské vztahy virtuálních sítí v různých [podporovaných oblastech](virtual-network-manage-peering.md#cross-region) a [různých předplatných Azure](create-peering-different-subscriptions.md#powershell), stejně jako vytvářet [návrhy hvězdicovitých sítí](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) s partnerskými vztahy. Další informace o partnerských vztazích virtuálních sítí najdete v tématech [Přehled partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md) a [Správa partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md).

Vlastní [počítač](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) můžete připojit k virtuální síti prostřednictvím sítě VPN a pracovat s prostředky ve virtuální síti nebo ve virtuálních sítích. Opakovaně použitelné skripty k dokončení mnoha úkolů zahrnutých v článcích virtuální sítě naleznete v [tématu ukázky skriptů](powershell-samples.md).