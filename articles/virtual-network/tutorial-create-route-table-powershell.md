---
title: "Směrování síťového provozu – prostředí PowerShell | Microsoft Docs"
description: "Zjistěte, jak ke směrování síťového provozu s směrovací tabulku pomocí prostředí PowerShell."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: f91b143c75a82aa6760796770b3ae4d0e4ec53dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Směrovat síťový provoz s směrovací tabulku pomocí prostředí PowerShell

Azure automaticky trasy provoz mezi všech podsítí v rámci virtuální sítě, ve výchozím nastavení. Můžete vytvořit vlastní trasy k přepsání Azure výchozí směrování. Možnost vytvářet vlastní trasy je užitečné, pokud například chcete směrovat přenos mezi podsítěmi přes bránu firewall. V tomto článku zjistíte, jak:

> [!div class="checklist"]
> * Vytvořit směrovací tabulku
> * Vytvořit trasu
> * Přidružení tabulku směrování pro podsíť virtuální sítě
> * Testování směrování
> * Řešení potíží s směrování

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a použití prostředí PowerShell místně, v tomto článku vyžaduje prostředí Azure PowerShell verze modulu 5.4.1 nebo novější. Spustit `Get-Module -ListAvailable AzureRM` najít nainstalovanou verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure. 

## <a name="create-a-route-table"></a>Vytvořit směrovací tabulku

Azure směrování provozu mezi všech podsítí ve virtuální síti, ve výchozím nastavení. Další informace o výchozích tras Azure najdete v tématu [systémové trasy](virtual-networks-udr-overview.md). Pokud chcete přepsat výchozí Azure a směrování, vytvořit směrovací tabulku, která obsahuje trasy a přidružit tabulku směrování pro podsíť virtuální sítě.

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

