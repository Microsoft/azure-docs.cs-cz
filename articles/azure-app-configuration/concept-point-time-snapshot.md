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
ms.openlocfilehash: 4f26f3a31dc6303e991c39fc7f85d9bf254d57bc
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884866"
---
# <a name="point-in-time-snapshot"></a>Snímek bodu v čase

Konfigurace aplikace Azure uchovává záznamy přesné časy, kdy je nový pár klíč hodnota vytvořit a následně upravit. Tyto záznamy tvoří úplnou časová osa změnami klíč hodnota. Konfigurace app storu můžete rekonstrukci historie klíč hodnota a přehrát jeho poslední hodnotu v kterémkoli daném okamžiku až po současnost. Tato funkce umožňuje "čas cesty" zpět a načíst starý klíč hodnota. Například můžete získat nastavení konfigurace pro včerejšího dne, těsně před poslední nasazení tak, aby mohli obnovit předchozí konfiguraci v případě, že je potřeba vrátit zpět aplikace.

## <a name="key-value-retrieval"></a>Načítání klíč hodnota

K načtení poslední hodnoty klíče, budete muset určit čas, po kterém se snímek klíč hodnota v hlavičce protokolu HTTP volání rozhraní REST API. Příklad:

        GET /revisions HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Konfigurace aplikací v současné době zachová historii změn za 7 dní.

## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Vytvoření webové aplikace ASP.NET](quickstart-aspnet-core-app.md)  
