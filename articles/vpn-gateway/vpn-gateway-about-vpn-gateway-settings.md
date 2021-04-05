---
title: 'Azure VPN Gateway: nastavení konfigurace'
description: Seznamte se s VPN Gateway prostředky a nastavení pro virtuální síť vytvořenou v modelu nasazení Správce prostředků.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 1aba87b2139fb8a7d395fb3180d2074e47310fa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010813"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Informace o nastavení konfigurace VPN Gateway

Brána sítě VPN je typem brány virtuální sítě, která odesílá šifrovaný provoz mezi vaší virtuální sítí a vaším místním umístěním přes veřejné připojení. Bránu VPN můžete použít také k posílání provozu mezi virtuálními sítěmi v rámci páteřní sítě Azure.

Připojení brány VPN se spoléhá na konfiguraci více prostředků, z nichž každá obsahuje konfigurovatelné nastavení. Části v tomto článku popisují prostředky a nastavení vztahující se k bráně VPN pro virtuální síť vytvořenou v modelu nasazení Správce prostředků. Popisy a diagramy topologie pro každé řešení připojení najdete v článku [o VPN Gateway](vpn-gateway-about-vpngateways.md) .

Hodnoty v tomto článku platí pro brány VPN (brány virtuální sítě, které používají síť VPN-GatewayType). Tento článek nepopisuje všechny typy bran nebo brány redundantní v zóně.

