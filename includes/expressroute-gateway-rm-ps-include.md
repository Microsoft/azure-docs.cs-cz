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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175540"
---
Postup pro tuto úlohu používá virtuální síť na základě hodnot v následujícím seznamu odkazů konfigurace. Další nastavení a názvy jsou také popsány v tomto seznamu. Tento seznam nepoužíváme přímo v žádném z kroků, i když přidáváme proměnné na základě hodnot v tomto seznamu. Seznam můžete zkopírovat pro použití jako odkaz a nahradit hodnoty vlastními.

* Virtual Network název = "TestVNet"
* Virtual Network adresního prostoru = 192.168.0.0/16
* Skupina prostředků = "TestRG"
* Subnet1 název = "front-end" 
* Subnet1 adresní prostor = "192.168.1.0/24"
* Název podsítě brány: "GatewaySubnet" musíte vždycky pojmenovat podsíť brány *GatewaySubnet*.
* Adresní prostor podsítě brány = "192.168.200.0/26"
* Region = "Východní USA"
* Název brány = "GS"
* Název brány IP = "GWIP"
* Název konfigurace IP adresy brány = "gwipconf"
* Type = "ExpressRoute" Tento typ je vyžadován pro konfiguraci ExpressRoute.
* Název veřejné IP adresy brány = "gwpip"

## <a name="add-a-gateway"></a>Přidání brány
1. Připojte se k předplatnému Azure.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Deklarujte proměnné pro toto cvičení. Nezapomeňte vzorek upravit tak, aby odrážel nastavení, které chcete použít.

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
4. Přidejte podsíť brány do svého Virtual Network. Podsíť brány musí mít název "GatewaySubnet". Měli byste vytvořit podsíť brány, která je/27 nebo větší (/26,/25 atd.).

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
7. Vyžádejte si veřejnou IP adresu. IP adresa se požaduje před vytvořením brány. Nelze zadat IP adresu, kterou chcete použít. je dynamicky přidělena. Tuto IP adresu použijete v části s další konfigurací. Element allocationmethod musí být dynamické.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Vytvořte konfiguraci pro bránu. Konfigurace brány definuje podsíť a veřejnou IP adresu, která se bude používat. V tomto kroku zadáváte konfiguraci, která se použije při vytváření brány. Tento krok ve skutečnosti nevytváří objekt brány. Podle následující ukázky vytvořte vlastní konfiguraci brány.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Vytvořte bránu. V tomto kroku je **GatewayType** zvláště důležité. Je nutné použít hodnotu **ExpressRoute**. Po spuštění těchto rutin může Brána trvat 45 minut nebo i více, než se vytvoří.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Ověření, že se brána vytvořila
Pomocí následujících příkazů ověřte, zda byla brána vytvořena:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Změna velikosti brány
Existuje několik [SKU brány](../articles/expressroute/expressroute-about-virtual-network-gateways.md). K změně SKU brány můžete použít následující příkaz.

> [!IMPORTANT]
> Tento příkaz nefunguje pro bránu UltraPerformance. Pokud chcete bránu změnit na bránu UltraPerformance, odeberte nejdřív existující bránu ExpressRoute a pak vytvořte novou bránu UltraPerformance. Pokud chcete bránu downgradovat z brány UltraPerformance, nejdřív odeberte bránu UltraPerformance a pak vytvořte novou bránu.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Odebrání brány
Chcete-li odebrat bránu, použijte následující příkaz:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
