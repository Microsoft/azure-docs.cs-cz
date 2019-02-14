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
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246167"
---
Pomocí příkazu [az network vpn-connection show](/cli/azure/network/vpn-connection) můžete ověřit, že vaše připojení bylo úspěšné. V příkladu odkazuje  --name na název připojení, které chcete testovat. Pokud navazování připojení probíhá, zobrazí se stav připojení Connecting (Připojování). Jakmile bude připojení navázáno, stav se změní na Connected (Připojeno).

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
