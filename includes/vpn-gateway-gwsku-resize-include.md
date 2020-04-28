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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "73495780"
---
Pomocí rutiny `Resize-AzVirtualNetworkGateway` prostředí PowerShell můžete upgradovat nebo downgradovat Generation1 nebo Generation2 SKU (u všech SKU VpnGw lze změnit velikost kromě základních SKU). Pokud používáte SKU základní brány, [použijte místo toho tyto pokyny](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) ke změně velikosti brány.

Následující příklad prostředí PowerShell zobrazuje SKLADOVOU položku brány, která se mění podle velikosti VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Můžete také změnit velikost brány v Azure Portal tak, že na ni kliknete na **konfigurační** stránku brány virtuální sítě a z rozevíracího seznamu vyberete jinou skladovou položku.
