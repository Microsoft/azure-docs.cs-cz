---
title: 'Brána Azure VPN: nastavení konfigurace'
description: Přečtěte si o nastavení brány VPN pro brány virtuálnísítě Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: d7a2040748d170b4e536df59947ea811f149d931
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244859"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Nastavení konfigurace brány VPN

Brána VPN je typ brány virtuální sítě, která odesílá šifrovaný provoz mezi vaší virtuální sítí a místním umístěním prostřednictvím veřejného připojení. Bránu VPN můžete taky použít k odesílání přenosů mezi virtuálními sítěmi přes páteřní síť Azure.

Připojení brány VPN závisí na konfiguraci více prostředků, z nichž každý obsahuje konfigurovatelné nastavení. Části v tomto článku popisují prostředky a nastavení, které se vztahují k bráně VPN pro virtuální síť vytvořenou v modelu nasazení Resource Manageru. Popisy a diagramy topologie pro každé řešení připojení najdete v článku [O bráně VPN.](vpn-gateway-about-vpngateways.md)

Hodnoty v tomto článku platí brány VPN (brány virtuální sítě, které používají -GatewayType Vpn). Tento článek nezahrnuje všechny typy bran nebo zónově redundantní brány.

* Hodnoty, které platí pro -GatewayType 'ExpressRoute', naleznete [v tématu Brány virtuální sítě pro ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

* Informace o zónově redundantních branách najdete v tématu [O zónově redundantních branách](about-zone-redundant-vnet-gateways.md).

* Informace o virtuální mase WAN naleznete [v tématu O virtuální matné wan](../virtual-wan/virtual-wan-about.md).



## <a name="gateway-types"></a><a name="gwtype"></a>Typy bran

Každá virtuální síť může mít jenom jednu bránu virtuální sítě každého typu. Při vytváření brány virtuální sítě, musíte se ujistit, že typ brány je správné pro vaši konfiguraci.

Dostupné hodnoty pro -GatewayType jsou:

* Vpn
* ExpressRoute

Brána VPN `-GatewayType` vyžaduje *vpn*.

Příklad:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gateway-skus"></a><a name="gwsku"></a>SKU brány

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Konfigurace skladové položky brány

#### <a name="azure-portal"></a>portál Azure

Pokud použijete portál Azure k vytvoření brány virtuální sítě Správce prostředků, můžete vybrat skladovou položku brány pomocí rozevíracího programu. Možnosti, které se zobrazí, odpovídají typu brány a typu VPN, které vyberete.

#### <a name="powershell"></a>PowerShell

Následující příklad prostředí PowerShell `-GatewaySku` určuje jako VpnGw1. Při použití Prostředí PowerShell k vytvoření brány musíte nejprve vytvořit konfiguraci IP adresy a pak použít proměnnou, která na ni odkazuje. V tomto příkladu je konfigurační proměnná $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizing-or-changing-a-sku"></a><a name="resizechange"></a>Změna velikosti nebo změny skladové položky

Pokud máte bránu VPN a chcete použít jinou skladovou položku brány, máte možnost změnit velikost skladové položky brány nebo změnit na jinou skladovou položku. Když změníte na jinou bránu Skladové položky, odstraníte existující bránu úplně a vytvořit novou. Sestavení brány může trvat až 45 minut. Ve srovnání s tím, když změníte velikost skladové položky brány, není mnoho prostojů, protože není nutné odstranit a znovu vytvořit bránu. Pokud máte možnost změnit velikost skladové položky brány, nikoli ji změnit, budete to chtít udělat. Existují však pravidla pro změna velikosti:

1. S výjimkou základní skladové položky můžete změnit velikost skladové položky brány VPN na jinou skladovou položku brány VPN v rámci stejné generace (Generace1 nebo Generace2). Například VpnGw1 generation1 může být velikost na VpnGw2 generation1, ale ne na VpnGw2 generation2.
2. Pokud používáte staré SKU brány, můžete měnit velikost mezi Basic, Standard a HighPerformance SKU.
3. Nelze **cannot** změnit velikost ze základních/standardních/vysoce výkonných skum na vpngw sku- sku. Místo toho je nutné [změnit](#change) na nové sku.

#### <a name="to-resize-a-gateway"></a><a name="resizegwsku"></a>Změna velikosti brány

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="to-change-from-an-old-legacy-sku-to-a-new-sku"></a><a name="change"></a>Změna ze staré (starší) skladové položky na novou skladovou položku

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connection-types"></a><a name="connectiontype"></a>Typy připojení

V modelu nasazení Správce prostředků každá konfigurace vyžaduje konkrétní typ připojení brány virtuální sítě. Dostupné hodnoty prostředí PowerShell v Resource Manageru pro `-ConnectionType` jsou:

* Protokol IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

V následujícím příkladu prostředí PowerShell vytvoříme připojení S2S, které vyžaduje typ připojení *IPsec*.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpn-types"></a><a name="vpntype"></a>Typy sítě VPN

Při vytváření brány virtuální sítě pro konfiguraci brány VPN je nutné zadat typ sítě VPN. Typ sítě VPN, který zvolíte, závisí na topologii připojení, kterou chcete vytvořit. Například připojení P2S vyžaduje typ vpn RouteBased. Typ VPN může také záviset na hardwaru, který používáte. Konfigurace S2S vyžadují zařízení VPN. Některá zařízení VPN podporují pouze určitý typ VPN.

Vybraný typ sítě VPN musí splňovat všechny požadavky na připojení pro řešení, které chcete vytvořit. Chcete-li například vytvořit připojení brány VPN S2S a připojení brány VPN P2S pro stejnou virtuální síť, použijte typ VPN *RouteBased,* protože P2S vyžaduje typ vpn RouteBased. Budete také muset ověřit, že vaše zařízení VPN podporuje připojení RouteBased VPN. 

Po vytvoření brány virtuální sítě nelze změnit typ VPN. Musíte odstranit bránu virtuální sítě a vytvořit novou. Existují dva typy sítě VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Následující příklad prostředí PowerShell `-VpnType` určuje jako *RouteBased*. Při vytváření brány se musíte ujistit, že parametr -VpnType odpovídá vaší konfiguraci.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="gateway-requirements"></a><a name="requirements"></a>Požadavky na bránu

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gateway-subnet"></a><a name="gwsub"></a>Podsíť brány

Před vytvořením brány VPN je nutné vytvořit podsíť brány. Podsíť brány obsahuje IP adresy, které virtuální počítače a služby brány virtuální sítě používají. Když vytvoříte bránu virtuální sítě, virtuální počítače brány se nasadí do podsítě brány a nakonfigurují je s požadovaným nastavením brány VPN. Nikdy nenasazujte nic jiného (například další virtuální počítače) do podsítě brány. Aby fungovala podsíť brány, musí být pojmenována GatewaySubnet. Pojmenování podsítě brány "GatewaySubnet" umožňuje Azure vědět, že se jedná o podsíť pro nasazení virtuálních počítačů a služeb brány virtuální sítě virtuální sítě virtuální sítě.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Při vytváření podsítě brány zadáte počet IP adres, které podsíť obsahuje. IP adresy v podsíti brány jsou přiděleny virtuálním počítačům brány a službám brány. Některé konfigurace vyžadují víc IP adres než jiné. 

Při plánování velikosti podsítě brány se podívejte do dokumentace ke konfiguraci, kterou plánujete vytvořit. Například konfigurace expressroute/VPN gateway vyžaduje větší podsíť brány než většina ostatních konfigurací. Kromě toho můžete chtít, aby vaše podsíť brány obsahuje dostatek IP adres pro případné budoucí další konfigurace. Zatímco můžete vytvořit podsíť brány tak malé jako /29, doporučujeme vytvořit podsíť brány /27 nebo větší (/27, /26 atd.), pokud máte k dispozici adresní prostor k tomu. To bude vyhovovat většině konfigurací.

Následující příklad prostředí Resource Manager PowerShell zobrazuje podsíť brány s názvem GatewaySubnet. Můžete vidět, že zápis CIDR určuje /27, což umožňuje dostatek IP adres pro většinu konfigurací, které v současné době existují.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="local-network-gateways"></a><a name="lng"></a>Brány místní sítě

 Brána místní sítě se liší od brány virtuální sítě. Při vytváření konfigurace brány VPN místní síťová brána obvykle představuje vaše místní umístění. V modelu nasazení Classic se brána místní sítě označovala jako „místní lokalita“.

Přidělíte místní síťové bráně název, veřejnou IP adresu místního zařízení VPN a zadáte předpony adres, které jsou umístěny v místním umístění. Azure vyhledá předpony cílové adresy pro síťový provoz, nahlíží do konfigurace, kterou jste zadali pro bránu místní sítě, a odpovídajícím způsobem směruje pakety. Můžete také zadat brány místní sítě pro konfigurace virtuální sítě na virtuální síť, které používají připojení brány VPN.

Následující příklad prostředí PowerShell vytvoří novou bránu místní sítě:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Někdy je třeba upravit nastavení brány místní sítě. Například při přidání nebo úpravě rozsahu adres nebo při změně ip adresy zařízení VPN. Viz [Úprava nastavení brány místní sítě pomocí prostředí PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="rest-apis-powershell-cmdlets-and-cli"></a><a name="resources"></a>REST API, rutiny prostředí PowerShell a cli

Další technické prostředky a specifické požadavky na syntaxi při použití rozhraní REST API, rutin prostředí PowerShell nebo rozhraní API Azure pro konfigurace brány VPN najdete na následujících stránkách:

| **Classic** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/az.network/#networking) |[PowerShell](/powershell/module/az.network#vpn) |
| [ROZHRANÍ API PRO ODPOČINEK](https://msdn.microsoft.com/library/jj154113) |[ROZHRANÍ API PRO ODPOČINEK](/rest/api/network/virtualnetworkgateways) |
| Nepodporuje se | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Další kroky

Další informace o dostupných konfiguracích připojení naleznete [v tématu O bráně VPN](vpn-gateway-about-vpngateways.md).
