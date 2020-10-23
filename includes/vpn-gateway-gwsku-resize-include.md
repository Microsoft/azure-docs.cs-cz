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
ms.openlocfilehash: a3ae2a876d6a3772d941fec0b8a1ea3f537e60c3
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424348"
---
Pomocí `Resize-AzVirtualNetworkGateway` rutiny prostředí PowerShell můžete upgradovat nebo downgradovat Generation1 nebo GENERATION2 SKU (u všech SKU VpnGw lze změnit velikost kromě základních SKU). Pokud používáte SKU základní brány, [použijte místo toho tyto pokyny](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) ke změně velikosti brány.

Následující příklad prostředí PowerShell zobrazuje SKLADOVOU položku brány, která se mění podle velikosti VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```