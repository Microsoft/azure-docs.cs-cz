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
ms.openlocfilehash: e833146d05f0c35449915c1d1293873258a7b7eb
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226772"
---
# <a name="point-in-time-snapshot"></a>Snímek k určitému časovému okamžiku

Konfigurace aplikace Azure uchovává záznamy přesné případů, kdy je nový pár klíč hodnota vytvořit a pak upravit. Tyto záznamy tvoří úplnou časová osa změnami klíč hodnota. Konfigurace app storu můžete rekonstrukci historie všechny klíčové hodnoty a přehrát jeho poslední hodnotu v kterémkoli daném okamžiku až po současnost. Pomocí této funkce můžete "čas cesty" zpět a načtení původní hodnoty klíče. Například můžete získat Včerejší nastavení konfigurace, těsně před poslední nasazení, aby bylo možné obnovit předchozí konfiguraci a vrátit zpět aplikace.

## <a name="key-value-retrieval"></a>Načítání klíč hodnota

K načtení poslední hodnoty klíče, určují dobu, po který jsou hodnoty klíče snímek v hlavičce protokolu HTTP volání rozhraní REST API. Příklad:

        GET /revisions HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Konfigurace aplikací v současné době udržuje sedm dní historie změn.

## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Vytvoření webové aplikace ASP.NET](quickstart-aspnet-core-app.md)  
