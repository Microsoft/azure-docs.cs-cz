---
title: Diagnostika problému se směrováním sítě virtuálních počítačů – Azure PowerShell
titleSuffix: Azure Network Watcher
description: V tomto článku se dozvíte, jak diagnostikovat potíže se síťovým směrováním virtuálního počítače pomocí funkce dalšího směrování Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 362157f023f7ed4d2da81962acd32e2da968193e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84738783"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnostika potíží se síťovým směrováním virtuálního počítače – Azure PowerShell

V tomto článku nasadíte virtuální počítač (VM) a pak zkontrolujete komunikaci s IP adresou a adresou URL. Určíte příčinu selhání komunikace a najdete jeho řešení.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, Tento článek vyžaduje modul Azure PowerShell `Az` . Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.



## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Než vytvoříte virtuální počítač, musíte vytvořit skupinu prostředků, která bude virtuální počítač obsahovat. Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Vytvořte virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Při spuštění tohoto kroku se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

Vytvoření virtuálního počítače trvá několik minut. Dokud se virtuální počítač nevytvoří a PowerShell nevrátí výstup, nepokračujte ve zbývajících krocích.

## <a name="test-network-communication"></a>Test síťové komunikace

Chcete-li otestovat síťovou komunikaci s Network Watcher, je třeba nejprve povolit sledovací proces sítě v oblasti, ve které je virtuální počítač, který chcete testovat, a poté použít možnost Network Watcher dalšího směrování k otestování komunikace.

## <a name="enable-network-watcher"></a>Povolení Network Watcheru

Pokud již máte povolen sledovací proces sítě v oblasti Východní USA, použijte [příkaz Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) k načtení sledovacího procesu sítě. Následující příklad načte existující sledovací proces sítě s názvem *NetworkWatcher_eastus*, který se nachází ve skupině prostředků *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Pokud v oblasti Východní USA ještě nemáte povolený sledovací proces sítě, vytvořte sledovací proces sítě v Východní USA oblasti pomocí [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) :

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Použití dalšího směrování

Azure automaticky vytváří trasy pro výchozí cíle. Můžete vytvořit vlastní trasy, které přepíšou ty výchozí. Někdy můžou vlastní trasy způsobit selhání komunikace. K otestování směrování z virtuálního počítače použijte příkaz [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) a určete další směrování směrování, když je provoz určený pro konkrétní adresu.

Otestujte odchozí komunikaci z virtuálního počítače na jednu z IP adres stránky www.bing.com:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Po několika sekundách vám výstup informuje o tom, že je **typem** **Internet**a že **RouteTableId** je **systémovou trasou**. Tento výsledek vám umožní zjistit, zda existuje platná trasa k cíli.

Otestujte odchozí komunikaci z virtuálního počítače na IP adresu 172.31.0.100:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Vrácený výstup vás informuje o tom, že **žádný** je **typem**a že **RouteTableId** je také **systémovou trasou**. Tento výsledek říká, že i když existuje trasa systému do cíle, neexistuje žádné další směrování, které by do cíle směrovalo provoz.

## <a name="view-details-of-a-route"></a>Zobrazení podrobností o trase

Pokud chcete dál analyzovat směrování, Projděte si efektivní trasy pro síťové rozhraní pomocí příkazu [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) :

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Vrátí se výstup, který obsahuje následující text:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Jak vidíte v předchozím výstupu, trasa s **AddressPrefix** s **hodnotou 0.0.0.0/0** směruje veškerý provoz, který není určený pro adresy v rámci předpon adres jiných tras s dalším segmentem směrování **Internetu**. Jak vidíte ve výstupu, i když je výchozí trasa k předponě 172.16.0.0/12, která zahrnuje adresu 172.31.0.100, **typem** je **none**. Azure vytváří výchozí trasu do 172.16.0.0/12, ale neurčuje typ dalšího směrování, dokud k tomu není důvod. Pokud jste například přidali rozsah adres 172.16.0.0/12 do adresního prostoru virtuální sítě, Azure změní **typem** na **virtuální síť** pro trasu. Při kontrole se pak jako **typem**zobrazí **virtuální síť** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít [příkaz Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili virtuální počítač a diagnostikovat síťové směrování z virtuálního počítače. Dozvěděli jste se, že Azure vytváří několik výchozích tras, a otestovali jste směrování na dva různé cíle. Přečtěte si i další informace o [směrování v Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a o tom, jak [vytvářet vlastní trasy](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

U odchozích připojení virtuálních počítačů můžete také určit latenci a povolený a zakázaný síťový provoz mezi virtuálním počítačem a koncovým bodem pomocí funkce [řešení potíží s připojením](network-watcher-connectivity-powershell.md) Network Watcher. Komunikaci mezi virtuálním počítačem a koncovým bodem, jako je IP adresa nebo adresa URL, můžete v průběhu času sledovat pomocí možnosti monitorování připojení Network Watcher. Informace o postupu najdete v tématu [monitorování síťového připojení](connection-monitor.md).
