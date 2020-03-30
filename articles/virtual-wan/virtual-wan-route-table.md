---
title: 'Virtuální WAN: Vytvoření tabulky směrování virtuálního rozbočovače do NVA: Azure PowerShell'
description: Virtuální wan virtuální rozbočovací tabulka směrovat provoz do sítě virtuálního zařízení.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: a55e1453fe7fe4d135286b22dabf58d434762581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645102"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Vytvoření směrovací tabulky virtuálního centra pro řízení provozu na síťové virtuální zařízení

Tento článek ukazuje, jak řídit provoz z virtuálního rozbočovače do síťového virtuálního zařízení. 

![Diagram virtuální sítě WAN](./media/virtual-wan-route-table/vwanroute.png)

V tomto článku získáte informace o těchto tématech:

* Vytvoření sítě WAN
* Vytvoření rozbočovače
* Vytvoření připojení virtuální sítě rozbočovače
* Vytvoření trasy rozbočovače
* Vytvoření směrovací tabulky
* Použití tabulky tras

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ověřte, zda jste splnili následující kritéria:

1. Máte síťové virtuální zařízení (NVA). Jedná se o software jiného výrobce podle vašeho výběru, který se obvykle zřizoval z Azure Marketplace ve virtuální síti.
2. K síťovému rozhraní síťového virtuálního zařízení je přiřazena privátní adresa IP. 
3. NVA nelze nasadit ve virtuálním rozbočovači. Musí být nasazený v samostatné virtuální síti. Pro tento článek virtuální síť NVA se označuje jako "Virtuální síť DMZ".
4. "Virtuální síť DMZ" může mít jednu nebo mnoho virtuálních sítí, které jsou k němu připojeny. V tomto článku se tato virtuální síť označuje jako "Virtuální síť s nepřímým paprskem". Tyto virtuální sítě se můžou připojit k virtuální síti DMZ pomocí partnerského vztahu virtuální sítě.
5. Ověřte, že už máte 2 virtuální sítě. Ty se budou používat jako virtuální sítě paprsku. Pro tento článek jsou adresní prostory virtuální sítě pro paprsky 10.0.2.0/24 a 10.0.3.0/24. Pokud potřebujete informace o tom, jak vytvořit virtuální síť, přečtěte si informace [o vytvoření virtuální sítě pomocí PowerShellu](../virtual-network/quick-create-powershell.md).
6. Ujistěte se, že v žádné virtuální sítě nejsou žádné brány virtuální sítě.

## <a name="1-sign-in"></a><a name="signin"></a>1. Přihlaste se

Ujistěte se, že jste nainstalovali nejnovější verzi rutin prostředí PowerShell správce prostředků. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-az-ps). To je důležité, protože starší verze rutin neobsahují aktuální hodnoty, které potřebujete pro toto cvičení.

1. Otevřete konzolu PowerShell se zvýšenými oprávněními a přihlaste se ke svému účtu Azure. Tato rutina vás vyzve k zadání přihlašovacích údajů. Po přihlášení stáhne nastavení vašeho účtu, aby byly dostupné pro Azure PowerShell.

   ```powershell
   Connect-AzAccount
   ```
2. Načtěte seznam předplatných Azure.

   ```powershell
   Get-AzSubscription
   ```
3. Určete předplatné, které chcete použít.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="2-create-resources"></a><a name="rg"></a>2. Vytváření zdrojů

1. Vytvořte skupinu prostředků.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Vytvořte virtuální WAN.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Vytvořte virtuální rozbočovač.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3. Vytvoření připojení

Vytvořte připojení virtuální sítě rozbočovače z virtuální sítě Nepřímý paprsek a virtuální sítě DMZ do virtuálního rozbočovače.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4. Vytvoření trasy virtuálního rozbočovače

Pro tento článek jsou adresní prostory virtuální sítě Nepřímý paprsek 10.0.2.0/24 a 10.0.3.0/24 a privátní IP adresa síťového rozhraní NVA DMZ je 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5. Vytvoření směrovací tabulky virtuálního rozbočovače

Vytvořte tabulku směrování virtuálního rozbočovače a potom na ni použijte vytvořenou trasu.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6. Potvrzení změn

Potvrdí změny do virtuálního rozbočovače.

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).
