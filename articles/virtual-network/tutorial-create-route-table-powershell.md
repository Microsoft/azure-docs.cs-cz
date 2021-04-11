---
title: Směrovat síťový provoz Azure PowerShell | Microsoft Docs
description: V tomto článku se dozvíte, jak směrovat síťový provoz pomocí směrovací tabulky pomocí PowerShellu.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
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
ms.openlocfilehash: 3386c8b57a5ced151d9f51facba583f03660e4ee
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061974"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Směrování síťového provozu pomocí směrovací tabulky pomocí PowerShellu

Azure ve výchozím nastavení automaticky směruje provoz mezi všemi podsítěmi v rámci virtuální sítě. Můžete vytvořit vlastní trasy a přepsat tak výchozí směrování Azure. Možnost vytvářet vlastní trasy je užitečná například v případě, že chcete směrovat provoz mezi podsítěmi přes síťové virtuální zařízení. V tomto článku získáte informace o těchto tématech:

* Vytvoření směrovací tabulky
* Vytvoření trasy
* Vytvoření virtuální sítě s několika podsítěmi
* Přidružení směrovací tabulky k podsíti
* Vytvoření síťového virtuálního zařízení, které směruje provoz
* Nasazení virtuálních počítačů do různých podsítí
* Směrování provozu z jedné podsítě do jiné přes síťové virtuální zařízení

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento článek verzi modulu Azure PowerShell 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

Než budete moct vytvořit směrovací tabulku, vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* pro všechny prostředky vytvořené v tomto článku.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Vytvořte směrovací tabulku pomocí [New-AzRouteTable](/powershell/module/az.network/new-azroutetable). Následující příklad vytvoří směrovací tabulku s názvem *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Vytvoření trasy

Vytvořte trasu načtením objektu směrovací tabulky pomocí [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable), vytvořte trasu pomocí [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig)a pak zapište konfiguraci směrování do tabulky směrování pomocí [set-AzRouteTable](/powershell/module/az.network/set-azroutetable).

```azurepowershell-interactive
Get-AzRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

Předtím, než budete moci přidružit směrovací tabulku k podsíti, je nutné vytvořit virtuální síť a podsíť. Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork* s předponou adresy *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Vytvořte tři podsítě vytvořením tří konfigurací podsítí pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Následující příklad vytvoří tři konfigurace podsítí pro *veřejné*, *privátní* a *DMZ* podsítě:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Zapište konfigurace podsítě do virtuální sítě pomocí [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), který vytvoří podsítě ve virtuální síti:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

Přiřaďte tabulku směrování *myRouteTablePublic* k *veřejné* podsíti pomocí [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) a pak zapište konfiguraci podsítě do virtuální sítě pomocí [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork).

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $myRouteTablePublic | `
Set-AzVirtualNetwork
```

## <a name="create-an-nva"></a>Vytvoření síťového virtuálního zařízení

Síťové virtuální zařízení je virtuální počítač, který provádí síťovou funkci, jako je směrování, brána firewall nebo optimalizace sítě WAN.

Před vytvořením virtuálního počítače vytvořte síťové rozhraní.

### <a name="create-a-network-interface"></a>Vytvoření síťového rozhraní

Před vytvořením síťového rozhraní musíte načíst ID virtuální sítě pomocí [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)a pak ID podsítě pomocí [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig). Vytvořte síťové rozhraní s [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) v podsíti *DMZ* s povoleným předáváním IP:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Pokud chcete vytvořit virtuální počítač a připojit k němu stávající síťové rozhraní, musíte nejdřív vytvořit konfiguraci virtuálního počítače pomocí [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Tato konfigurace zahrnuje síťové rozhraní vytvořené v předchozím kroku. Po zobrazení výzvy k zadání uživatelského jména a hesla vyberte uživatelské jméno a heslo, pomocí kterého se chcete přihlásit k virtuálnímu počítači.

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzVMNetworkInterface -Id $nic.Id
```

Vytvořte virtuální počítač s použitím konfigurace virtuálního počítače pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač s názvem *myVmNva*.

