---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4e958026b1167d65f47bddbe5e89ec4d6fed7ee3
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217932"
---
Pomocí příkazu [az network vpn-connection show](/cli/azure/network/vpn-connection) můžete ověřit, že vaše připojení bylo úspěšné. V příkladu odkazuje  --name na název připojení, které chcete testovat. Pokud navazování připojení probíhá, zobrazí se stav připojení Connecting (Připojování). Jakmile bude připojení navázáno, stav se změní na Connected (Připojeno).

```azurecli-interactive
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
