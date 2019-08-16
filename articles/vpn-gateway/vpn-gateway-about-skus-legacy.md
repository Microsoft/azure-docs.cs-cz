---
title: Starší skladové položky brány VPN Gateway služby Azure Virtual Network Microsoft Docs
description: Jak pracovat se starými SKU brány virtuální sítě; Basic, Standard a HighPerformance.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 5c745258929d495c1e568a156690f569de9f0e36
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533904"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Práce s SKU brány virtuální sítě (zastaralé SKU)

Tento článek obsahuje informace o starších (starých) SKU brány virtuální sítě. Starší verze SKU stále fungují v obou modelech nasazení pro brány VPN, které již byly vytvořeny. Klasické služby VPN Gateway nadále používají starší skladové položky pro existující brány i pro nové brány. Při vytváření nových Správce prostředků bran VPN Gateway použijte nové SKU brány. Informace o nových SKU najdete v tématu [o VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKU brány

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

Ceny starší brány můžete zobrazit v části **Virtual Network brány** , která je umístěná na [stránce s cenami ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="agg"></a>Odhadovaná agregovaná propustnost podle SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Podporované konfigurace podle SKU a typu sítě VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Změna velikosti brány

Bránu můžete změnit na SKU brány ve stejné rodině SKU. Například pokud máte standardní SKU, můžete změnit velikost na HighPerformance SKU. Nemůžete ale změnit velikost služby VPN Gateway mezi původními SKU a nové rodiny SKU. Například nemůžete přejít ze standardní SKU na SKU VpnGw2 nebo základní SKU na VpnGw1.

### <a name="resource-manager"></a>Resource Manager

Chcete-li změnit velikost brány pro model nasazení Správce prostředků pomocí prostředí PowerShell, použijte následující příkaz:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

Můžete také změnit velikost brány v Azure Portal.

### <a name="classicresize"></a>Standardním

Chcete-li změnit velikost brány pro model nasazení Classic, je nutné použít rutiny prostředí PowerShell pro správu služeb. Použijte následující příkaz:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change"></a>Přejít na nové SKU brány

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o nových SKU brány najdete v tématu [SKU brány](vpn-gateway-about-vpngateways.md#gwsku).

Další informace o nastavení konfigurace najdete v tématu [informace o nastavení konfigurace VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).
