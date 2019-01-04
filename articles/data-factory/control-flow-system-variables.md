---
title: Systémové proměnné ve službě Azure Data Factory | Dokumentace Microsoftu
description: Tento článek popisuje systémové proměnné podporovaných službou Azure Data Factory. Tyto proměnné můžete použít ve výrazech, při definování entit služby Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 9a4d5acfe16a2fdbb3b631cb8baf6cb8e90a7d58
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016283"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systémové proměnné podporovaných službou Azure Data Factory
Tento článek popisuje systémové proměnné podporovaných službou Azure Data Factory. Tyto proměnné můžete použít ve výrazech, při definování entit služby Data Factory.

## <a name="pipeline-scope"></a>Obor kanálu
Tyto proměnné systému může být odkazováno kdekoli v kódu JSON kanálu.

| Název proměnné | Popis |
| --- | --- |
| @pipeline(). DataFactory |Název datové továrny spuštění kanálu je spuštěna v rámci |
| @pipeline(). Kanál |Název kanálu |
| @pipeline().RunId | ID konkrétní spuštění kanálu |
| @pipeline(). TriggerType | Typ aktivační události, která vyvolá kanál (ruční, plánovač) |
| @pipeline(). SpuštěcíID| ID aktivační událost, která volá kanál |
| @pipeline(). Název aktivační události| Název aktivační události, která volá kanál |
| @pipeline(). TriggerTime| Čas, kdy aktivační událost, která vyvolala kanálu. Čas aktivační události je skutečný čas aktivace, ne naplánovaném čase. Například `13:20:08.0149599Z` dochází místo `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Obor aktivační události plánu
Tyto proměnné systému může být odkazováno kdekoli v triggeru JSON-li aktivační událost není typu: "ScheduleTrigger."

| Název proměnné | Popis |
| --- | --- |
| @trigger.scheduledTime) |Čas naplánovaného aktivační události k vyvolání spuštění kanálu. Například pro aktivační událost, který se aktivuje každých 5 minut by vrátil tuto proměnnou `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z` v uvedeném pořadí.|
| @trigger.startTime) |Čas, kdy se aktivační událost **skutečně** aktivuje k vyvolání spuštění kanálu. Například pro trigger, který se aktivuje každých 5 minut, tato proměnná může vrátit asi takhle nějak. `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z` v uvedeném pořadí.|

## <a name="tumbling-window-trigger-scope"></a>Aktivační událost pro přeskakující okno oboru
Tyto proměnné systému může být odkazováno kdekoli v triggeru JSON-li aktivační událost není typu: "TumblingWindowTrigger."

| Název proměnné | Popis |
| --- | --- |
| @trigger(). outputs.windowStartTime |Spuštění okna při aktivační událost byla naplánována k vyvolání spuštění kanálu. Pokud pro přeskakující okno má frekvenci "každou hodinu" to může být čas na začátek hodiny.|
| @trigger(). outputs.windowEndTime |Konec časového období při aktivační událost byla naplánována k vyvolání spuštění kanálu. Pokud pro přeskakující okno má frekvenci "každou hodinu" to může být čas na konec hodiny.|
## <a name="next-steps"></a>Další postup
Informace o tom, jak jsou tyto proměnné použít ve výrazech naleznete v tématu [jazyk výrazů & funkce](control-flow-expression-language-functions.md).