Směrovací tabulka obsahuje nula nebo víc tras. Vytvořit trasu načtením objekt tabulky trasy s [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), vytvořit trasu s [přidat AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig), zapište si konfigurace trasy do směrovací tabulky s [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

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

Trasy, která bude směrovat všechny přenosy určené do předpona adresy 10.0.1.0/24 prostřednictvím sítě virtuálního zařízení s IP adresou 10.0.2.4. Virtuální zařízení sítě a podsítě s předponou zadaná adresa se vytvoří v dalších krocích. Trasy přepíše Azure výchozí směrování, který směruje provoz mezi podsítěmi přímo. Každý postup určuje typ dalšího směrování. Typ dalšího přechodu dá pokyn Azure směrování provozu. V tomto příkladu je typ dalšího přechodu *VirtualAppliance*. Další informace o všech dostupných další typy směrování v Azure a jejich použití naleznete v tématu [dalšího směrování typy](virtual-networks-udr-overview.md#custom-routes).

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

Předpony adres musí být v rámci předpona adresy definované pro virtuální síť. Předpony adres podsítě se nesmí překrývat mezi sebou.

Zápis konfigurací podsítě pro virtuální síť s [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), která vytvoří podsítí ve virtuální síti:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Můžete přidružit směrovací tabulku do nula nebo více podsítí. Podsíť může mít žádnou nebo jednu směrovací tabulku přidružené k němu. Odchozí provoz z podsítě se směruje na základě Azure výchozí trasy a jakékoli vlastní trasy, která jste přidali do tabulky směrování, kterou přidružíte k podsíti. Přidružení *myRouteTablePublic* směrovací tabulku, aby *veřejné* podsíť s [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) a zapište si konfiguraci podsítě, která virtuální síť s [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

Před nasazením směrovací tabulky pro použití v provozním prostředí, doporučujeme, aby důkladně Seznamte se s [směrování v Azure](virtual-networks-udr-overview.md) a [Azure omezení](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Testování směrování

K testování směrování, vytvoříte virtuální počítač, který slouží jako virtuální zařízení sítě, prostřednictvím směruje trasy, kterou jste vytvořili v předchozím kroku. Po vytvoření virtuálního zařízení sítě, budete nasazovat do virtuálního počítače *veřejné* a *privátní* podsítě. Budete pak směrovat provoz z *veřejné* podsítě, který *privátní* podsítě virtuální síťové zařízení.

### <a name="create-a-network-virtual-appliance"></a>Vytvořit virtuální síťové zařízení

Virtuální počítač spuštěný síťové aplikace se často označuje jako virtuální zařízení sítě. Virtuální zařízení sítě obvykle přijímat síťový provoz, provádět některé akce, poté předat dál ani vyřadit síťový provoz na základě logiky nakonfigurované v síťové aplikace. 

#### <a name="create-a-network-interface"></a>Vytvořit rozhraní sítě

V předchozím kroku jste vytvořili trasu, která zadaný virtuální zařízení sítě jako typ dalšího směrování. Virtuální počítač spuštěný síťové aplikace se často označuje jako virtuální zařízení sítě. V produkčním prostředí je virtuální zařízení sítě, které můžete nasadit často předem nakonfigurovaný virtuální počítač. Několik síťových virtuálních zařízení jsou k dispozici na [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). V tomto článku se vytvoří základní virtuální počítač.

Virtuální počítač má jeden nebo více síťových rozhraní připojené k, které umožňují komunikovat se sítí virtuálního počítače. Pro síťové rozhraní, abyste mohli předávat síťové přenosy odesílané do, který není určený pro vlastní IP adresu, musí být povolené předávání IP pro síťové rozhraní. Před vytvořením rozhraní sítě, budete muset načíst virtuální sítě Id s [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), pak Id podsítě s [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Vytvoření síťové rozhraní s [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) v *DMZ* podsíť s povoleným předáváním IP:

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

#### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

K vytvoření virtuálního počítače a k němu připojí existujícího síťového rozhraní, musíte nejdřív vytvořit konfiguraci virtuálního počítače s [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). Konfigurace zahrnuje síťové rozhraní vytvořili v předchozím kroku. Po zobrazení výzvy k zadání uživatelského jména a hesla, vyberte uživatelské jméno a heslo, které chcete k přihlášení do virtuálního počítače s. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine configuration.
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

Vytvořit virtuální počítač pomocí konfigurace virtuálního počítače s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač s názvem *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

`-AsJob` Možnost vytvoří virtuální počítač na pozadí, abyste mohli pokračovat k dalšímu kroku. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které se chcete přihlásit k virtuálnímu počítači s. V produkčním prostředí je virtuální zařízení sítě, které můžete nasadit často předem nakonfigurovaný virtuální počítač. Několik síťových virtuálních zařízení jsou k dispozici na [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

Azure přiřazen 10.0.2.4 jako privátní IP adresu virtuálního počítače, protože 10.0.2.4 je první dostupná IP adresa v *DMZ* podsíť *myVirtualNetwork*.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte dva virtuální počítače ve virtuální síti, abyste mohli ověřit, že provoz z *veřejné* podsítě se směruje na *privátní* podsítě virtuální zařízení sítě později. 

Vytvoření virtuálního počítače v *veřejné* podsíť s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač s názvem *myVmWeb* v *veřejné* podsíť *myVirtualNetwork* virtuální sítě. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmWeb" `
  -AsJob
```

Azure přiřazen 10.0.0.4 jako privátní IP adresu virtuálního počítače, protože 10.0.1.4 je první dostupná IP adresa v *veřejné* podsíť *myVirtualNetwork*.

Vytvoření virtuálního počítače v *privátní* podsítě.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmMgmt"
```

Virtuální počítač trvá několik minut pro vytvoření. Azure přiřazen 10.0.1.4 jako privátní IP adresu virtuálního počítače, protože 10.0.1.4 je první dostupná IP adresa v *privátní* podsíť *myVirtualNetwork*. 

Nemusíte pokračovat dalším krokem, dokud je virtuální počítač vytvořený a Azure vrátí výstup do prostředí PowerShell.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Směrovat provoz prostřednictvím sítě virtuálního zařízení

Použití [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) povolit ICMP příchozí k *myVmWeb* a *myVmMgmt* virtuálních počítačů přes bránu Windows firewall pro použití tracert k testování komunikace mezi virtuálními počítači v pozdějším kroku:

```powershell-interactive
Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmWeb `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS

Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmMgmt `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS
```

Předchozí příkazy může trvat několik minut. Dalším krokem nepokračujte, dokud příkazy dokončení a výstup se vrací do prostředí PowerShell. I když tracert se používá k testování směrování v tomto článku, což ICMP přes bránu Windows Firewall pro nasazení v produkčním prostředí se nedoporučuje.

Připojit virtuální počítač veřejné IP adresy z Internetu. Použití [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vrátit veřejnou IP adresu virtuálního počítače. Následující příklad vrací veřejnou IP adresu *myVmMgmt* virtuálního počítače:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Chcete-li vytvořit relaci vzdálené plochy s použijte následující příkaz *myVmMgmt* virtuálního počítače z místního počítače. Nahraďte `<publicIpAddress>` s IP adresou vrácená z předchozí příkaz.

```
mstsc /v:<publicIpAddress>
```

Soubor Remote Desktop Protocol (.rdp) je vytvořen, stažena do počítače a otevřít. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (budete muset vybrat možnost **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při zpracování je Vytvoření virtuálního počítače) a potom vyberte **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a pokračujte v připojení. 

Jste povolili předávání IP v rámci Azure pro síťové rozhraní virtuálního počítače v [předávání IP povolit](#enable-ip-forwarding). V rámci virtuálního počítače operační systém nebo aplikace běžící v rámci virtuálního počítače, musí taky umět předávat síťový provoz. Když nasadíte virtuální síťové zařízení v provozním prostředí, zařízení obvykle filtry, protokoly nebo provádí některé další funkce před předáním provoz. V tomto článku však operační systém jednoduše předává veškerý provoz, které obdrží. Povolení předávání IP v rámci operačního systému *myVmNva*:

Z příkazového řádku na *myVmMgmt* virtuální počítač, vzdálenou plochu *myVmNva* virtuálního počítače:

``` 
mstsc /v:myVmNva
```
    
Povolení předávání IP v rámci operačního systému *myVmNva* virtuální počítač, zadejte následující příkaz v prostředí PowerShell *myVmNva* virtuálního počítače:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Restartujte *myVmNva* virtuální počítač, který také odpojíte této relaci vzdálené plochy, můžete ponechat v otevřít k relaci vzdálené plochy *myVmMgmt* virtuálního počítače.

Po *myVmNva* restartování virtuálního počítače, použijte následující příkaz k testování směrování pro síťový provoz *myVmWeb* virtuální počítač z *myVmMgmt* virtuální počítač.

```
tracert myvmweb
```

Odpověď je stejný jako v následujícím příkladu:

```
Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
  
Trace complete.
```

Uvidíte, že provoz se směruje přímo z *myVmMgmt* virtuální počítač *myVmWeb* virtuálního počítače. Azure výchozích tras, směrovat provoz přímo mezi podsítěmi.

Použijte následující příkaz pro vzdálenou plochu *myVmWeb* virtuální počítač z *myVmMgmt* virtuálního počítače:

``` 
mstsc /v:myVmWeb
```

K testování směrování pro síťový provoz *myVmMgmt* virtuální počítač z *myVmWeb* virtuální počítač, zadejte následující příkaz z příkazového řádku:

```
tracert myvmmgmt
```

Odpověď je stejný jako v následujícím příkladu:

```
Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```

Uvidíte, že prvním skoku je 10.0.2.4, což je virtuální zařízení sítě privátní IP adresu. Druhé směrování je 10.0.1.4, privátní IP adresu *myVmMgmt* virtuálního počítače. Trasy přidat do *myRouteTablePublic* směrovací tabulky a přidružené k *veřejné* podsíť způsobila Azure pro směrování provozu prostřednictvím hodnocení chyb zabezpečení, nikoli přímo do *privátní* podsítě.

Zavřete relací vzdálené plochy na obě *myVmWeb* a *myVmMgmt* virtuálních počítačů.

## <a name="troubleshoot-routing"></a>Řešení potíží s směrování

Jste se naučili v předchozích krocích, Azure, použije výchozí trasy, lze, Volitelně můžete přepsat pomocí vlastní trasy. V některých případech nemusí být provoz směruje podle očekávání jako. Použití [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) povolit sledovací proces sítě v oblasti EastUS, pokud ještě nemáte sledovací proces sítě v této oblasti:

```azurepowershell-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region:
$nw = New-AzureRmNetworkWatcher `
 -Location eastus `
 -Name myNetworkWatcher_eastus `
 -ResourceGroupName myResourceGroup
```

Použití [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) k určení, jak se provoz se směruje mezi dvěma virtuálními počítači. Například následující příkaz testy směrování provozu z *myVmWeb* (10.0.0.4) virtuálního počítače *myVmMgmt* (10.0.1.4) virtuálního počítače:

```azurepowershell-interactive
$vmWeb = Get-AzureRmVM `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup

Get-AzureRmNetworkWatcherNextHop `
  -DestinationIPAddress 10.0.1.4 `
  -NetworkWatcherName myNetworkWatcher_eastus `
  -ResourceGroupName myResourceGroup `
  -SourceIPAddress 10.0.0.4 `
  -TargetVirtualMachineId $vmWeb.Id
```
Tento výstup se vrátí po krátké čekání:

```azurepowershell
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

Výstup informuje, že dalšího směrování IP adresu pro provoz z *myVmWeb* k *myVmMgmt* je 10.0.2.4 ( *myVmNva* virtuální počítač), typ dalšího směrování je  *VirtualAppliance*, a že je směrovací tabulce, která způsobí, že směrování *myRouteTablePublic*.

Efektivní trasy pro každé síťové rozhraní jsou kombinaci Azure výchozí trasy a všech tras, které definujete. Chcete-li zobrazit všechny trasy efektivní pro rozhraní sítě ve virtuálním počítači s [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). Chcete-li například zobrazit efektivní trasy pro *myVmWeb* síťové rozhraní na *myVmWeb* virtuální počítač, zadejte následující příkaz:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Jsou vráceny všechny výchozí trasy a trasy, k níž jste přidali v předchozím kroku.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, použijte [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto článku vytvořit směrovací tabulku a přidružené k podsíti. Vytvořili jste virtuální zařízení sítě směrovat přenosy z veřejných podsítě do privátní podsítě. Když nasadíte mnoho prostředků Azure v rámci virtuální sítě, zdrojů u některých služeb Azure PaaS nelze nasadit do virtuální sítě. Můžete dál omezit přístup k prostředkům některé služby Azure PaaS provoz jenom z podsítě virtuální sítě, když. Přechodu na další článku se dozvíte, jak omezit přístup k síti na Azure PaaS prostředky.

> [!div class="nextstepaction"]
> [Omezit přístup k síti na PaaS prostředky](virtual-network-service-endpoints-configure.md#azure-powershell)