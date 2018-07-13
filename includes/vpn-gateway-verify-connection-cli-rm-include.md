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
ms.openlocfilehash: 1c3103889bd11df45710cc0d6afaeeba022c9ace
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38763001"
---
Pomocí příkazu [az network vpn-connection show](/cli/azure/network/vpn-connection#show) můžete ověřit, že vaše připojení bylo úspěšné. V příkladu odkazuje  --name na název připojení, které chcete testovat. Pokud navazování připojení probíhá, zobrazí se stav připojení Connecting (Připojování). Jakmile bude připojení navázáno, stav se změní na Connected (Připojeno).

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```