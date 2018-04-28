---
title: Diagnostika virtuálního počítače směrování potížím se sítí - prostředí Azure PowerShell | Microsoft Docs
description: V tomto článku zjistěte, jak diagnostikovat problém směrování sítě virtuálního počítače pomocí další funkce směrování služby sledovací proces sítě Azure.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f793a201b3fbf57ac2f420c4f4e57a230bc11468
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnostika virtuálního počítače směrování potížím se sítí - prostředí Azure PowerShell

V tomto článku nasazení virtuálního počítače (VM) a zkontrolujte komunikace na IP adresu a adresu URL. Je-li určit příčinu selhání komunikace a jak ho mohli vyřešit.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a použití prostředí PowerShell místně, v tomto článku vyžaduje prostředí AzureRM PowerShell verze modulu 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Než bude možné vytvořit virtuální počítač, musíte vytvořit skupinu prostředků tak, aby obsahovala virtuálního počítače. Vytvořte skupinu prostředků pomocí rutiny [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Vytvoření virtuálního počítače s [nové AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Při spuštění tohoto kroku se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač.

```azurepowershell-interactive
$vM = New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

Vytvoření virtuálního počítače trvá několik minut. V zbývající kroky nebudete pokračovat, dokud je virtuální počítač vytvořený a vrátí výstup prostředí PowerShell.

## <a name="test-network-communication"></a>Test síťové komunikace

K testování komunikaci sítě s sledovací proces sítě, musíte nejprve povolit sledovací proces sítě ve virtuálním počítači, který chcete testovat je v oblasti a pak použít další funkce směrování sledovací proces sítě k testování komunikace.

## <a name="enable-network-watcher"></a>Povolit sledovací proces sítě

Pokud již máte sledovací proces sítě povolené v oblasti Východ USA, použijte [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher) načíst sledovací proces sítě. Následující příklad načte existující sledovací proces sítě s názvem *NetworkWatcher_eastus* který se v *NetworkWatcherRG* skupiny prostředků:

```azurepowershell-interactive
$networkWatcher = Get-AzureRmNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Pokud ještě nemáte sledovací proces sítě povolené v oblasti Východ USA, použijte [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) v oblasti Východ USA vytvořit sledovací proces sítě:

```azurepowershell-interactive
$networkWatcher = New-AzureRmNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Použít další směrování

Azure automaticky vytvoří trasy do výchozího umístění. Můžete vytvořit vlastní trasy, které potlačí výchozí trasy. Vlastní trasy v některých případech může způsobit selhání komunikace. Chcete-li otestovat směrování z virtuálního počítače, použijte [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) příkaz pro určení další segment směrování, kdy je provoz určený pro konkrétní adresu.

Testovací odchozí komunikaci z virtuálního počítače na některou z IP adres pro www.bing.com:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Za několik sekund, výstup vás upozorní, který **NextHopType** je **Internet**a že **RouteTableId** je **systémová trasa**. Tento výsledek umožňuje vědět, že je platný trasu k cíli.

Testovací odchozí komunikaci z virtuálního počítače do 172.31.0.100:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Výstup vás upozorní, který **žádné** je **NextHopType**a že **RouteTableId** je také **systémová trasa**. Tento výsledek umožňuje vědět, že při trasu platné systému do cílového umístění neexistuje žádné další směrování směrovat provoz do cílového umístění.

## <a name="view-details-of-a-route"></a>Zobrazení podrobností trasu

K analýze další směrování, zkontrolujte efektivní trasy pro síťové rozhraní s [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) příkaz:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Se vrátí výstupu, který obsahuje následující text:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Jak je uvedeno v předchozí výstupní cesta se **AaddressPrefix** z **0.0.0.0/0** směrovat všechny přenosy není určené pro adresy v rámci jiné trasy předpony s příštího směrování **Internet**. Jak je vidět také ve výstupu, když je výchozí trasu k 172.16.0.0/12 předponu, která zahrnuje 172.31.0.100 adresu, **nextHopType** je **žádné**. Azure vytvoří výchozí trasu k 172.16.0.0/12, ale není zadat typ dalšího směrování, dokud je důvod se. Pokud jste například přidali rozsah adres 172.16.0.0/12 do adresního prostoru virtuální sítě, Azure změní **nextHopType** k **virtuální síť** pro trasu. Kontrola by pak zobrazíte **virtuální síť** jako **nextHopType**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili virtuální počítač a diagnostice, směrování sítě z virtuálního počítače. Jste zjistili, že Azure vytvoří několik výchozích tras a testovat směrování na dva různé cíle. Další informace o [směrování v Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a postup [vytvořit vlastní trasy](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Pro odchozí připojení virtuálních počítačů, můžete také určit latence a povolené a odepřen síťový provoz mezi virtuálního počítače a koncový bod pomocí sledovací proces sítě [řešení potíží s připojení](network-watcher-connectivity-powershell.md) schopností. Můžete monitorovat komunikace mezi virtuálního počítače a koncový bod, jako je například IP adresu nebo adresy URL, v čase pomocí funkce monitorování připojení sledovací proces sítě. Další informace, jak zjistit, [monitorování připojení k síti](connection-monitor.md).