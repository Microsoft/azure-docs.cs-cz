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
ms.openlocfilehash: 081352a23e6a0d8f9e2daa77eca1f8ac85172ff6
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418224"
---
Pro aktuální SKU (VpnGw1, VpnGw2 a VPNGW3), kterého chcete změnit velikost skladová položka pro upgrade na výkonnější jedna brána lze použít `Resize-AzVirtualNetworkGateway` rutiny Powershellu. Můžete také provést downgrade použití této rutiny velikost SKU brány. Pokud používáte základní bránu SKU, [místo toho použijte tyto pokyny](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) Změna velikosti brány.

Následující příklad PowerShell ukazuje během změny velikosti pro VpnGw2 skladové položky brány.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Můžete taky změnit velikost brány na webu Azure Portal tak, že přejdete **konfigurace** stránce pro vaši bránu virtuální sítě a z rozevíracího seznamu výběru různých SKU.