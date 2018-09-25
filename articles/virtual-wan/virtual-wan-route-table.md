---
title: Vytvoření Azure virtuální sítě WAN virtuální rozbočovač směrovací tabulky řídit síťové virtuální zařízení | Dokumentace Microsoftu
description: Tabulka směrování virtuální sítě WAN virtuální rozbočovač řídit provoz do síťového virtuálního zařízení.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: 56321c7c2239c0f1f0e73126d90a521e3cafb853
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984187"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Vytvoření směrovací tabulky virtuální rozbočovač řídit provoz do síťového virtuálního zařízení

Tento článek popisuje, jak řídit provoz z virtuální rozbočovač do síťového virtuálního zařízení. 

![Diagram virtuální sítě WAN](./media/virtual-wan-route-table/vwanroute.png)

V tomto článku se dozvíte jak:

* Vytvoření sítě WAN
* Vytvoření rozbočovače
* Vytvoření připojení virtuální sítě centra
* Vytvořte trasu rozbočovače
* Vytvoření směrovací tabulky
* Použití směrovací tabulky

## <a name="before-you-begin"></a>Než začnete

Ověřte, že splňujete následující kritéria:

1. Máte síťové virtuální zařízení (NVA) se softwarem třetích stran podle vašeho výběru, který je obvykle zřízený z Azure Marketplace (odkaz) ve virtuální síti.
2. Máte privátní IP adresa přiřazená síťovému rozhraní síťového virtuálního zařízení. 
3. Virtuální rozbočovač se nedá nasadit síťové virtuální zařízení. Musí být nasazeny v samostatné virtuální sítě. Pro účely tohoto článku virtuální sítě se označuje jako "Virtuální síť DMZ".
4. "Virtuální síť DMZ" může mít jeden nebo mnoho virtuálních sítí k němu připojená. V tomto článku tuto virtuální síť se nazývá nepřímé paprsku virtuální sítě. Tyto virtuální sítě se dá propojit k virtuální síti DMZ pomocí VNet peering.
5. Ověřte, že máte 2 virtuální sítě už vytvořili. Ty se používají jako virtuální sítě paprsků. Pro účely tohoto článku se adresní prostory virtuální sítě paprsků 10.0.2.0/24 a 10.0.3.0/24. Pokud potřebujete informace o tom, jak vytvořit virtuální síť, přečtěte si téma [vytvoření virtuální sítě pomocí Powershellu](../virtual-network/quick-create-powershell.md).
6. Ujistěte se, že nejsou žádné brány virtuální sítě v žádné virtuální sítě.

## <a name="signin"></a>1. Přihlášení

Ujistěte se, že jste si nainstalovali nejnovější verzi rutin Powershellu pro Resource Manager. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). To je důležité, protože starší verze rutin neobsahují aktuální hodnoty, které potřebujete pro toto cvičení.

1. Otevřete konzolu Powershellu se zvýšenými oprávněními a přihlaste se ke svému účtu Azure. Tato rutina vás vyzve k zadání přihlašovacích údajů přihlásit. Po přihlášení se stáhne nastavení účtu, aby byly k dispozici pro prostředí Azure PowerShell.

  ```powershell
  Connect-AzureRmAccount
  ```
2. Načtěte seznam předplatných Azure.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Určete předplatné, které chcete použít.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```

## <a name="rg"></a>2. Vytvoření prostředků

1. Vytvořte skupinu prostředků.

  ```powershell
  New-AzureRmResourceGroup -Location "West US" -Name "testRG"
  ```
2. Vytvořte virtuální síť WAN.

  ```powershell
  $virtualWan = New-AzureRmVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
  ```
3. Vytvořte virtuální rozbočovač.

  ```powershell
  New-AzureRmVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24"
  ```

## <a name="connections"></a>3. Vytvoření připojení

Vytvořte Centrum připojení virtuální sítě z nepřímé virtuální sítě paprsků a virtuální síť DMZ virtuální rozbočovač.

  ```powershell
  $remoteVirtualNetwork1= Get-AzureRmVirtualNetwork -Name “indirectspoke1” -ResourceGroupName “testRG”
  $remoteVirtualNetwork2= Get-AzureRmVirtualNetwork -Name “indirectspoke2” -ResourceGroupName “testRG”
  $remoteVirtualNetwork3= Get-AzureRmVirtualNetwork -Name “dmzvnet” -ResourceGroupName “testRG”

  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection1” -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection2” -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection3” -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="route"></a>4. Vytvořit trasu virtuální rozbočovač

Pro účely tohoto článku nepřímé virtuální sítě paprsků adresních prostorů jsou 10.0.2.0/24 a 10.0.3.0/24 a síťové virtuální zařízení hraniční sítě síťové rozhraní privátní IP adresa je 10.0.4.5.

```powershell
$route1 = New-AzureRmVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="applyroute"></a>5. Vytvoření směrovací tabulky virtuální rozbočovač

Vytvoření směrovací tabulky virtuální rozbočovač a pak pro ni nastavit vytvořená trasa.
 
```powershell
$routeTable = New-AzureRmVirtualHubRouteTable -Route @($route1)
```

## <a name="commit"></a>6. Potvrzení změn

Potvrďte změny do virtuální rozbočovač.

```powershell
Set-AzureRmVirtualHub -VirtualWanId $virtualWan.Id -ResourceGroupName "testRG" -Name "westushub” -RouteTable $routeTable
```

## <a name="cleanup"></a>Vyčištění prostředků

Pokud už tyto prostředky nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Položku myResourceGroup nahraďte názvem vaší skupiny prostředků a spusťte následující příkaz PowerShellu:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).