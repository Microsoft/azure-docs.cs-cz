---
title: 'Virtuální síť WAN: vytvoření tabulky směrování virtuálního rozbočovače do síťové virtuální zařízení: Azure PowerShell'
description: Tabulka směrování virtuálních rozbočovačů sítě WAN pro řízení provozu do síťového virtuálního zařízení.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 6a6e701377956e39696567eff9a6a0abca927b88
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055072"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Vytvoření směrovací tabulky virtuálního centra pro řízení provozu do síťového virtuálního zařízení

V tomto článku se dozvíte, jak řídit provoz z virtuálního rozbočovače do síťového virtuálního zařízení. 

![Diagram virtuální sítě WAN](./media/virtual-wan-route-table-nva/vwanroute.png)

V tomto článku získáte informace o těchto tématech:

* Vytvoření sítě WAN
* Vytvoření rozbočovače
* Vytvoření připojení k virtuální síti centra
* Vytvoření trasy centra
* Vytvoření směrovací tabulky
* Použít směrovací tabulku

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ověřte, že splňujete následující kritéria:

* Máte síťové virtuální zařízení (síťové virtuální zařízení). Toto je software od jiného výrobce, který je obvykle zřízený z Azure Marketplace ve virtuální síti.
* Máte přiřazenou privátní IP adresu k síťové virtuální zařízení síťovému rozhraní. 
* SÍŤOVÉ virtuální zařízení nejde nasadit ve virtuálním centru. Musí být nasazené v samostatné virtuální síti. V tomto článku se virtuální síť síťové virtuální zařízení označuje jako DMZ VNet.
* K virtuálním sítím DMZ je možné připojit jednu nebo více virtuálních sítí. V tomto článku se tato virtuální síť označuje jako "nepřímá virtuální síť rozbočovače". Tyto virtuální sítě můžou být připojené k virtuální síti DMZ pomocí partnerského vztahu virtuálních sítí.
* Ověřte, že už máte vytvořené 2 virtuální sítě. Budou použity jako paprskový virtuální sítě. V tomto článku jsou adresní prostory ve virtuální síti 10.0.2.0/24 a 10.0.3.0/24. Pokud potřebujete informace o tom, jak vytvořit virtuální síť, přečtěte si téma [vytvoření virtuální sítě pomocí PowerShellu](../virtual-network/quick-create-powershell.md).
* Zajistěte, aby v žádném virtuální sítě neexistovaly žádné brány virtuální sítě.

## <a name="1-sign-in"></a><a name="signin"></a>1. přihlášení

Ujistěte se, že instalujete nejnovější verzi rutin Správce prostředků PowerShellu. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-az-ps). To je důležité, protože starší verze rutin neobsahují aktuální hodnoty, které potřebujete pro toto cvičení.

1. Otevřete konzolu PowerShellu se zvýšenými oprávněními a přihlaste se ke svému účtu Azure. Tato rutina vás vyzve k zadání přihlašovacích údajů. Po přihlášení se stáhne nastavení účtu, aby bylo možné Azure PowerShell.

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

## <a name="2-create-resources"></a><a name="rg"></a>2. vytváření prostředků

1. Vytvořte skupinu prostředků.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Vytvořte virtuální síť WAN.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Vytvořte virtuální rozbočovač.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3. vytváření připojení

Vytvořte připojení k virtuální síti rozbočovače z nepřímé virtuální sítě a virtuální sítě DMZ k virtuálnímu rozbočovači.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4. vytvoření trasy virtuálního rozbočovače

V tomto článku jsou adresní prostory nepřímých virtuálních sítí 10.0.2.0/24 a 10.0.3.0/24 a privátní IP adresa síťového rozhraní DMZ síťové virtuální zařízení je 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5. vytvoření tabulky směrování virtuálního rozbočovače

Vytvořte tabulku směrování virtuálního rozbočovače a pak na ni použijte vytvořenou trasu.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6. potvrďte změny.

Potvrďte změny ve virtuálním centru.

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).
