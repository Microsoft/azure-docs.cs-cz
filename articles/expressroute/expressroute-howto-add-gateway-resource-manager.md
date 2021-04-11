---
title: 'Kurz – Azure ExpressRoute: přidání brány do virtuální sítě – Azure PowerShell'
description: Tento kurz vám pomůže přidat bránu virtuální sítě do již vytvořené Správce prostředků VNet pro ExpressRoute pomocí Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: c2fa1441f5b7d8af4d610c5341f60009e57173af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560943"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Kurz: Konfigurace brány virtuální sítě pro ExpressRoute pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Správce prostředků – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasický – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Tento kurz vám pomůže přidat, změnit jeho velikost a odebrat bránu virtuální sítě (VNet) pro již existující virtuální síť. Kroky pro tuto konfiguraci platí pro virtuální sítě, které byly vytvořeny pomocí modelu nasazení Správce prostředků pro konfiguraci ExpressRoute. Další informace najdete v tématu [informace o branách virtuální sítě pro ExpressRoute](expressroute-about-virtual-network-gateways.md).

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Vytvořte podsíť brány.
> - Vytvořte bránu Virtual Network.

## <a name="prerequisites"></a>Požadavky

### <a name="configuration-reference-list"></a>Seznam odkazů konfigurace

Postup pro tuto úlohu používá virtuální síť na základě hodnot v následujícím seznamu odkazů konfigurace. Další nastavení a názvy jsou také popsány v tomto seznamu. Tento seznam nepoužíváme přímo v žádném z kroků, i když přidáváme proměnné na základě hodnot v tomto seznamu. Seznam můžete zkopírovat pro použití jako odkaz a nahradit hodnoty vlastními.

| Nastavení                   | Hodnota                                              |
| ---                       | ---                                                |
| Název Virtual Network | *TestVNet* |    
| Virtual Network adresní prostor | *192.168.0.0/16* |
| Skupina prostředků | *TestRG* |
| Název Subnet1 | *FrontEnd* |   
| Adresní prostor Subnet1 | *192.168.1.0/24* |
| Název Subnet1 | *FrontEnd* |
| Název podsítě brány | *GatewaySubnet* |    
| Adresní prostor podsítě brány | *192.168.200.0/26* |
| Oblast | *East US* |
| Název brány | *GW* |   
| Název IP brány | *GWIP* |
| Název konfigurace IP adresy brány | *gwipconf* |
| Typ | *ExpressRoute* |
| Název veřejné IP adresy brány  | *gwpip* |

> [!IMPORTANT]
> Podpora protokolu IPv6 pro soukromý partnerský vztah je v současnosti v **Public Preview**. Pokud chcete připojit virtuální síť k okruhu ExpressRoute pomocí nakonfigurovaného privátního partnerského vztahu založeného na protokolu IPv6, ujistěte se, že je vaše virtuální síť duální Stack, a postupujte podle pokynů popsaných [tady](../virtual-network/ipv6-overview.md).
> 
> 

## <a name="add-a-gateway"></a>Přidání brány

1. Pokud se chcete připojit k Azure, spusťte `Connect-AzAccount` .

1. Deklarujte proměnné pro toto cvičení. Nezapomeňte vzorek upravit tak, aby odrážel nastavení, které chcete použít.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
1. Uložte objekt virtuální sítě jako proměnnou.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
1. Přidejte podsíť brány do svého Virtual Network. Podsíť brány musí mít název "GatewaySubnet". Podsíť brány musí být/27 nebo větší (/26,/25 atd.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
    Pokud používáte virtuální síť Dual stack a naplánujete použití privátního partnerského vztahu založeného na protokolu IPv6 přes ExpressRoute, vytvořte místo toho podsíť brány Dual Stack.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26","ace:daa:daaa:deaa::/64"
   ```
1. Nastavte konfiguraci.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
1. Uložte podsíť brány jako proměnnou.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
1. Vyžádejte si veřejnou IP adresu. IP adresa se požaduje před vytvořením brány. Nemůžete zadat IP adresu, kterou chcete použít. dynamicky se přiřazuje. Tuto IP adresu použijete v části s další konfigurací. Element allocationmethod musí být dynamické.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
1. Vytvořte konfiguraci pro bránu. Konfigurace brány definuje podsíť a veřejnou IP adresu, která se bude používat. V tomto kroku zadáte konfiguraci, která se použije při vytváření brány. Podle následující ukázky vytvořte vlastní konfiguraci brány.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
1. Vytvořte bránu. V tomto kroku je **GatewayType** zvláště důležité. Je nutné použít hodnotu **ExpressRoute**. Po spuštění těchto rutin může Brána trvat 45 minut nebo i více, než se vytvoří.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```
> [!IMPORTANT]
> Pokud máte v úmyslu používat privátní partnerský vztah založený na protokolu IPv6 přes ExpressRoute, nezapomeňte vybrat AZ SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) for **-GatewaySku**.
> 
> 

## <a name="verify-the-gateway-was-created"></a>Ověření, že se brána vytvořila
Pomocí následujících příkazů ověřte, zda byla brána vytvořena:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Změna velikosti brány
Existuje několik [SKU brány](expressroute-about-virtual-network-gateways.md). K změně SKU brány můžete použít následující příkaz.

> [!IMPORTANT]
> Tento příkaz nefunguje pro bránu UltraPerformance. Pokud chcete bránu změnit na bránu UltraPerformance, odeberte nejdřív existující bránu ExpressRoute a pak vytvořte novou bránu UltraPerformance. Pokud chcete bránu downgradovat z brány UltraPerformance, nejdřív odeberte bránu UltraPerformance a pak vytvořte novou bránu.
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Bránu odeberete pomocí následujícího příkazu:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

## <a name="next-steps"></a>Další kroky
Po vytvoření brány virtuální sítě můžete připojit virtuální síť k okruhu ExpressRoute. 

> [!div class="nextstepaction"]
> [Propojení Virtual Network k okruhu ExpressRoute](expressroute-howto-linkvnet-arm.md)