```azurepowershell-interactive
$vmNva = New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

`-AsJob`Možnost vytvoří virtuální počítač na pozadí, takže můžete pokračovat k dalšímu kroku.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače, abyste mohli ověřit, že provoz z *veřejné* podsítě je směrován do *privátní* podsítě prostřednictvím síťového virtuálního zařízení v pozdějším kroku.

Vytvořte virtuální počítač ve *veřejné* podsíti pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří virtuální počítač s názvem *myVmPublic* ve *veřejné* podsíti virtuální sítě *myVirtualNetwork* .

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Vytvořte virtuální počítač v *privátní* podsíti.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

Vytvoření virtuálního počítače trvá několik minut. V dalším kroku nepokračujte, dokud se virtuální počítač nevytvoří a Azure vrátí výstup do PowerShellu.

## <a name="route-traffic-through-an-nva"></a>Směrování provozu přes síťové virtuální zařízení

K vrácení veřejné IP adresy virtuálního počítače *myVmPrivate* použijte [příkaz Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) . Následující příklad vrátí veřejnou IP adresu virtuálního počítače *myVmPrivate* :

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Pomocí následujícího příkazu vytvořte relaci vzdálené plochy s virtuálním počítačem s *myVmPrivate* z místního počítače. Nahraďte `<publicIpAddress>` IP adresou vrácenou předchozím příkazem.

```
mstsc /v:<publicIpAddress>
```

Otevřete stažený soubor RDP. Pokud se zobrazí výzva, vyberte **Připojit**.

Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (abyste mohli zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače, možná budete muset vybrat **Další možnosti** a pak **Použít jiný účet**), a pak vyberte **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** a pokračujte v připojování.

V pozdějším kroku se `tracert.exe` k otestování směrování používá příkaz. Tracert používá protokol ICMP (Internet Control Message Protocol), který je odepřen prostřednictvím brány Windows Firewall. Povolte průchod protokolu ICMP bránou Windows Firewall zadáním následujícího příkazu v PowerShellu na virtuálním počítači *myVmPrivate*:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

I když se k testování směrování v tomto článku používá trasa trasování, nedoporučuje se povolit protokol ICMP prostřednictvím brány Windows Firewall pro produkční nasazení.

V části Povolení předávání IP jste povolili předávání IP v rámci Azure pro síťové rozhraní virtuálního počítače. Operační systém nebo aplikace spuštěná v rámci virtuálního počítače musí také být schopné směrovat síťový provoz. Povolte předávání IP v rámci operačního systému *myVmNva*.

Z příkazového řádku na virtuálním počítači s *myVmPrivate* : Vzdálená plocha na *myVmNva*:

``` 
mstsc /v:myvmnva
```

Pokud chcete povolit předávání IP v rámci operačního systému, v PowerShellu na virtuálním počítači *myVmNva* zadejte následující příkaz:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```

Restartujte virtuální počítač *myVmNva*. Tím se také odpojí relace vzdálené plochy.

Zatímco jste stále připojeni k virtuálnímu počítači *myVmPrivate*, po restartování virtuálního počítače *myVmNva* vytvořte relaci vzdálené plochy k virtuálnímu počítači *myVmPublic*:

``` 
mstsc /v:myVmPublic
```

Povolte průchod protokolu ICMP bránou Windows Firewall zadáním následujícího příkazu v PowerShellu na virtuálním počítači *myVmPublic*:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Pokud chcete otestovat směrování síťového provozu z virtuálního počítače *myVmPublic* do virtuálního počítače *myVmPrivate*, v PowerShellu na virtuálním počítači *myVmPublic* zadejte následující příkaz:

```
tracert myVmPrivate
```

Odpověď bude podobná jako v následujícím příkladu:

```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:

1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4

Trace complete.
```

Jak vidíte, první segment směrování je 10.0.2.4, což je privátní IP adresa síťového virtuálního zařízení. Druhý segment směrování je 10.0.1.4, což je privátní IP adresa virtuálního počítače *myVmPrivate*. Trasa přidaná do směrovací tabulky *myRouteTablePublic* a přidružená k podsíti *Public* způsobila, že Azure směruje provoz přes síťové virtuální zařízení, a ne přímo do podsítě *Private*.

Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPublic*. Stále zůstanete připojeni k virtuální síti *myVmPrivate*.

Pokud chcete otestovat směrování síťového provozu z virtuálního počítače *myVmPrivate* do virtuálního počítače *myVmPublic*, na příkazovém řádku na virtuálním počítači *myVmPrivate* zadejte následující příkaz:

```
tracert myVmPublic
```

Odpověď bude podobná jako v následujícím příkladu:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:

1     1 ms     1 ms     1 ms  10.0.0.4

Trace complete.
```

Jak vidíte, provoz se směruje přímo z virtuálního počítače *myVmPrivate* do virtuálního počítače *myVmPublic*. Azure ve výchozím nastavení směruje provoz přímo mezi podsítěmi.

Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPrivate*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků a všechny prostředky, které obsahuje, pomocí [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili směrovací tabulku a přidružili ji k podsíti. Vytvořili jste jednoduché síťové virtuální zařízení, které směruje provoz z veřejné podsítě do privátní podsítě. Nasaďte celou řadu předem nakonfigurovaných síťových virtuálních zařízení, která v [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)provádí síťové funkce, jako je brána firewall a optimalizace sítě WAN. Další informace o směrování najdete v tématech [Přehled směrování](virtual-networks-udr-overview.md) a [Správa směrovací tabulky](manage-route-table.md).

Přestože v rámci virtuální sítě můžete nasadit řadu prostředků Azure, prostředky některých služeb Azure PaaS do virtuální sítě nasadit nejde. Přesto můžete omezit přístup k prostředkům některých služeb Azure PaaS pouze pro provoz z podsítě virtuální sítě. Další informace najdete v tématu [omezení síťového přístupu k prostředkům PaaS](tutorial-restrict-network-access-to-resources-powershell.md).
