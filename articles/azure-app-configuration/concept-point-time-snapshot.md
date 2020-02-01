---
title: Snímek konfigurace aplikace Azure – snímek v čase
description: Přehled toho, jak snímek v čase funguje v konfiguraci aplikací Azure
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.openlocfilehash: 4a352ba913b6ad4e3c8607677078e21070f294fd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899588"
---
# <a name="point-in-time-snapshot"></a>Snímek k určitému časovému okamžiku

Konfigurace aplikace Azure udržuje záznamy přesně po vytvoření nové páry klíč-hodnota a pak se upraví. Tyto záznamy tvoří úplnou časovou osu ve změnách hodnot typu klíč-hodnota. Úložiště konfigurace aplikace může rekonstruovat historii jakékoli hodnoty klíče a kdykoli v daném okamžiku přehraje poslední hodnotu, a to až do současného. Pomocí této funkce můžete "čas-cestování" zpět a načíst starou hodnotu klíče. Můžete například získat nejstarší nastavení konfigurace těsně před nejnovějším nasazením, aby bylo možné obnovit předchozí konfiguraci a vrátit aplikaci zpět.

## <a name="key-value-retrieval"></a>Načtení hodnoty klíče

Chcete-li načíst dřívější hodnoty klíče, zadejte čas, kdy jsou klíčové hodnoty snímky v hlavičce HTTP REST API volání. Příklad:

```rest
GET /kv HTTP/1.1
Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT
```

V současné době konfigurace aplikace udržuje sedm dní v historii změn.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace v ASP.NET Core](./quickstart-aspnet-core-app.md)  
