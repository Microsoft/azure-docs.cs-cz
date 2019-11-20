---
title: Snímek konfigurace aplikace Azure – snímek v čase | Microsoft Docs
description: Přehled toho, jak snímek v čase funguje v konfiguraci aplikací Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: fdbe4846700c690261dbc734063f4420478666a8
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185205"
---
# <a name="point-in-time-snapshot"></a>Snímek k určitému časovému okamžiku

Konfigurace aplikace Azure udržuje záznamy přesně po vytvoření nové páry klíč-hodnota a pak se upraví. Tyto záznamy tvoří úplnou časovou osu ve změnách hodnot typu klíč-hodnota. Úložiště konfigurace aplikace může rekonstruovat historii jakékoli hodnoty klíče a kdykoli v daném okamžiku přehraje poslední hodnotu, a to až do současného. Pomocí této funkce můžete "čas-cestování" zpět a načíst starou hodnotu klíče. Můžete například získat nejstarší nastavení konfigurace těsně před nejnovějším nasazením, aby bylo možné obnovit předchozí konfiguraci a vrátit aplikaci zpět.

## <a name="key-value-retrieval"></a>Načtení hodnoty klíče

Chcete-li načíst dřívější hodnoty klíče, zadejte čas, kdy jsou klíčové hodnoty snímky v hlavičce HTTP REST API volání. Příklad:

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

V současné době konfigurace aplikace udržuje sedm dní v historii změn.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace v ASP.NET Core](./quickstart-aspnet-core-app.md)  
