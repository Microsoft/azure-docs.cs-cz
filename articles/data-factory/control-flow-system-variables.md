---
title: Systémové proměnné v Azure Data Factory | Microsoft Docs
description: Tento článek popisuje systémové proměnné podporovaných službou Azure Data Factory. Tyto proměnné můžete použít ve výrazech při definování entit služby Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 43ea8703bdbfc23511c831a5f91c9461948cc254
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37058896"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systémové proměnné podporovaných službou Azure Data Factory
Tento článek popisuje systémové proměnné podporovaných službou Azure Data Factory. Tyto proměnné můžete použít ve výrazech při definování entit služby Data Factory.

## <a name="pipeline-scope"></a>Obor kanálu
Tyto proměnné systému může být odkazováno kdekoli v kódu JSON kanálu.

| Název proměnné | Popis |
| --- | --- |
| @pipeline(). Objekt pro vytváření dat |Název objektu pro vytváření dat kanál spustit běží v rámci |
| @pipeline(). Kanál |Název kanálu |
| @pipeline().RunId | ID specifické kanálu spustit |
| @pipeline(). TriggerType | Typ aktivační událost, která volá kanálu (ruční, Scheduler) |
| @pipeline(). SpuštěcíID| ID aktivační události, která volá kanálu |
| @pipeline(). Název aktivační události| Název aktivační události, která volá kanálu |
| @pipeline(). TriggerTime| Čas, kdy aktivační událost, která volá kanálu. Čas aktivace je skutečný čas aktivní, nejsou naplánovaném čase. Například `13:20:08.0149599Z` je vrácena místo `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Plán aktivace oboru
Tyto proměnné systému může být odkazováno kdekoli v aktivační události JSON Pokud aktivační událost není typu: "ScheduleTrigger."

| Název proměnné | Popis |
| --- | --- |
| @trigger.scheduledTime) |Čas naplánovaného aktivační události k vyvolání kanálu spustit. Například pro aktivační událost, která aktivuje každých 5 minut, by vrátit tuto proměnnou `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z` v uvedeném pořadí.|
| @trigger.startTime) |Čas, kdy aktivační událost **ve skutečnosti** aktivováno pro vyvolání kanálu spustit. Například pro aktivační událost, která aktivuje každých 5 minut, tato proměnná může vrátit přibližně toto `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z` v uvedeném pořadí.|

## <a name="tumbling-window-trigger-scope"></a>Přeskakující okno Aktivace oboru
Tyto proměnné systému může být odkazováno kdekoli v aktivační události JSON Pokud aktivační událost není typu: "TumblingWindowTrigger."

| Název proměnné | Popis |
| --- | --- |
| @trigger(). outputs.windowStartTime |Začátek okna v případě, že aktivační událost byla naplánována k vyvolání spuštění kanálu. Pokud má aktivační událost přeskakující okno frekvencí "každou hodinu" to může být čas od začátku za hodinu.|
| @trigger(). outputs.windowEndTime |Konec okna v případě, že aktivační událost byla naplánována k vyvolání kanálu spustit. Pokud má aktivační událost přeskakující okno frekvencí "každou hodinu" bude čas, konec hodiny.|
## <a name="next-steps"></a>Další postup
Informace o tom, jak jsou tyto proměnné použít ve výrazech naleznete v tématu [výrazu jazyka & funkce](control-flow-expression-language-functions.md).
