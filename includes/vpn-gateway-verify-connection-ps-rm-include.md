---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 49f48c2d0bf865cf8c8fde831e7a597f8701d213
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "67174835"
---
Pomocí rutiny Get-AzVirtualNetworkGatewayConnection můžete ověřit, jestli připojení proběhlo úspěšně, a to bez ladění. 

1. Použijte následující příklad rutiny a nakonfigurujte hodnoty tak, aby odpovídaly vašemu prostředí. Po zobrazení výzvy vyberte možnost „A“, abyste spustili „vše“. V příkladu odkazuje -Name na název připojení, které chcete testovat.

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. Po dokončení zpracování rutiny si prohlédněte hodnoty. Ve výše uvedeném příkladu se zobrazí stav připojení Připojeno a vy vidíte příchozí a odchozí bajty.
   
   ```
   "connectionStatus": "Connected",
   "ingressBytesTransferred": 33509044,
   "egressBytesTransferred": 4142431
   ```