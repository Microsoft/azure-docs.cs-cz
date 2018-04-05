---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c06e69dd9d1997500589659e936dc25ee01ed145
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
Pro aktuální SKU (VpnGw1, VpnGw2 a VPNGW3), kterou chcete změnit velikost bránu SKU pro upgrade na výkonnější jeden, můžete použít `Resize-AzureRmVirtualNetworkGateway` rutiny prostředí PowerShell. Můžete také starší verzi brány velikost SKU použití této rutiny. Pokud používáte bránu základní SKU, [místo toho použijte tyto pokyny](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) ke změně velikosti brány.

Následující příklad PowerShell ukazuje změnu velikosti na VpnGw2 SKU brány.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Můžete taky změnit velikost brány na webu Azure portal přechodem na **konfigurace** stránky pro bránu virtuální sítě a vyberete různé SKU z rozevíracího seznamu.