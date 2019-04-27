---
title: Azure v okamžiku snímek konfigurace aplikací | Dokumentace Microsoftu
description: Přehled o tom, jak v daném okamžiku snímku funguje v konfiguraci aplikací Azure
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
ms.openlocfilehash: 83770e8c5f415670855b5cf2502d02c4d6919440
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741134"
---
# <a name="point-in-time-snapshot"></a>Snímek k určitému časovému okamžiku

Konfigurace aplikace Azure uchovává záznamy přesné případů, kdy je nový pár klíč hodnota vytvořit a pak upravit. Tyto záznamy tvoří úplnou časová osa změnami klíč hodnota. Konfigurace app storu můžete rekonstrukci historie všechny klíčové hodnoty a přehrát jeho poslední hodnotu v kterémkoli daném okamžiku až po současnost. Pomocí této funkce můžete "čas cesty" zpět a načtení původní hodnoty klíče. Například můžete získat Včerejší nastavení konfigurace, těsně před poslední nasazení, aby bylo možné obnovit předchozí konfiguraci a vrátit zpět aplikace.

## <a name="key-value-retrieval"></a>Načítání klíč hodnota

K načtení poslední hodnoty klíče, určují dobu, po který jsou hodnoty klíče snímek v hlavičce protokolu HTTP volání rozhraní REST API. Příklad:

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Konfigurace aplikací v současné době udržuje sedm dní historie změn.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace ASP.NET Core](./quickstart-aspnet-core-app.md)  
