---
title: Propojení virtuálních sítí s partnerským vztahem virtuální sítě – Azure PowerShell
description: V tomto článku se dozvíte, jak propojit virtuální sítě s využitím partnerského vztahu virtuálních sítí pomocí Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5214dbe692584c8b1df2007019c8108fd56dfa38
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059339"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Propojení virtuálních sítí s využitím partnerského vztahu virtuálních sítí pomocí PowerShellu

Virtuální sítě můžete mezi sebou propojit s využitím partnerského vztahu virtuálních sítí. Po vytvoření partnerského vztahu virtuálních sítí budou moct prostředky v obou virtuálních sítích komunikovat mezi sebou se stejnou latencí a šířkou pásma, jako kdyby byly ve stejné virtuální síti. V tomto článku získáte informace o těchto tématech:

* Vytvoření dvou virtuálních sítí
* Propojení dvou virtuálních sítí s využitím partnerského vztahu virtuálních sítí
* Nasazení virtuálního počítače do obou virtuálních sítí
* Komunikace mezi virtuálními počítači

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento článek verzi modulu Azure PowerShell 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-virtual-networks"></a>Vytvoření virtuálních sítí

Před vytvořením virtuální sítě je nutné vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork1* s předponou adresy *10.0.0.0/16*.

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

Zapište konfiguraci podsítě do virtuální sítě pomocí [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), který vytvoří podsíť:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Vytvořte virtuální síť s předponou adresy 10.1.0.0/16 a jednu podsíť:

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

Vytvořte partnerský vztah s [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering). Následující příklad partnerských uzlů *myVirtualNetwork1* na *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Ve výstupu vráceném po provedení předchozího příkazu se zobrazí zpráva, že **PeeringState** je *inicializován*. Partnerský vztah zůstane v *inicializovaném* stavu, dokud nevytvoříte partnerský vztah z *myVirtualNetwork2* do *myVirtualNetwork1*. Vytvořte partnerský vztah z *myVirtualNetwork2* do *myVirtualNetwork1*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Ve výstupu vráceném po provedení předchozího příkazu se zobrazí informace o tom, že je **PeeringState** *připojený*. Azure také změnil stav partnerského vztahu partnerského vztahu *myVirtualNetwork1-myVirtualNetwork2* na *připojeno*. Potvrďte, že stav partnerského vztahu pro partnerský vztah *myVirtualNetwork1-myVirtualNetwork2* se změnil na *připojeno* pomocí [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Prostředky v jedné virtuální síti nemůžou komunikovat s prostředky ve druhé virtuální síti, dokud nejsou *připojené* **PeeringStatey** partnerských vztahů v obou virtuálních sítích.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte v obou virtuálních sítích virtuální počítač, abyste mezi nimi mohli v pozdějším kroku navázat komunikaci.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvořte virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač s názvem *myVm1* ve virtuální síti *myVirtualNetwork1* . `-AsJob`Možnost vytvoří virtuální počítač na pozadí, takže můžete pokračovat k dalšímu kroku. Po zobrazení výzvy zadejte uživatelské jméno a heslo, pomocí kterých se chcete přihlásit k virtuálnímu počítači.

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

Vytvoření virtuálního počítače trvá několik minut. Nepokračujte v pozdějších krocích, dokud Azure nevytvoří virtuální počítač a vrátí výstup do PowerShellu.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

K veřejné IP adrese virtuálního počítače se můžete připojit z Internetu. K vrácení veřejné IP adresy virtuálního počítače použijte [příkaz Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) . Následující příklad vrátí veřejnou IP adresu virtuálního počítače *myVm1*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Pomocí následujícího příkazu vytvořte relaci vzdálené plochy s virtuálním počítačem s *myVm1* z místního počítače. Nahraďte `<publicIpAddress>` IP adresou vrácenou předchozím příkazem.

```
mstsc /v:<publicIpAddress>
```

Vytvoří se soubor protokol RDP (Remote Desktop Protocol) (. RDP), stáhne se do počítače a otevře se. Zadejte uživatelské jméno a heslo (možná budete muset vybrat **Další volby**, pak **použít jiný účet**, chcete-li zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače), a pak klikněte na **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a pokračujte v připojení.

Na virtuálním počítači s *myVm1* Povolte protokol ICMP (Internet Control Message Protocol) přes bránu Windows Firewall, abyste mohli pomocí tohoto virtuálního počítače v pozdějším kroku provést příkaz k otestování z *MyVm2* v prostředí PowerShell:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

I když se k komunikaci mezi virtuálními počítači v tomto článku používá příkaz test pomocí protokolu ICMP, nedoporučuje se povolit protokol ICMP prostřednictvím brány Windows Firewall pro produkční nasazení.

Pokud se chcete připojit k virtuálnímu počítači *myVm2*, zadejte následující příkaz na příkazový řádek na virtuálním počítači *myVm1*:

```
mstsc /v:10.1.0.4
```

Vzhledem k tomu, že jste povolili příkaz *myVm1* na virtuálním počítači, můžete ho teď pomocí příkazu na základě IP adresy na virtuálním počítači *myVm2* odeslat příkazem na něj

```
ping 10.0.0.4
```

Dostanete čtyři odpovědi. Odpojte se z relací RDP k oběma virtuálním počítačům *myVm1* a *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků a všechny prostředky, které obsahuje, pomocí [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak propojit dvě sítě ve stejné oblasti Azure s partnerským vztahem virtuální sítě. Můžete vytvářet také partnerské vztahy virtuálních sítí v různých [podporovaných oblastech](virtual-network-manage-peering.md#cross-region) a [různých předplatných Azure](create-peering-different-subscriptions.md#powershell), stejně jako vytvářet [návrhy hvězdicovitých sítí](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) s partnerskými vztahy. Další informace o partnerských vztazích virtuálních sítí najdete v tématech [Přehled partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md) a [Správa partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md).

[Vlastní počítač můžete připojit k virtuální síti](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pomocí sítě VPN a pracovat s prostředky ve virtuální síti nebo v partnerských virtuálních sítích. Aby bylo možné opakovaně použitelným skriptům dokončit mnoho úloh popsaných v článcích o virtuální síti, přečtěte si téma [ukázky skriptů](powershell-samples.md).
