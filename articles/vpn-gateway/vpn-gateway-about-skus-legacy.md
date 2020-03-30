---
title: Starší položky su-prodej brány virtuální sítě Azure pro virtuální síť
description: Jak pracovat se starými virtuálními síťovými bránami SKU; Základní, Standardní a Vysoký výkon.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279387"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Práce s virtuálními síťovými bránami skutužky (starší virtuální pamětijsoucí)

Tento článek obsahuje informace o starší (staré) virtuální síťové brány skuf. Starší položky skum stále fungují v obou modelech nasazení pro brány VPN, které již byly vytvořeny. Klasické brány VPN nadále používají starší skutély, a to jak pro existující brány, tak pro nové brány. Při vytváření nových bran VPN správce prostředků použijte nové suny brány brány brány. Informace o nových skum naleznete v tématu [About VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gateway-skus"></a><a name="gwsku"></a>SKU brány

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

Ceny starších bran můžete zobrazit v části **Brány virtuální sítě,** která se nachází na [stránce cen ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>Odhadovaná agregovaná propustnost podle typů SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>Podporované konfigurace podle skladové položky a typu VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>Změna velikosti brány

Můžete změnit velikost brány do skladové položky brány v rámci stejné rodiny skladových položk. Například pokud máte standardní skladovou položku, můžete změnit velikost na skladovou položku s vysokým výkonem. Nelze však změnit velikost brány VPN mezi starými skladovými položkami a novými rodinami skladových položk. Například nelze přejít ze standardní sku vpngw2 sku nebo základní skladové položky na VpnGw1.

### <a name="resource-manager"></a>Resource Manager

Chcete-li změnit velikost brány pro model nasazení Správce prostředků pomocí prostředí PowerShell, použijte následující příkaz:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

Můžete také změnit velikost brány na webu Azure Portal.

### <a name="classic"></a><a name="classicresize"></a>Classic

Chcete-li změnit velikost brány pro klasický model nasazení, musíte použít rutiny PowerShell správy služeb. Použijte následující příkaz:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>Změna na nové soupoložky brány

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o nových supoložkách brány naleznete v tématu [Brána SKU](vpn-gateway-about-vpngateways.md#gwsku).

Další informace o nastavení konfigurace naleznete v tématu [O nastavení konfigurace brány VPN](vpn-gateway-about-vpn-gateway-settings.md).