* Hodnoty, které se vztahují na-GatewayType ' ExpressRoute ', najdete v tématu [Virtual Network Branch pro ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

* Informace o branách redundantních bránách najdete v tématu [informace o redundantních branách v zóně](about-zone-redundant-vnet-gateways.md).

* Informace o virtuální síti WAN najdete v tématu [o virtuální síti WAN](../virtual-wan/virtual-wan-about.md).

## <a name="gateway-types"></a><a name="gwtype"></a>Typy bran

Každá virtuální síť může mít pouze jednu bránu virtuální sítě každého typu. Při vytváření brány virtuální sítě je nutné zajistit, aby byl typ brány správný pro vaši konfiguraci.

Dostupné hodnoty pro – GatewayType jsou:

* Vpn
* ExpressRoute

Brána sítě VPN vyžaduje `-GatewayType` *síť VPN*.

Příklad:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gateway-skus"></a><a name="gwsku"></a>Skladové položky brány

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Konfigurace SKU brány

**Azure Portal**

Pokud pomocí Azure Portal vytvoříte bránu služby Správce prostředků virtuální sítě, můžete vybrat SKU brány pomocí rozevíracího seznamu. Možnosti, které zobrazíte, odpovídají typu brány a typu VPN, který vyberete.

**PowerShell**

Následující příklad prostředí PowerShell Určuje `-GatewaySku` jako VpnGw1. Když pomocí PowerShellu vytvoříte bránu, musíte nejdřív vytvořit konfiguraci IP adresy a potom k tomu použít proměnnou. V tomto příkladu je konfigurační proměnná $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

**Azure CLI**

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizing-or-changing-a-sku"></a><a name="resizechange"></a>Změna velikosti nebo změny SKU

Pokud máte bránu VPN a chcete použít jinou SKU brány, vaše možnosti mají buď změnit velikost SKU brány, nebo přejít na jinou SKLADOVOU položku. Když změníte jinou SKU brány, odstraníte zcela stávající bránu a vytvoříte novou. Sestavení brány může trvat až 45 minut. V porovnání se při změně velikosti SKU brány nejedná o spoustu výpadků, protože nemusíte bránu odstranit a znovu sestavovat. Pokud máte možnost změnit velikost SKU brány místo změny, budete ji chtít provést. Existují však pravidla týkající se změny velikosti:

1. S výjimkou základního SKU můžete změnit velikost SKU brány VPN na jinou SKLADOVOU položku služby VPN Gateway v rámci stejné generace (Generation1 nebo Generation2). Například VpnGw1 of Generation1 lze změnit na VpnGw2 Generation1, ale ne na VpnGw2 Generation2.
2. Pokud používáte staré SKU brány, můžete měnit velikost mezi Basic, Standard a HighPerformance SKU.
3. **Nemůžete** změnit velikost z položek Basic/Standard/HighPerformance SKU na SKU VpnGw. Místo toho je třeba [Přejít](#change) na nové SKU.

#### <a name="to-resize-a-gateway"></a><a name="resizegwsku"></a>Změna velikosti brány

**Azure Portal**

[!INCLUDE [Resize a SKU - portal](../../includes/vpn-gateway-resize-gw-portal-include.md)]

**PowerShell**

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="to-change-from-an-old-legacy-sku-to-a-new-sku"></a><a name="change"></a>Změna ze staré (starší) SKU na novou SKLADOVOU položku

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connection-types"></a><a name="connectiontype"></a>Typy připojení

V modelu nasazení Správce prostředků Každá konfigurace vyžaduje konkrétní typ připojení brány virtuální sítě. Dostupné hodnoty prostředí PowerShell v Resource Manageru pro `-ConnectionType` jsou:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

V následujícím příkladu PowerShellu vytvoříme připojení S2S, které vyžaduje typ připojení s *protokolem IPSec*.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpn-types"></a><a name="vpntype"></a>Typy sítě VPN

Když vytvoříte bránu virtuální sítě pro konfiguraci brány sítě VPN, musíte zadat typ sítě VPN. Typ sítě VPN, který zvolíte, závisí na topologii připojení, kterou chcete vytvořit. Například připojení P2S vyžaduje typ VPN RouteBased. Typ sítě VPN může také záviset na hardwaru, který používáte. Konfigurace S2S vyžadují zařízení VPN. Některá zařízení VPN podporují jenom určitý typ sítě VPN.

Typ sítě VPN, který vyberete, musí splňovat všechny požadavky na připojení pro řešení, které chcete vytvořit. Pokud například chcete vytvořit připojení k bráně VPN Gateway a připojení brány VPN P2S pro stejnou virtuální síť, použijete typ VPN *RouteBased* , protože P2S vyžaduje typ RouteBased VPN. Budete taky muset ověřit, jestli vaše zařízení VPN podporovalo připojení VPN RouteBased. 

Jakmile je brána virtuální sítě vytvořená, nemůžete změnit typ sítě VPN. Musíte odstranit bránu virtuální sítě a vytvořit novou. Existují dva typy sítě VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Následující příklad prostředí PowerShell Určuje `-VpnType` jako *RouteBased*. Při vytváření brány se musíte ujistit, že parametr -VpnType odpovídá vaší konfiguraci.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="gateway-requirements"></a><a name="requirements"></a>Požadavky na bránu

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gateway-subnet"></a><a name="gwsub"></a>Podsíť brány

Než vytvoříte bránu sítě VPN, musíte vytvořit podsíť brány. Podsíť brány obsahuje IP adresy, které používají virtuální počítače a služby brány virtuální sítě. Když vytvoříte bránu virtuální sítě, virtuální počítače brány se nasadí do podsítě brány a nakonfigurují s požadovaným nastavením služby VPN Gateway. Nikdy nesaďte nic jiného (například další virtuální počítače) do podsítě brány. Aby bylo možné správně pracovat, podsíť brány musí mít název "GatewaySubnet". Pojmenování podsítě brány "GatewaySubnet" umožňuje službě Azure zjistit, že se jedná o podsíť pro nasazení virtuálních počítačů a služeb brány virtuální sítě do.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Při vytváření podsítě brány zadáte počet IP adres, které podsíť obsahuje. IP adresy v podsíti brány jsou přiděleny virtuálním počítačům brány a službám brány. Některé konfigurace vyžadují víc IP adres než jiné. 

Při plánování velikosti podsítě brány si přečtěte dokumentaci ke konfiguraci, kterou plánujete vytvořit. Například konfigurace s ExpressRoute/VPN Gateway vyžaduje větší podsíť brány než většina ostatních konfigurací. Kromě toho můžete chtít zajistit, aby podsíť brány obsahovala dostatek IP adres, aby mohla pojmout možné budoucí další konfigurace. I když můžete vytvořit podsíť brány, která je menší než/29, doporučujeme vytvořit podsíť brány o velikosti/27 nebo větší (/27,/26 atd.), pokud máte dostupný adresní prostor. To bude vyhovovat většině konfigurací.

Následující příklad Správce prostředků PowerShell ukazuje podsíť brány s názvem GatewaySubnet. Můžete vidět, že zápis CIDR určuje/27, což umožňuje dostatek IP adres pro většinu konfigurací, které aktuálně existují.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="local-network-gateways"></a><a name="lng"></a>Brány místní sítě

Brána místní sítě se liší od brány virtuální sítě. Při vytváření konfigurace brány VPN se jako brána místní sítě obvykle představuje vaše místní síť a příslušné zařízení VPN. V modelu nasazení Classic se brána místní sítě označovala jako „místní lokalita“.

Bráně místní sítě dáte název, veřejnou IP adresu nebo plně kvalifikovaný název domény (FQDN) místního zařízení VPN a určíte předpony adres, které se nacházejí v místním umístění. Azure nahlíží na předpony cílových adres pro síťový provoz, sleduje konfiguraci, kterou jste zadali pro bránu místní sítě, a odpovídajícím způsobem směruje pakety. Pokud na svém zařízení VPN používáte Border Gateway Protocol (BGP), zadáte IP adresu partnerského uzlu protokolu BGP vašeho zařízení VPN a číslo autonomního systému (ASN) vaší místní sítě. Také zadáte brány místní sítě pro konfigurace VNet-to-VNet, které používají připojení brány VPN.

Následující příklad prostředí PowerShell vytvoří novou bránu místní sítě:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Někdy je potřeba upravit nastavení místní síťové brány. Například když přidáte nebo upravíte rozsah adres nebo pokud se změní IP adresa zařízení VPN. Přečtěte si téma [Úprava nastavení místní síťové brány pomocí PowerShellu](vpn-gateway-modify-local-network-gateway.md).

## <a name="rest-apis-powershell-cmdlets-and-cli"></a><a name="resources"></a>Rozhraní REST API, rutiny prostředí PowerShell a rozhraní příkazového řádku

Další technické materiály a specifické požadavky na syntaxi při použití rozhraní REST API, rutin PowerShellu nebo Azure CLI pro konfiguraci VPN Gateway najdete na následujících stránkách:

| **Standardním** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/az.network/#networking) |[PowerShell](/powershell/module/az.network#vpn) |
| [REST API](/previous-versions/azure/reference/jj154113(v=azure.100)) |[REST API](/rest/api/network/virtualnetworkgateways) |
| Nepodporováno | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Další kroky

Další informace o dostupných konfiguracích připojení najdete v tématu [o VPN Gateway](vpn-gateway-about-vpngateways.md).