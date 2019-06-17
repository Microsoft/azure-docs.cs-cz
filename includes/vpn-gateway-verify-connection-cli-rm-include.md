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
ms.openlocfilehash: 0e009354e66ab13cdb9fbc3cf9e4b37e904bdfd1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121207"
---
Pomocí příkazu [az network vpn-connection show](/cli/azure/network/vpn-connection) můžete ověřit, že vaše připojení bylo úspěšné. V příkladu odkazuje  --name na název připojení, které chcete testovat. Pokud navazování připojení probíhá, zobrazí se stav připojení Connecting (Připojování). Jakmile bude připojení navázáno, stav se změní na Connected (Připojeno).

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
