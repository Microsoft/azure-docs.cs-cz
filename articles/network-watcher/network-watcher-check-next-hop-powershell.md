---
title: "Najít další segment s Azure sítě sledovacích procesů další segment – prostředí PowerShell | Microsoft Docs"
description: "Tento článek popisuje, jak můžete najít, co je typ dalšího směrování a ip adresu pomocí dalšího přechodu pomocí prostředí PowerShell."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 6a656c55-17bd-40f1-905d-90659087639c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ef559fbbd3e8448d64167552cacee04790418343
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-powershell"></a>Zjistěte, co typ dalšího směrování je pomocí funkce další směrování v sledovací proces sítě Azure pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> - [portál Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Rozhraní API Azure REST](network-watcher-check-next-hop-rest.md)

Další směrování je funkce sledovací proces sítě, která poskytuje možnost get typ dalšího směrování a IP adresy, které jsou založené na zadaný virtuální počítač. Tato funkce je užitečná při určování, zda prochází odchozího provozu z virtuálního počítače brány, internet nebo virtuální sítě získat do cíle.

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři budete používat portál Azure se najít typ dalšího směrování a IP adresu.

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě. Tento scénář také předpokládá, že skupina prostředků se platný virtuální počítač existuje má být použit.

## <a name="scenario"></a>Scénář

Scénář popsaná v tomto článku používá další směrování, funkce sledovací proces sítě, který vyhledá typ dalšího směrování a IP adresu pro prostředek. Další informace o další směrování, navštivte [další směrování přehled](network-watcher-next-hop-overview.md).

## <a name="retrieve-network-watcher"></a>Načtení sledovací proces sítě

Prvním krokem je pro získání instance sledovací proces sítě. `$networkWatcher` Dalšího směrování je předán proměnná ověřte rutiny.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-virtual-machine"></a>Získat virtuální počítač

Další směrování vrátí dalšího směrování a IP adresu dalšího přechodu z virtuálního počítače. Id virtuálního počítače je vyžadováno pro rutinu. Pokud už znáte ID virtuálního počítače používat, můžete tento krok přeskočit.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

> [!NOTE]
> Další směrování vyžaduje, aby prostředků virtuálního počítače je přidělená ke spuštění.

## <a name="get-the-network-interfaces"></a>Získat rozhraní sítě

IP adresa síťového adaptéru na virtuálním počítači je potřeba v tomto příkladu, nemůžeme načíst síťové adaptéry na virtuálním počítači. Pokud už znáte IP adresu, která chcete testovat na virtuálním počítači, můžete tento krok přeskočit.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="get-next-hop"></a>Získat další směrování

Teď říkáme `Get-AzureRmNetworkWatcherNextHop` rutiny. Jsme předat rutinu sledovací proces sítě, virtuální počítač Id, zdrojové IP adresy a cílové IP adresy. V tomto příkladu je cílovou IP adresu pro virtuální počítač v jiné virtuální síti. Mezi dvě virtuální sítě je bránu virtuální sítě.

```powershell
Get-AzureRmNetworkWatcherNextHop -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id -SourceIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress  -DestinationIPAddress 10.0.2.4 
```

## <a name="review-results"></a>Kontrola výsledků

Po dokončení, jsou uvedené výsledky. IP adresa dalšího směrování je vrácen a také typ prostředku, který je. V tomto scénáři je veřejnou IP adresu brány virtuální sítě.

```
NextHopIpAddress NextHopType           RouteTableId 
---------------- -----------           ------------ 
13.78.238.92     VirtualNetworkGateway Gateway Route
```

V následujícím seznamu jsou aktuálně dostupné hodnoty NextHopType:

**Typ dalšího směrování**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Žádné

## <a name="next-steps"></a>Další kroky

Zjistěte, jak zkontrolovat nastavení skupiny zabezpečení sítě prostřednictvím kódu programu, navštivte stránky [NSG auditování s sledovací proces sítě](network-watcher-nsg-auditing-powershell.md)

















