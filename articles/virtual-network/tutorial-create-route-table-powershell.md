---
title: Směrování síťového provozu Azure PowerShell | Microsoft Docs
description: V tomto článku zjistěte, jak ke směrování síťového provozu s směrovací tabulku pomocí prostředí PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 2aca1de567dbd4d37daf7f9dd7c407b669396a47
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "31603704"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Směrovat síťový provoz s směrovací tabulku pomocí prostředí PowerShell

Azure ve výchozím nastavení automaticky směruje provoz mezi všemi podsítěmi v rámci virtuální sítě. Můžete vytvořit vlastní trasy a přepsat tak výchozí směrování Azure. Možnost vytvářet vlastní trasy je užitečná například v případě, že chcete směrovat provoz mezi podsítěmi přes síťové virtuální zařízení. V tomto článku získáte informace o těchto tématech:

* Vytvoření směrovací tabulky
* Vytvoření trasy
* Vytvoření virtuální sítě s několika podsítěmi
* Přidružení směrovací tabulky k podsíti
* Vytvoření síťového virtuálního zařízení, které směruje provoz
* Nasazení virtuálních počítačů do různých podsítí
* Směrování provozu z jedné podsítě do jiné přes síťové virtuální zařízení

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure. 

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

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

## <a name="create-a-route"></a>Vytvoření trasy

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

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

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

## <a name="create-an-nva"></a>Vytvoření síťového virtuálního zařízení

Síťové virtuální zařízení je virtuální počítač, který provádí síťovou funkci, jako je směrování, brána firewall nebo optimalizace sítě WAN.

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

Vytvoření virtuálního počítače trvá několik minut. Nemusíte pokračovat dalším krokem, dokud je virtuální počítač vytvořený a Azure vrátí výstup do prostředí PowerShell.

## <a name="route-traffic-through-an-nva"></a>Směrování provozu přes síťové virtuální zařízení

Použití [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vrátit veřejnou IP adresu *myVmPrivate* virtuálních počítačů. Následující příklad vrací veřejnou IP adresu *myVmPrivate* virtuálních počítačů:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Chcete-li vytvořit relaci vzdálené plochy s použijte následující příkaz *myVmPrivate* virtuálního počítače z místního počítače. Nahraďte `<publicIpAddress>` IP adresou vrácenou předchozím příkazem.

```
mstsc /v:<publicIpAddress>
```

Otevřete stažený soubor RDP. Pokud se zobrazí výzva, vyberte **Připojit**.

Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače (abyste mohli zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače, možná budete muset vybrat **Další možnosti** a pak **Použít jiný účet**), a pak vyberte **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** a pokračujte v připojování. 

V pozdější fázi příkaz tracert.exe slouží k otestování, směrování. Tracert používá zprávu protokolu ICMP (Internet Control), který byl odepřen přes bránu Windows Firewall. Povolte průchod protokolu ICMP bránou Windows Firewall zadáním následujícího příkazu v PowerShellu na virtuálním počítači *myVmPrivate*:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

I když trasování cesty se používá k testování směrování v tomto článku, což ICMP přes bránu Windows Firewall pro nasazení v produkčním prostředí se nedoporučuje.

V části [Povolení předávání IP](#enable-ip-forwarding) jste povolili předávání IP v rámci Azure pro síťové rozhraní virtuálního počítače. Operační systém nebo aplikace spuštěná v rámci virtuálního počítače musí také být schopné směrovat síťový provoz. Povolení předávání IP v rámci operačního systému *myVmNva*.

Z příkazového řádku na *myVmPrivate* virtuálních počítačů, vzdálenou plochu *myVmNva*:

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
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
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

Pokud již nepotřebujete, použijte [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto článku vytvořit směrovací tabulku a přidružené k podsíti. Vytvořili jste jednoduchá síť virtuální zařízení, která směrovat přenosy z veřejných podsítě do privátní podsítě. Nasazení celou řadu předem nakonfigurovaná síťová virtuální zařízení, které provádějí síťových funkcí, jako jsou brány firewall a optimalizace sítě WAN z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Další informace o směrování najdete v tématech [Přehled směrování](virtual-networks-udr-overview.md) a [Správa směrovací tabulky](manage-route-table.md).

Přestože v rámci virtuální sítě můžete nasadit řadu prostředků Azure, prostředky některých služeb Azure PaaS do virtuální sítě nasadit nejde. Přesto můžete omezit přístup k prostředkům některých služeb Azure PaaS pouze pro provoz z podsítě virtuální sítě. Další informace, jak zjistit, [omezit síťový přístup k prostředkům PaaS](tutorial-restrict-network-access-to-resources-powershell.md).
