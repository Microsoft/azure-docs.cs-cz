---
title: Starší virtuální sítě Azure s SKU služby VPN gateway | Dokumentace Microsoftu
description: Jak pracovat s staré SKU; brány virtuální sítě Basic, Standard a HighPerformance.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: cherylmc
ms.openlocfilehash: 5a9e3f63a484069bf8cd39f8a545d7c37f05c63c
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417308"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Práce s SKU (starší verze SKU) brány virtuální sítě

Tento článek obsahuje informace o starší (staré) brány virtuální sítě skladové položky. Starší skladové položky i nadále fungovat v obou modelech nasazení. pro brány VPN Gateway, které již byly vytvořeny. Brány VPN Classic i nadále používat starší verze SKU pro existující brány i pro nové brány. Při vytváření brány VPN nové Resource Manageru, použijte nové SKU brány. Informace o nové SKU najdete v tématu [informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKU brány

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Odhadovaná agregovaná propustnost podle SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Podporované konfigurace podle SKU a typu sítě VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Změňte velikost brány

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Změnit velikost skladové položky brány v rámci stejné skladová položka rodina brána. Například pokud máte standardní SKU, můžete změnit velikost na HighPerformance SKU. Nelze však změnit velikost vaší brány VPN mezi staré SKU a řad SKU na nové. Například nelze přejít z standardní skladová jednotka VpnGw2 SKU nebo základní SKU na VpnGw1.

Změna velikosti brány pro model nasazení classic, použijte následující příkaz:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Změna velikosti brány pro model nasazení Resource Manageru pomocí Powershellu, použijte následující příkaz:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
Můžete taky změnit velikost brány na webu Azure Portal.

## <a name="change"></a>Změnit na nové SKU brány

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Další postup

Další informace o nové SKU brány najdete v tématu [skladové položky brány](vpn-gateway-about-vpngateways.md#gwsku).

Další informace o nastavení konfigurace, najdete v části [nastavení konfigurace služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).
