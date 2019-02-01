---
title: Nastavení služby VPN gateway pro více míst připojení Azure | Dokumentace Microsoftu
description: Další informace o nastavení služby VPN Gateway pro brány virtuální sítě Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: cherylmc
ms.openlocfilehash: 3bf3dd325af48f99e109f651628883d8f946fdc8
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512478"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Informace o nastavení konfigurace služby VPN Gateway

Brána VPN je typem brány virtuální sítě, která odesílá šifrovaný síťový provoz mezi vaší virtuální sítí a místním umístěním přes veřejné připojení. Bránu sítě VPN můžete použít také k posílání síťového provozu mezi virtuálními sítěmi přes páteřní síť Azure.

Připojení brány VPN se spoléhá na konfiguraci více zdrojů, z nichž každý obsahuje konfigurovatelné nastavení. Části v tomto článku popisují prostředky a nastavení, které se týkají brány sítě VPN pro virtuální sítě vytvořené v modelu nasazení Resource Manageru. Můžete najít popisy a diagramy topologie pro každé připojení řešení [informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md) článku.

>[!NOTE]
> Hodnoty v tomto článku platí brány VPN (brány virtuální sítě, které používají parametr-GatewayType Vpn). Tento článek nepopisuje všechny typy brány a zónově redundantní brány.
>
>* Hodnoty, které platí pro parametr-GatewayType "ExpressRoute", naleznete v tématu [brány virtuální sítě pro ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).
>* Zónově redundantní brány najdete v části [o zónově redundantní brány](about-zone-redundant-vnet-gateways.md).
>* Virtuální sítě WAN, naleznete v tématu [o virtuální sítě WAN](../virtual-wan/virtual-wan-about.md). 
>

## <a name="gwtype"></a>Typy bran

Každá virtuální síť může mít pouze jednu bránu virtuální sítě každého typu. Při vytváření brány virtuální sítě, musí se ujistěte, že je typ brány odpovídá vaší konfiguraci.

Dostupné hodnoty pro parametr-GatewayType jsou:

* Vpn
* ExpressRoute

Vyžaduje bránu sítě VPN `-GatewayType` *Vpn*.

Příklad:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>SKU brány

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Konfigurace skladové položky brány

#### <a name="azure-portal"></a>portál Azure

Je-li vytvořit bránu virtuální sítě Resource Manageru pomocí webu Azure portal, můžete pomocí rozevíracího seznamu vyberte SKU brány. Možnosti, které budou vám nabídnuty odpovídají typ brány a typ sítě VPN, kterou jste vybrali.

#### <a name="powershell"></a>PowerShell

Následující příklad Powershellu Určuje, `-GatewaySku` jako VpnGw1. Při použití Powershellu k vytvoření brány, je třeba nejprve vytvořit konfiguraci IP adresy a poté na něj odkazovat pomocí proměnné. V tomto příkladu je proměnná konfigurace $gwipconfig.

