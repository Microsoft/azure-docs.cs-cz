---
title: zahrnout soubor
description: zahrnout soubor
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922ac7eb6cb9676af65700a6a2fe7fbae35a0dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175540"
---
Kroky pro tuto úlohu používají virtuální síť na základě hodnot v následujícím seznamu odkazů na konfiguraci. V tomto seznamu jsou také uvedena další nastavení a názvy. Tento seznam nepoužíváme přímo v žádném z kroků, i když přidáváme proměnné na základě hodnot v tomto seznamu. Seznam můžete zkopírovat a použít jako odkaz a nahradit je vlastními hodnotami.

* Název virtuální sítě = "Testvnet"
* Adresní prostor virtuální sítě = 192.168.0.0/16
* Skupina prostředků = "TestRG"
* Název podsítě1 = "Front-end" 
* Adresní prostor podsítě1 = "192.168.1.0/24"
* Název podsítě brány: "GatewaySubnet" Vždy musíte pojmenovat gateway podsíť *GatewaySubnet*.
* Adresní prostor podsítě brány = "192.168.200.0/26"
* Region = "Východní USA"
* Název brány = "GW"
* Název IP brány = "GWIP"
* Název konfigurace IP brány = "gwipconf"
* Type = "ExpressRoute" Tento typ je vyžadován pro konfiguraci ExpressRoute.
* Název veřejné IP brány = "gwpip"

## <a name="add-a-gateway"></a>Přidání brány
1. Připojte se k předplatnému Azure.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Deklarujte proměnné pro toto cvičení. Nezapomeňte upravit ukázku tak, aby odrážela nastavení, která chcete použít.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. Uložte objekt virtuální sítě jako proměnnou.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Přidejte do virtuální sítě podsíť brány. Podsíť brány musí mít název "GatewaySubnet". Měli byste vytvořit podsíť brány, která je /27 nebo větší (/26, /25 atd.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Nastavte konfiguraci.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Uložte podsíť brány jako proměnnou.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Vyžádejte si veřejnou IP adresu. Ip adresa je požadována před vytvořením brány. Nelze zadat adresu IP, kterou chcete použít. je dynamicky alokována. Tuto IP adresu použijete v části s další konfigurací. AllocationMethod musí být dynamická.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Vytvořte konfiguraci brány. Konfigurace brány definuje podsíť a veřejnou IP adresu, která se bude používat. V tomto kroku určujete konfiguraci, která bude použita při vytváření brány. Tento krok ve skutečnosti nevytvoří objekt brány. Podle následující ukázky vytvořte vlastní konfiguraci brány.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Vytvořte bránu. V tomto kroku **-GatewayType** je zvláště důležité. Je nutné použít hodnotu **ExpressRoute**. Po spuštění těchto rutin může vytvoření brány trvat 45 minut nebo déle.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Ověření vytvoření brány
Pomocí následujících příkazů ověřte, zda byla brána vytvořena:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Změna velikosti brány
Existuje několik [skum brány](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Pomocí následujícího příkazu můžete kdykoli změnit skladovou položku brány.

> [!IMPORTANT]
> Tento příkaz nefunguje pro bránu UltraPerformance. Chcete-li změnit bránu na bránu UltraPerformance, nejprve odeberte existující bránu ExpressRoute a vytvořte novou bránu UltraPerformance. Chcete-li bránu snížit na nižší verzi z brány UltraPerformance, nejprve bránu UltraPerformance odeberte a pak vytvořte novou bránu.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Odebrání brány
K odebrání brány použijte následující příkaz:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
