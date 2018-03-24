---
title: Směrování síťového provozu - prostředí Azure PowerShell | Microsoft Docs
description: Zjistěte, jak ke směrování síťového provozu s směrovací tabulku pomocí prostředí PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f7be6aa58c6779150d3e79893e6e179d08611567
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Směrovat síťový provoz s směrovací tabulku pomocí prostředí PowerShell

Azure automaticky trasy provoz mezi všech podsítí v rámci virtuální sítě, ve výchozím nastavení. Můžete vytvořit vlastní trasy k přepsání Azure výchozí směrování. Možnost vytvářet vlastní trasy je užitečné, pokud například chcete směrovat přenos mezi podsítěmi přes virtuální síťové zařízení (hodnocení chyb zabezpečení). V tomto článku zjistíte, jak:

> [!div class="checklist"]
> * Vytvořit směrovací tabulku
> * Vytvořit trasu
> * Vytvoření virtuální sítě s několika podsítěmi
> * Přidružení tabulku směrování pro podsíť
> * Vytvoření hodnocení chyb zabezpečení, který směruje provoz
> * Nasazení virtuálních počítačů (VM) do různých podsítí
> * Směrovat provoz z jedné podsítě do jiné prostřednictvím hodnocení chyb zabezpečení

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a použití prostředí PowerShell místně, v tomto článku vyžaduje prostředí Azure PowerShell verze modulu 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure. 

## <a name="create-a-route-table"></a>Vytvořit směrovací tabulku