```powershell
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizechange"></a>Změna velikosti nebo změnu SKU

Pokud máte bránu sítě VPN a chcete použít různé skladové položky brány, vaše možnosti jsou buď Změna velikosti SKU brány, nebo chcete změnit na druhý. Při změně na jiný skladové položky brány zcela odstranit existující bránu a vytvářet nové. To může trvat až 45 minut na sestavení. Porovnání když změníte velikost skladové položky, brány budete mít velmi málo výpadek protože není nutné odstranit a znovu vytvořit bránu. Pokud máte možnost Změna velikosti SKU brány, spíše než ho změnit, můžete to udělat. Existují však pravidla týkající se změny velikosti:

1. Můžete měnit velikost mezi VpnGw1, VpnGw2 a VpnGw3 SKU.
2. Pokud používáte staré SKU brány, můžete měnit velikost mezi Basic, Standard a HighPerformance SKU.
3. **Není možné** změnit velikost z Basic/Standard/HighPerformance SKU na nové VpnGw1/VpnGw2/VpnGw3 SKU. Místo toho musíte [změnit](#change) na nové SKU.

#### <a name="resizegwsku"></a>Změňte velikost brány

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="change"></a>Chcete-li změnit ze staré (starší) skladové položky na novou SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connectiontype"></a>Typy připojení

V modelu nasazení Resource Manager Každá konfigurace vyžaduje typ připojení brány konkrétní virtuální sítě. Dostupné hodnoty prostředí PowerShell v Resource Manageru pro `-ConnectionType` jsou:

* Protokol IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

V následujícím příkladu Powershellu vytvoříme připojení S2S, které vyžaduje typ připojení *IPsec*.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>Typy sítě VPN

Při vytváření brány virtuální sítě pro konfiguraci brány VPN, musíte zadat typ sítě VPN. Typ sítě VPN, kterou zvolíte, závisí na topologie připojení, který chcete vytvořit. Připojení P2S například vyžaduje typ sítě VPN RouteBased. Typ sítě VPN může také záviset na hardwaru, kterou používáte. Konfigurace S2S vyžadují zařízení VPN. Některá zařízení VPN podporují pouze určitého typu sítě VPN.

Typ sítě VPN, který jste vybrali musí splňovat všechny připojení požadavky na řešení, že který chcete vytvořit. Pokud chcete vytvořit připojení brány VPN typu S2S a připojení brány VPN typu P2S pro stejnou virtuální síť, je třeba, použijte typ sítě VPN *RouteBased* protože P2S vyžaduje typ sítě VPN RouteBased. Musíte také ověřte, že vaše zařízení VPN podporované připojení VPN typu RouteBased. 

Po vytvoření brány virtuální sítě, nelze změnit typ sítě VPN. Budete muset odstranit bránu virtuální sítě a vytvořte novou. Existují dva typy sítě VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Následující příklad Powershellu Určuje, `-VpnType` jako *RouteBased*. Při vytváření brány se musíte ujistit, že parametr -VpnType odpovídá vaší konfiguraci.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>Požadavky na bránu

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Podsíť brány

Než vytvoříte bránu VPN, musíte vytvořit podsíť brány. Podsíť brány obsahuje IP adresy, které používají bránu virtuální sítě virtuálních počítačů a služeb. Při vytváření brány virtuální sítě, virtuální počítače brány se nasazují do podsítě brány a nakonfigurovanou povinné nastavení služby VPN gateway. Musíte nikdy nenasazujte nic jiného (třeba dalších virtuálních počítačů) do podsítě brány. Podsíť brány musí mít název "GatewaySubnet" fungovala správně. Název podsítě brány: GatewaySubnet"umožňuje vědět, že se jedná o podsítě k nasazení brány virtuální sítě virtuálních počítačů a služeb na Azure.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Při vytváření podsítě brány zadáte počet IP adres, které podsíť obsahuje. Virtuální počítače brány a služby brány se přidělují IP adresy v podsíti brány. Některé konfigurace vyžadují víc IP adres než jiné. Podívejte se na pokyny pro konfiguraci, kterou chcete vytvořit a ověřit, že podsíť brány, kterou chcete vytvořit tyto požadavky splňuje. Kromě toho můžete zajistit, aby že podsíť brány obsahuje dostatek IP adres pro případné další další konfigurace. Můžete si sice vytvořit podsíť brány malá jako minimální velikostí/29, doporučujeme vytvořit podsíť brány o velikosti/28 nebo větší (/ 28, velikost/27, / 26 atd.). Tímto způsobem, pokud chcete přidat funkce v budoucnu, nebude mít odstraňovat bránu, pak odstraňte a znovu vytvořit podsíť brány umožňující další IP adresy.

Následující příklad Powershellu pro Resource Manager ukazuje podsíť brány s názvem GatewaySubnet. Uvidíte, že zápis CIDR Určuje velikost/27, která zajistíte dostatek IP adres u většiny konfigurací, které momentálně existují.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Místní síťové brány

Při vytváření konfiguraci brány VPN brány místní sítě často představuje místní umístění. V modelu nasazení Classic se brána místní sítě označovala jako „místní lokalita“. 

Pojmenujte bránu místní sítě, veřejnou IP adresu místního zařízení VPN a zadáte předpony adres, které se nacházejí na místní umístění. Azure zjistí pro síťový provoz předpony cílových adres consults konfiguraci, kterou jste zadali pro bránu místní sítě a směruje pakety odpovídajícím způsobem. Zadáte také brány místní sítě pro konfigurace VNet-to-VNet pomocí připojení brány VPN.

Následující příklad Powershellu vytvoří novou bránu místní sítě:

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

V některých případech budete muset upravit nastavení místní síťové brány. Například když přidáváte nebo odebíráte rozsah adres, nebo pokud IP adresa zařízení VPN bude měnit. Zobrazit [úprava nastavení místní síťové brány pomocí Powershellu](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>REST API, rutin prostředí PowerShell a rozhraní příkazového řádku

Další zdroje technických informací a požadavky na konkrétní syntaxe při použití rozhraní REST API, rutin prostředí PowerShell nebo rozhraní příkazového řádku Azure pro konfigurace brány VPN najdete v tématu na následujících stránkách:

| **Classic** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/azurerm.network/#networking) |[PowerShell](/powershell/module/azurerm.network#vpn) |
| [REST API](https://msdn.microsoft.com/library/jj154113) |[REST API](/rest/api/network/virtualnetworkgateways) |
| Nepodporuje se | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Další postup

Další informace o konfiguracích dostupné připojení, najdete v části [informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md).
