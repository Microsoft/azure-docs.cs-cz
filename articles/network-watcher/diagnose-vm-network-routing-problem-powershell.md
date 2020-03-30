---
title: Diagnostika problému se směrováním v síti virtuálních počítači – Azure PowerShell
titleSuffix: Azure Network Watcher
description: V tomto článku se dozvíte, jak diagnostikovat problém směrování sítě virtuálních strojů pomocí další možnosti směrování služby Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
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
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: b5a636471eab188dc8648761afedd81694331953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834701"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnostika problému se směrováním sítě virtuálních strojů – Azure PowerShell

V tomto článku nasadíte virtuální počítač (VM) a potom zkontrolujte komunikaci s IP adresou a adresou URL. Určíte příčinu selhání komunikace a najdete jeho řešení.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, tento `Az` článek vyžaduje modul Azure PowerShell. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.



## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Než vytvoříte virtuální počítač, musíte vytvořit skupinu prostředků, která bude virtuální počítač obsahovat. Vytvořte skupinu prostředků pomocí [skupiny New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Vytvořte virtuální virtuální město s [novým AzVM](/powershell/module/az.compute/new-azvm). Při spuštění tohoto kroku se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

Vytvoření virtuálního počítače trvá několik minut. Dokud se virtuální počítač nevytvoří a PowerShell nevrátí výstup, nepokračujte ve zbývajících krocích.

## <a name="test-network-communication"></a>Test síťové komunikace

Chcete-li otestovat síťovou komunikaci s sledovacím procesem sítě, musíte nejprve povolit sledovací proces sítě v oblasti, ve které je virtuální modul, ve kterém chcete testovat, a potom k testování komunikace použít další schopnost směrování sledovacího programu sítě.

## <a name="enable-network-watcher"></a>Povolení Network Watcheru

Pokud již máte v oblasti USA východ povolen sledovací modul sítě, načtěte jej pomocí [funkce Get-AzNetworkWatcher.](/powershell/module/az.network/get-aznetworkwatcher) Následující příklad načte existující sledovací proces sítě s názvem *NetworkWatcher_eastus*, který se nachází ve skupině prostředků *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Pokud ještě nemáte v oblasti USA východ povolen sledovací modul sítě, vytvořte pomocí služby [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) sledovací proces sítě v oblasti USA – východ:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Použití dalšího směrování

Azure automaticky vytváří trasy pro výchozí cíle. Můžete vytvořit vlastní trasy, které přepíšou ty výchozí. Někdy můžou vlastní trasy způsobit selhání komunikace. Chcete-li otestovat směrování z virtuálního soudu, použijte příkaz [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) k určení dalšího směrování směrování, když je provoz určen pro určitou adresu.

Otestujte odchozí komunikaci z virtuálního počítače na jednu z IP adres stránky www.bing.com:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Po několika sekundách výstup informuje, že **NextHopType** je **Internet**a že **RouteTableId** je **systémová trasa**. Tento výsledek vám umožní vědět, že existuje platná trasa k cíli.

Otestujte odchozí komunikaci z virtuálního počítače na IP adresu 172.31.0.100:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Vrácený výstup vás informuje, že **None** je **NextHopType**a že **RouteTableId** je také **systémová trasa**. Tento výsledek říká, že i když existuje trasa systému do cíle, neexistuje žádné další směrování, které by do cíle směrovalo provoz.

## <a name="view-details-of-a-route"></a>Zobrazení podrobností o trase

Chcete-li dále analyzovat směrování, zkontrolujte efektivní trasy pro síťové rozhraní pomocí příkazu [Get-AzEffectiveRouteTable:](/powershell/module/az.network/get-azeffectiveroutetable)

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Je vrácen výstup, který obsahuje následující text:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Jak můžete vidět v předchozím výstupu, trasa s **adresou Prefix** **0.0.0/0** směruje veškerý provoz, který není určen pro adresy v rámci předpony adresy jiné trasy s dalším směrováním **internetu**. Jak můžete také vidět ve výstupu, i když je výchozí trasa k 172.16.0.0/12 předpona, která obsahuje adresu 172.31.0.100, **nextHopType** je **None**. Azure vytváří výchozí trasu do 172.16.0.0/12, ale neurčuje typ dalšího směrování, dokud k tomu není důvod. Pokud jste například přidali rozsah adres 172.16.0.0/12 do adresního prostoru virtuální sítě, Azure změní **další HopType** na **virtuální síť** pro trasu. Kontrola by pak zobrazit **virtuální síť** jako **další HopType**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, můžete [odebrat-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) odebrat skupinu prostředků a všechny prostředky, které obsahuje:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili virtuální ho a diagnostikovali síťové směrování z virtuálního soudu. Dozvěděli jste se, že Azure vytváří několik výchozích tras, a otestovali jste směrování na dva různé cíle. Přečtěte si i další informace o [směrování v Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a o tom, jak [vytvářet vlastní trasy](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

U odchozích připojení virtuálních zařízení můžete také určit latenci a povolený a odepřený síťový provoz mezi virtuálním serverem a koncovým bodem pomocí funkce [řešení potíží s připojením](network-watcher-connectivity-powershell.md) sledování sítě. Můžete sledovat komunikaci mezi virtuálním počítačům a koncovým bodem, jako je například IP adresa nebo adresa URL, v průběhu času pomocí funkce sledování připojení sledování sítě. Informace o tom naleznete [v tématu Sledování síťového připojení](connection-monitor.md).
