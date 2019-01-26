---
title: Diagnostika potíží se virtuální počítač sítě směrováním – Azure PowerShell | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak k diagnostice problému směrování sítě virtuálního počítače pomocí dalšího segmentu směrování funkce služby Azure Network Watcher.
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
ms.openlocfilehash: 81bbf2b69e0e492ea75e8cbbe980d7e83a86eae7
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54912846"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnostika potíží se virtuální počítač sítě směrováním – Azure PowerShell

V tomto článku nasazení virtuálního počítače (VM) a potom zkontrolujte komunikaci na IP adresu a adresu URL. Určíte příčinu selhání komunikace a najdete jeho řešení.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte modul AzureRM PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Než vytvoříte virtuální počítač, musíte vytvořit skupinu prostředků, která bude virtuální počítač obsahovat. Vytvořte skupinu prostředků pomocí rutiny [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Vytvořte virtuální počítač pomocí rutiny [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Při spuštění tohoto kroku se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač.

```azurepowershell-interactive
$vM = New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

Vytvoření virtuálního počítače trvá několik minut. Dokud se virtuální počítač nevytvoří a PowerShell nevrátí výstup, nepokračujte ve zbývajících krocích.

## <a name="test-network-communication"></a>Test síťové komunikace

Otestovat síťová komunikace pomocí služby Network Watcher, musíte nejprve povolit network watcher ve službě oblast, kterou virtuální počítač, který chcete testovat a pak použít další možnosti směrování ve službě Network Watcher k otestování komunikace.

## <a name="enable-network-watcher"></a>Povolení sledovacího procesu sítě

Pokud už máte sledovací proces sítě v oblasti USA – východ povolený, použijte rutinu [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher) a sledovací proces sítě načtěte. Následující příklad načte existující sledovací proces sítě s názvem *NetworkWatcher_eastus*, který se nachází ve skupině prostředků *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzureRmNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Pokud ještě sledovací proces sítě v oblasti USA – východ povolený nemáte, použijte rutinu [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) a sledovací proces sítě v oblasti USA – východ vytvořte:

```azurepowershell-interactive
$networkWatcher = New-AzureRmNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Použití dalšího směrování

Azure automaticky vytváří trasy pro výchozí cíle. Můžete vytvořit vlastní trasy, které přepíšou ty výchozí. Někdy můžou vlastní trasy způsobit selhání komunikace. K otestování směrování z virtuálního počítače, použijte [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) příkazu můžete určit další segment směrování, kdy je provoz určený pro konkrétní adresu.

Otestujte odchozí komunikaci z virtuálního počítače na jednu z IP adres stránky www.bing.com:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Po pár sekundách výstupu uvidíte, které **NextHopType** je **Internet**a že **RouteTableId** je **systémová trasa**. Tento výsledek vám umožňuje vědět, že je platná trasa do cíle.

Otestujte odchozí komunikaci z virtuálního počítače na IP adresu 172.31.0.100:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Výstupu vráceného vás informuje, který **žádný** je **NextHopType**a že **RouteTableId** je také **systémová trasa**. Tento výsledek říká, že i když existuje trasa systému do cíle, neexistuje žádné další směrování, které by do cíle směrovalo provoz.

## <a name="view-details-of-a-route"></a>Zobrazení podrobností o trase

Pokud chcete analyzovat další směrování, zkontrolujte efektivní trasy pro síťové rozhraní s [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) příkaz:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Se nevrátí výstup, který obsahuje následující text:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Jak je vidět ve výstupu předchozí, trasa **AddressPrefix** z **0.0.0.0/0** směruje veškerý provoz není určený pro adresy v rozsahu předpon adres jiné trasy s dalším segmentem směrování **Internet**. Jak je vidět také ve výstupu, i když je výchozí trasu k předponě 172.16.0.0/12, která zahrnuje 172.31.0.100 adresu, **nextHopType** je **žádný**. Azure vytváří výchozí trasu do 172.16.0.0/12, ale neurčuje typ dalšího směrování, dokud k tomu není důvod. Pokud například přidáte 172.16.0.0/12 rozsah adres do adresního prostoru virtuální sítě, Azure změní **nextHopType** k **virtuální síť** trasy. Pak by zobrazil kontrolu **virtuální síť** jako **nextHopType**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili virtuální počítač a diagnostikovat směrováním v síti z virtuálního počítače. Dozvěděli jste se, že Azure vytváří několik výchozích tras, a otestovali jste směrování na dva různé cíle. Přečtěte si i další informace o [směrování v Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a o tom, jak [vytvářet vlastní trasy](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Pro odchozí připojení virtuálního počítače, můžete také určit, latence a povolený a odepřen síťový provoz mezi virtuálním počítači a koncový bod pomocí Network Watcheru [řešení potíží s připojením](network-watcher-connectivity-powershell.md) funkce. Komunikace mezi virtuálním Počítačem a koncový bod, jako je například IP adresa nebo adresa URL, v čase pomocí funkce monitorování připojení Network Watcher můžete monitorovat. Další informace o postupu [monitorování připojení k síti](connection-monitor.md).