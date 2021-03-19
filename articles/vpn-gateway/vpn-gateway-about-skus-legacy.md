---
title: Starší skladové položky brány sítě VPN služby Azure Virtual Network
description: Jak pracovat se starými SKU brány virtuální sítě; Basic, Standard a HighPerformance.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84687782"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Práce s SKU brány virtuální sítě (zastaralé SKU)

Tento článek obsahuje informace o starších (starých) SKU brány virtuální sítě. Starší verze SKU stále fungují v obou modelech nasazení pro brány VPN, které již byly vytvořeny. Klasické služby VPN Gateway nadále používají starší skladové položky pro existující brány i pro nové brány. Při vytváření nových Správce prostředků bran VPN Gateway použijte nové SKU brány. Informace o nových SKU najdete v tématu [o VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gateway-skus"></a><a name="gwsku"></a>Skladové položky brány

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

Ceny starší brány můžete zobrazit v části **Virtual Network brány** , která je umístěná na [stránce s cenami ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>Odhadovaná agregovaná propustnost podle typů SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>Podporované konfigurace podle SKU a typu sítě VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>Změna velikosti brány

Bránu můžete změnit na SKU brány ve stejné rodině SKU. Například pokud máte standardní SKU, můžete změnit velikost na HighPerformance SKU. Nemůžete ale změnit velikost služby VPN Gateway mezi původními SKU a nové rodiny SKU. Například nemůžete přejít ze standardní SKU na SKU VpnGw2 nebo základní SKU na VpnGw1.

### <a name="resource-manager"></a>Resource Manager

Chcete-li změnit velikost brány pro model nasazení Správce prostředků pomocí prostředí PowerShell, použijte následující příkaz:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

Můžete také změnit velikost brány v Azure Portal.

### <a name="classic"></a><a name="classicresize"></a>Standardním

Chcete-li změnit velikost brány pro model nasazení Classic, je nutné použít rutiny prostředí PowerShell pro správu služeb. Použijte následující příkaz:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>Přejít na nové SKU brány

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o nových SKU brány najdete v tématu [SKU brány](vpn-gateway-about-vpngateways.md#gwsku).

Další informace o nastavení konfigurace najdete v tématu [informace o nastavení konfigurace VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).
