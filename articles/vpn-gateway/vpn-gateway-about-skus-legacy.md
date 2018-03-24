---
title: Virtuální síť starší verze Azure SKU služby VPN gateway | Microsoft Docs
description: Jak pracovat s původním SKU; brány virtuální sítě Basic, Standard a HighPerformance.
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
ms.openlocfilehash: 4feecb9c1e91e1bc6c66a610c092e7bf894886e5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Práce s SKU (starší verze SKU) brány virtuální sítě

Tento článek obsahuje informace o starší verze (starý) brány virtuální sítě SKU. Starší verze SKU i nadále fungovat v obou modelech nasazení pro brány sítě VPN, které již byly vytvořeny. Classic brány VPN dál používat starší verze SKU pro existující brány i pro nové brány. Při vytváření nového správce prostředků VPN Gateway, použijte nové SKU brány. Informace o nové SKU najdete v tématu [o službě VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKU brány

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Odhadovaná agregovaná propustnost podle SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Podporované konfigurace podle typu SKU a síť VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Změnit velikost brány

Můžete změnit velikost bránu skladová položka brány v rámci stejné řada SKU. Například pokud máte standardní SKU, můžete změnit velikost na HighPerformance SKU. Však nemůže změnit velikost bránu VPN mezi SKU staré a nové rodiny SKU. Například nelze přejít z standardní SKU VpnGw2 SKU nebo základní SKU pro VpnGw1.

Ke změně velikosti bránu pro model nasazení classic, použijte následující příkaz:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Ke změně velikosti bránu pro model nasazení Resource Manager pomocí prostředí PowerShell, použijte následující příkaz:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
Můžete taky změnit velikost brány na portálu Azure.

## <a name="change"></a>Změnit na nový SKU brány.

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Další postup

Další informace o nové SKU brány najdete v tématu [SKU brány](vpn-gateway-about-vpngateways.md#gwsku).

Další informace o nastavení konfigurace najdete v tématu [nastavení konfigurace o službě VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).