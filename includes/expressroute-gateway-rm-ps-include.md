---
title: zahrnout soubor
description: zahrnout soubor
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/22/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7e33d4ed7100287ef6b22aa4c90fd52671363902
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31613615"
---
Kroky pro tuto úlohu použijte virtuální sítě na základě hodnot v následujícím seznamu odkaz konfigurace. Názvy a další nastavení jsou také popsány v tomto seznamu. Tento seznam nepoužívá přímo v žádném z kroků, i když přidáme proměnné na základě hodnot v tomto seznamu. Seznam, aby používal jako odkaz, můžete zkopírovat nahraďte hodnoty vlastními.

**Seznam odkazů konfigurace**

* Název virtuální sítě = "TestVNet"
* Virtuální adresní prostor sítě = 192.168.0.0/16
* Skupina prostředků = "TestRG"
* Subnet1 Name = "FrontEnd" 
* Adresní prostor Subnet1 = "192.168.1.0/24"
* Název podsítě brány: "GatewaySubnet" název musí být vždy podsíť brány *GatewaySubnet*.
* Adresního prostoru podsítě brány = "192.168.200.0/26"
* Oblast = "East US"
* Název brány = "GW"
* Název brány IP = "GWIP"
* Konfigurace IP brány Name = "gwipconf"
* Typ = "ExpressRoute" Tento typ je požadovaná konfigurace ExpressRoute.
* Název veřejné IP adresy brány = "gwpip"

## <a name="add-a-gateway"></a>Přidat bránu
1. Připojení k předplatnému Azure.

  ```powershell 
  Connect-AzureRmAccount
  Get-AzureRmSubscription 
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Deklarace proměnných pro toto cvičení. Nezapomeňte upravit ukázku nastavení, které chcete použít.

  ```powershell 
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "GW"
  $GWIPName = "GWIP"
  $GWIPconfName = "gwipconf"
  $VNetName = "TestVNet"
  ```
3. Uložte objekt virtuální sítě jako proměnnou.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  ```
4. Přidáte podsíť brány k virtuální síti. Podsíť brány musí mít název "GatewaySubnet". Měli byste vytvořit podsíť brány, který je/27 nebo větší (/ 26, / 25 atd.).

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
  ```
5. Nastavte konfiguraci.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Uložte jako proměnnou a podsíť brány.

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
  ```
7. Vyžádejte si veřejnou IP adresu. Před vytvořením brány je požadována adresa IP. Nelze zadat IP adresu, která chcete použít; se přidělí dynamicky. Tuto IP adresu použijete v části s další konfigurací. AllocationMethod musí být dynamické.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  ```
8. Vytvoření konfigurace pro bránu. Konfigurace brány definuje podsíť a veřejnou IP adresu, která se bude používat. V tomto kroku určíte konfigurace, který se použije při vytváření brány. Tento krok není ve skutečnosti vytvořit objekt brány. Podle následující ukázky vytvořte vlastní konfiguraci brány.

  ```powershell
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```
9. Vytvoření brány. V tomto kroku **- GatewayType** je obzvláště důležité. Musíte použít hodnotu **ExpressRoute**. Po spuštění těchto rutin, může trvat brány 45 minut nebo déle vytvořit.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
  ```

## <a name="verify-the-gateway-was-created"></a>Ověřte, že vytvoření brány
Chcete-li ověřit, zda byl vytvořen brány použijte následující příkazy:

```powershell
Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Změnit velikost brány
Existuje řada [SKU brány](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Tento příkaz můžete kdykoli změnit skladová položka brány.

> [!IMPORTANT]
> Tento příkaz nefunguje pro UltraPerformance bránu. Chcete-li změnit bránu pro bránu UltraPerformance, nejprve odeberte existující bránu ExpressRoute a poté vytvořit novou bránu UltraPerformance. Přejít na starší verzi bránu z bránu UltraPerformance, nejprve odeberte UltraPerformance brány a poté vytvořit novou bránu.
> 
> 

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Odebrat bránu
Chcete-li odebrat bránu, použijte následující příkaz:

```powershell
Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
