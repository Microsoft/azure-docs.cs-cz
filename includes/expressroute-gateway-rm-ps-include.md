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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60366371"
---
Kroky pro tuto úlohu použijte na základě hodnot v seznamu následující odkaz Konfigurace virtuální sítě. Další nastavení a názvy jsou také popsány v tomto seznamu. I když můžeme přidat proměnné na základě hodnot v tomto seznamu, budeme není přímo v žádném z kroků, pomocí tohoto seznamu. Můžete zkopírovat seznam, aby používal jako odkaz, nahraďte hodnoty vlastními.

* Název virtuální sítě = "TestVNet"
* Adresní prostor virtuální sítě = 192.168.0.0/16
* Skupina prostředků = "TestRG"
* Subnet1 Name = "FrontEnd" 
* Subnet1 adresní prostor = "192.168.1.0/24"
* Název podsítě brány: "GatewaySubnet" je nutné vždy pojmenovat podsíť brány *GatewaySubnet*.
* Adresní prostor podsítě brány = "192.168.200.0/26"
* Oblast = "East US"
* Název brány = "GS"
* Název IP brány = "GWIP"
* Konfiguraci IP adresy brány Name = "gwipconf"
* Typ = "ExpressRoute" Tento typ je požadován pro konfiguraci služby ExpressRoute.
* Název veřejné IP brány = "gwpip"

## <a name="add-a-gateway"></a>Přidání brány
1. Připojte se ke svému předplatnému Azure.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Deklarujte proměnné pro účely tohoto cvičení. Nezapomeňte upravit vzorku tak, aby odrážely nastavení, které chcete použít.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. Store objekt virtuální sítě jako proměnnou.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Přidáte podsíť brány k virtuální síti. Podsíť brány musí mít název "GatewaySubnet". Měli byste vytvořit podsíť brány, která je/27 nebo větší (/ 26, / 25 atd.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Nastavte konfiguraci.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Podsíť brány Store jako proměnnou.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Vyžádejte si veřejnou IP adresu. IP adresa je požadována před vytvořením brány. Nelze zadat IP adresu, kterou chcete použít; se přidělí dynamicky. Tuto IP adresu použijete v části s další konfigurací. AllocationMethod musí být dynamické.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Vytvořte konfiguraci brány. Konfigurace brány definuje podsíť a veřejnou IP adresu, která se bude používat. V tomto kroku musíte zadat konfigurace, který se použije při vytváření brány. Tento krok nevytvoří objektu brány. Podle následující ukázky vytvořte vlastní konfiguraci brány.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Vytvoření brány. V tomto kroku **- GatewayType** je obzvláště důležité. Je nutné použít hodnotu **ExpressRoute**. Po spuštění těchto rutin, brány může trvat 45 minut nebo více otázek a vytvořit.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Ověřte, že vytvoření brány
Pokud chcete ověřit vytvoření brány použijte následující příkazy:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Změňte velikost brány
Několik položek [skladové položky brány](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Následující příkaz můžete kdykoli změnit SKU brány.

> [!IMPORTANT]
> Tento příkaz nefunguje pro brány UltraPerformance. Chcete-li změnit brána brány UltraPerformance, nejprve odeberte existující bránu ExpressRoute a pak vytvořte nové brány UltraPerformance. Přejít na nižší verzi z brány UltraPerformance bránu, nejprve odeberte brány UltraPerformance a pak vytvořit novou bránu.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Odebrání brány
Použijte následující příkaz k odebrání brány:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
