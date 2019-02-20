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
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418020"
---
Můžete ověřit, že vaše připojení bylo úspěšné, že pomocí rutiny "Get-AzVirtualNetworkGatewayConnection", s nebo bez něj "-Debug". 

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