Než bude možné vytvořit směrovací tabulku, vytvořte skupinu prostředků s [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* pro všechny prostředky, které jsou vytvořené v tomto článku. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Vytvořit směrovací tabulku s [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable). Následující příklad vytvoří směrovací tabulku s názvem *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Vytvořit trasu

Vytvořit trasu načtením objekt tabulky trasy s [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), vytvořit trasu s [přidat AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig), zapište si konfigurace trasy do směrovací tabulky s [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

```azurepowershell-interactive
Get-AzureRmRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzureRmRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzureRmRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení tabulku směrování pro podsíť

Než budete moct přidružit tabulku směrování pro podsíť, budete muset vytvořit virtuální síť a podsíť. Vytvořte virtuální síť pomocí rutiny [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork* s předponou adresy *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Vytvořte tři podsítě vytvořením tří konfigurací podsítě s [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Následující příklad vytvoří tři konfigurací podsítě pro *veřejné*, *privátní*, a *DMZ* podsítě:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Zápis konfigurací podsítě pro virtuální síť s [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), která vytvoří podsítí ve virtuální síti:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Přidružení *myRouteTablePublic* směrovací tabulku, aby *veřejné* podsíť s [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) a zapište si konfiguraci podsítě, která virtuální síť s [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

## <a name="create-an-nva"></a>Vytvoření hodnocení chyb zabezpečení

Hodnocení chyb zabezpečení sítě je virtuální počítač, který provádí síťové funkce, například směrování, fungující brána firewall může nebo optimalizace sítě WAN.

Před vytvořením virtuálního počítače, vytvořte síťové rozhraní.

### <a name="create-a-network-interface"></a>Vytvořit rozhraní sítě

Před vytvořením rozhraní sítě, budete muset načíst virtuální sítě Id s [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), pak Id podsítě s [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Vytvoření síťové rozhraní s [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) v *DMZ* podsíť s povoleným předáváním IP:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzureRmVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Vytvoření virtuálního počítače

K vytvoření virtuálního počítače a k němu připojí existujícího síťového rozhraní, musíte nejdřív vytvořit konfigurace virtuálního počítače s [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). Konfigurace zahrnuje síťové rozhraní vytvořili v předchozím kroku. Po zobrazení výzvy k zadání uživatelského jména a hesla, vyberte uživatelské jméno a heslo, které chcete k přihlášení do virtuálního počítače s. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzureRmVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Vytvoření virtuálního počítače pomocí konfigurace virtuálního počítače s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač s názvem *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

`-AsJob` Možnost vytvoří virtuální počítač na pozadí, abyste mohli pokračovat k dalšímu kroku.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte dva virtuální počítače ve virtuální síti, abyste mohli ověřit, že provoz z *veřejné* podsítě se směruje na *privátní* podsítě virtuální zařízení sítě později. 

Vytvoření virtuálního počítače v *veřejné* podsíť s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač s názvem *myVmPublic* v *veřejné* podsíť *myVirtualNetwork* virtuální sítě. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Vytvoření virtuálního počítače v *privátní* podsítě.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

Virtuální počítač trvá několik minut pro vytvoření. Nemusíte pokračovat dalším krokem, dokud je virtuální počítač vytvořený a Azure vrátí výstup do prostředí PowerShell.

## <a name="route-traffic-through-an-nva"></a>Směrovat provoz prostřednictvím hodnocení chyb zabezpečení

Použití [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vrátit veřejnou IP adresu *myVmPrivate* virtuálních počítačů. Následující příklad vrací veřejnou IP adresu *myVmPrivate* virtuálních počítačů:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Chcete-li vytvořit relaci vzdálené plochy s použijte následující příkaz *myVmPrivate* virtuálního počítače z místního počítače. Nahraďte `<publicIpAddress>` s IP adresou vrácená z předchozí příkaz.

```
mstsc /v:<publicIpAddress>
```

Otevřete stažený soubor RDP. Po zobrazení výzvy vyberte **Connect**.

Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (budete muset vybrat možnost **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače), potom vyberte **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** pokračovat v připojení. 

V pozdější fázi příkaz tracert.exe slouží k otestování, směrování. Tracert používá zprávu protokolu ICMP (Internet Control), který byl odepřen přes bránu Windows Firewall. Povolte protokol ICMP přes bránu Windows firewall tak, že zadáte následující příkaz z prostředí PowerShell:

```powershell
New-NetFirewallRule ???DisplayName ???Allow ICMPv4-In??? ???Protocol ICMPv4
```

I když tracert se používá k testování směrování v tomto článku, což ICMP přes bránu Windows Firewall pro nasazení v produkčním prostředí se nedoporučuje.

Povolení předávání IP v rámci operačního systému *myVmNva* podle následujících kroků z *myVmPrivate* virtuálních počítačů:

Vzdálenou plochu *myVmNva* virtuálních počítačů pomocí následujícího příkazu z prostředí PowerShell:

``` 
mstsc /v:myvmnva
```
    
Pokud chcete povolit předávání v operačním systému protokolu IP, zadejte následující příkaz v prostředí PowerShell:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Restartujte virtuální počítač, který také odpojí relaci vzdálené plochy.

Při stále připojen k *myVmPrivate* virtuálního počítače, po *myVmNva* restartování virtuálního počítače, vytvořte relaci vzdálené plochy k *myVmPublic* virtuálních počítačů pomocí následujícího příkazu:

``` 
mstsc /v:myVmPublic
```
    
Povolte protokol ICMP přes bránu Windows firewall tak, že zadáte následující příkaz z prostředí PowerShell:

```powershell
New-NetFirewallRule ???DisplayName ???Allow ICMPv4-In??? ???Protocol ICMPv4
```

K testování směrování síťový provoz *myVmPrivate* virtuální počítač z *myVmPublic* virtuální počítač, z prostředí PowerShell zadejte následující příkaz:

```
tracert myVmPrivate
```

Odpověď je stejný jako v následujícím příkladu:
    
```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```
      
Uvidíte, že prvním skoku je 10.0.2.4, což je virtuální zařízení sítě privátní IP adresu. Druhé směrování je 10.0.1.4, privátní IP adresu *myVmPrivate* virtuálních počítačů. Trasy přidat do *myRouteTablePublic* směrovací tabulky a přidružené k *veřejné* podsíť způsobila Azure pro směrování provozu prostřednictvím hodnocení chyb zabezpečení, nikoli přímo do *privátní* podsítě.

Zavřete relaci vzdálené plochy k *myVmPublic* virtuálních počítačů, což zanechá jste stále připojeni k *myVmPrivate* virtuálních počítačů.
K testování směrování síťový provoz *myVmPublic* virtuální počítač z *myVmPrivate* virtuální počítač, zadejte následující příkaz z příkazového řádku:

```
tracert myVmPublic
```

Odpověď je stejný jako v následujícím příkladu:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
    
Trace complete.
```

Uvidíte, že provoz se směruje přímo z *myVmPrivate* virtuálního počítače *myVmPublic* virtuálních počítačů. Ve výchozím nastavení Azure trasy provoz přímo mezi podsítěmi.

Zavřete relaci vzdálené plochy k *myVmPrivate* virtuálních počítačů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, použijte [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto článku vytvořit směrovací tabulku a přidružené k podsíti. Vytvořili jste jednoduchá síť virtuální zařízení, která směrovat přenosy z veřejných podsítě do privátní podsítě. Nasazení celou řadu předem nakonfigurovaná síťová virtuální zařízení, které provádějí síťových funkcí, jako jsou brány firewall a optimalizace sítě WAN z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Před nasazením směrovací tabulky pro použití v provozním prostředí, doporučujeme, aby důkladně Seznamte se s [směrování v Azure](virtual-networks-udr-overview.md), [spravovat směrovací tabulky](manage-route-table.md), a [Azure omezuje](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Když nasadíte mnoho prostředků Azure v rámci virtuální sítě, zdrojů u některých služeb Azure PaaS nelze nasadit do virtuální sítě. Můžete dál omezit přístup k prostředkům některé služby Azure PaaS provoz jenom z podsítě virtuální sítě, když. Přechodu na v dalším kurzu se dozvíte, jak omezit přístup k síti na Azure PaaS prostředky.

> [!div class="nextstepaction"]
> [Omezit přístup k síti na PaaS prostředky](tutorial-restrict-network-access-to-resources-powershell.md)