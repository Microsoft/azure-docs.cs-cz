---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f6fd4039614dbd7c1a2b2c6ba8403502a6420fe3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159389"
---
Pro aktuální SKU (VpnGw1, VpnGw2 a VPNGW3), kterého chcete změnit velikost skladová položka pro upgrade na výkonnější jedna brána lze použít `Resize-AzVirtualNetworkGateway` rutiny Powershellu. Můžete také provést downgrade použití této rutiny velikost SKU brány. Pokud používáte základní bránu SKU, [místo toho použijte tyto pokyny](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) Změna velikosti brány.

Následující příklad PowerShell ukazuje během změny velikosti pro VpnGw2 skladové položky brány.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Můžete taky změnit velikost brány na webu Azure Portal tak, že přejdete **konfigurace** stránce pro vaši bránu virtuální sítě a z rozevíracího seznamu výběru různých SKU.