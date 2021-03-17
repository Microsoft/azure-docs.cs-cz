---
title: Systémové proměnné v Azure Data Factory
description: Tento článek popisuje systémové proměnné, které podporuje Azure Data Factory. Tyto proměnné můžete použít ve výrazech při definování Data Factory entit.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 119ecb3ec9c208340f09f513bf10b3ad24312cb5
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201222"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systémové proměnné podporované nástrojem Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje systémové proměnné, které podporuje Azure Data Factory. Tyto proměnné můžete použít ve výrazech při definování Data Factory entit.

## <a name="pipeline-scope"></a>Obor kanálu

Na tyto systémové proměnné se dá odkazovat kdekoli v kódu JSON kanálu.

| Název proměnné | Popis |
| --- | --- |
| @pipeline(). DataFactory |Název datové továrny, ve které běží běh kanálu. |
| @pipeline(). Kanálu |Název kanálu |
| @pipeline(). RunId |ID konkrétního spuštění kanálu |
| @pipeline(). TriggerType |Typ triggeru, který vyvolal kanál (například `ScheduleTrigger` , `BlobEventsTrigger` ). Seznam podporovaných typů triggerů najdete v tématu [spuštění kanálu a triggery v Azure Data Factory](concepts-pipeline-execution-triggers.md). Typ triggeru `Manual` označuje, že se kanál aktivoval ručně. |
| @pipeline(). TriggerId|ID triggeru, který vyvolal kanál |
| @pipeline(). TriggerName|Název triggeru, který vyvolal kanál. |
| @pipeline(). TriggerTime|Čas spuštění triggeru, který vyvolal kanál. Jedná se o čas, kdy Trigger **skutečně** vyvolal spuštění kanálu a může se mírně lišit od naplánovaného času triggeru.  |

>[!NOTE]
>Systémové proměnné data a času související s triggerem (v oborech kanálu a triggeru) vrátí data UTC ve formátu ISO 8601, například `2017-06-01T22:20:00.4061448Z` .

## <a name="schedule-trigger-scope"></a>Rozsah triggeru plánu

Na tyto systémové proměnné se dá odkazovat kdekoli v kódu JSON triggeru pro triggery typu [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger).

| Název proměnné | Popis |
| --- | --- |
| @trigger().scheduledTime |Čas, kdy byla aktivační událost naplánována k vyvolání spuštění kanálu. |
| @trigger(). Čas_spuštění |Čas, kdy se Trigger **skutečně** aktivoval pro vyvolání spuštění kanálu. Může se mírně lišit od naplánovaného času triggeru. |

## <a name="tumbling-window-trigger-scope"></a>Rozsah triggeru pro bubnový interval

Na tyto systémové proměnné se dá odkazovat kdekoli v kódu JSON triggeru pro triggery typu [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger).

| Název proměnné | Popis |
| --- | --- |
| @trigger(). Outputs. windowStartTime |Začátek okna přidruženého ke spuštění triggeru |
| @trigger(). Outputs. windowEndTime |Konec okna přidruženého ke spuštění triggeru |
| @trigger().scheduledTime |Čas, kdy byla aktivační událost naplánována k vyvolání spuštění kanálu. |
| @trigger(). Čas_spuštění |Čas, kdy se Trigger **skutečně** aktivoval pro vyvolání spuštění kanálu. Může se mírně lišit od naplánovaného času triggeru. |

## <a name="storage-event-trigger-scope"></a>Rozsah triggeru události úložiště

Na tyto systémové proměnné se dá odkazovat kdekoli v kódu JSON triggeru pro triggery typu [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger).

| Název proměnné | Popis |
| --- | --- |
| @triggerBody(). fileName  |Název souboru, jehož vytvořením nebo odstraněním způsobilo aktivaci triggeru.   |
| @triggerBody(). název_složky  |Cesta ke složce, která obsahuje soubor určený parametrem `@triggerBody().fileName` . První segment cesty ke složce je název kontejneru Azure Blob Storage.  |
| @trigger(). Čas_spuštění |Čas, kdy Trigger vyvolal vyvolání spuštění kanálu. |

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak se tyto proměnné používají ve výrazech, najdete v tématu [Expression language & Functions](control-flow-expression-language-functions.md).
* Postup použití systémových proměnných oboru triggeru v kanálu najdete v tématu [metadata triggeru reference v kanálu](how-to-use-trigger-parameterization.md) .
