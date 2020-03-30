---
title: Systémové proměnné v Azure Data Factory
description: Tento článek popisuje systémové proměnné podporované službou Azure Data Factory. Tyto proměnné můžete použít ve výrazech při definování entit Factory dat.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 0a5237336530d30c3801b13b910171e236e87a23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73679275"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systémové proměnné podporované službou Azure Data Factory
Tento článek popisuje systémové proměnné podporované službou Azure Data Factory. Tyto proměnné můžete použít ve výrazech při definování entit Factory dat.

## <a name="pipeline-scope"></a>Obor kanálu
Tyto systémové proměnné lze odkazovat kdekoli v kanálu JSON.

| Název proměnné | Popis |
| --- | --- |
| @pipeline(). DataFactory |Název datové továrny, kterou je spuštěno v rámci spuštění kanálu |
| @pipeline(). Potrubí |Název potrubí |
| @pipeline(). RunId | ID konkrétního spuštění kanálu |
| @pipeline(). Typ aktivační události | Typ aktivační události, která vyvolala kanál (Ruční, Plánovač) |
| @pipeline(). TriggerId| ID aktivační události, která vyvolá kanál |
| @pipeline(). Název_aktivační události| Název aktivační události, která vyvolá kanál |
| @pipeline(). TriggerTime| Čas, kdy aktivační událost, která vyvolala kanál. Čas aktivační události je skutečný čas vypalovaného, nikoli naplánovaný čas. Například `13:20:08.0149599Z` je vrácena namísto`13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Obor aktivační události plánu
Tyto systémové proměnné lze odkazovat kdekoli v aktivační události JSON, pokud aktivační událost je typu: "ScheduleTrigger."

| Název proměnné | Popis |
| --- | --- |
| @trigger().scheduledTime |Čas, kdy byla naplánovaná aktivační událost k vyvolání spuštění kanálu. Například pro aktivační událost, která se aktivuje každých `2017-06-01T22:25:00Z` `2017-06-01T22:30:00Z` 5 minut, tato proměnná vrátí `2017-06-01T22:20:00Z`, , v uvedeném pořadí.|
| @trigger().startTime |Čas, kdy aktivační událost **skutečně** aktivována vyvolat spuštění kanálu. Například pro aktivační událost, která se aktivuje každých 5 `2017-06-01T22:20:00.4061448Z` `2017-06-01T22:25:00.7958577Z`minut, může tato proměnná vrátit něco takového , , `2017-06-01T22:30:00.9935483Z` v uvedeném pořadí. (Poznámka: Časové razítko je ve výchozím nastavení ve formátu ISO 8601)|

## <a name="tumbling-window-trigger-scope"></a>Rozsah aktivační události omílání okna
Tyto systémové proměnné lze odkazovat kdekoli v aktivační události JSON, pokud aktivační událost je typu: "TumblingWindowTrigger."
(Poznámka: Časové razítko je ve výchozím nastavení ve formátu ISO 8601)

| Název proměnné | Popis |
| --- | --- |
| @trigger().outputs.windowStartTime |Začátek okna, kdy byla naplánována aktivační událost vyvolat spuštění kanálu. Pokud omílání okno spoušť má frekvenci "hodinové" by to čas na začátku hodiny.|
| @trigger().outputs.windowEndTime |Konec okna, kdy byla naplánována aktivační událost vyvolat spuštění kanálu. Pokud omílání okno spoušť má frekvenci "hodinové" by to čas na konci hodiny.|
## <a name="next-steps"></a>Další kroky
Informace o tom, jak jsou tyto proměnné používány ve výrazech, naleznete v [tématu Výraz jazyka & funkce](control-flow-expression-language-functions.md).
