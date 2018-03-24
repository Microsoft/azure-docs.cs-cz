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
ms.openlocfilehash: da682e3e947c3f8643610e42ad22fb9d742796f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
Můžete ověřit, zda bylo připojení úspěšně vytvořeno, spuštěním rutiny Get-AzureRmVirtualNetworkGatewayConnection s přepínačem -Debug nebo bez něj. 

1. Použijte následující příklad rutiny a nakonfigurujte hodnoty tak, aby odpovídaly vašemu prostředí. Po zobrazení výzvy vyberte možnost „A“, abyste spustili „vše“. V příkladu odkazuje -Name na název připojení, které chcete testovat.

  ```powershell
  Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
  ```
2. Po dokončení zpracování rutiny si prohlédněte hodnoty. Ve výše uvedeném příkladu se zobrazí stav připojení Připojeno a vy vidíte příchozí a odchozí bajty.
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```