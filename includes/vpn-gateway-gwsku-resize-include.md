---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73495780"
---
Rutinu `Resize-AzVirtualNetworkGateway` prostředí PowerShell můžete použít k upgradu nebo downgrade sku Generation1 nebo Generation2 (všechny jednotky VpnGw s ougeny lze velikost s výjimkou základních skladových celků). Pokud používáte skladovou položku základní brány, změňte velikost brány [pomocí těchto pokynů.](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize)

Následující příklad prostředí PowerShell ukazuje, že sku brány se velikost na VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Velikost brány na webu Azure Portal můžete také změnit tak, že přejdete na stránku **Konfigurace** pro bránu virtuální sítě a vyberete jinou skladovou položku z rozevíracího souboru.
