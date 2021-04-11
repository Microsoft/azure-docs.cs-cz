---
title: Systémové proměnné v Azure Data Factory
description: Tento článek popisuje systémové proměnné, které podporuje Azure Data Factory. Tyto proměnné můžete použít ve výrazech při definování Data Factory entit.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: b85efa7ac4481ab9eb2b2637aee7d9e5e76e8f3f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786052"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systémové proměnné podporované nástrojem Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje systémové proměnné, které podporuje Azure Data Factory. Tyto proměnné můžete použít ve výrazech při definování Data Factory entit.

## <a name="pipeline-scope"></a>Obor kanálu

Na tyto systémové proměnné se dá odkazovat kdekoli v kódu JSON kanálu.

| Název proměnné | Description |
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

| Název proměnné | Description |
| --- | --- |
| @trigger().scheduledTime |Čas, kdy byla aktivační událost naplánována k vyvolání spuštění kanálu. |
| @trigger(). Čas_spuštění |Čas, kdy se Trigger **skutečně** aktivoval pro vyvolání spuštění kanálu. Může se mírně lišit od naplánovaného času triggeru. |

## <a name="tumbling-window-trigger-scope"></a>Rozsah triggeru pro bubnový interval

Na tyto systémové proměnné se dá odkazovat kdekoli v kódu JSON triggeru pro triggery typu [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger).

| Název proměnné | Description |
| --- | --- |
| @trigger(). Outputs. windowStartTime |Začátek okna přidruženého ke spuštění triggeru |
| @trigger(). Outputs. windowEndTime |Konec okna přidruženého ke spuštění triggeru |
| @trigger().scheduledTime |Čas, kdy byla aktivační událost naplánována k vyvolání spuštění kanálu. |
| @trigger(). Čas_spuštění |Čas, kdy se Trigger **skutečně** aktivoval pro vyvolání spuštění kanálu. Může se mírně lišit od naplánovaného času triggeru. |

## <a name="storage-event-trigger-scope"></a>Rozsah triggeru události úložiště

Na tyto systémové proměnné se dá odkazovat kdekoli v kódu JSON triggeru pro triggery typu [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger).

| Název proměnné | Description |
| --- | --- |
| @triggerBody(). fileName  |Název souboru, jehož vytvořením nebo odstraněním způsobilo aktivaci triggeru.   |
| @triggerBody(). název_složky  |Cesta ke složce, která obsahuje soubor určený parametrem `@triggerBody().fileName` . První segment cesty ke složce je název kontejneru Azure Blob Storage.  |
| @trigger(). Čas_spuštění |Čas, kdy Trigger vyvolal vyvolání spuštění kanálu. |

## <a name="custom-event-trigger-scope"></a>Rozsah triggeru vlastní události

Na tyto systémové proměnné se dá odkazovat kdekoli v kódu JSON triggeru pro triggery typu [CustomEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger).

>[!NOTE]
>Azure Data Factory očekává, že se vlastní událost naformátuje pomocí [schématu události Azure Event Grid](../event-grid/event-schema.md).

| Název proměnné | Description
| --- | --- |
| @triggerBody(). Event. eventType | Typ událostí, které spustily vlastní spuštění triggeru událostí. Typ události je pole definované zákazníkem a přebírat se všemi hodnotami typu String. |
| @triggerBody(). Event. Subject | Předmět vlastní události, která způsobila, že se Trigger aktivuje. |
| @triggerBody(). Event. data. _Název_klíče_ | Datové pole ve vlastní události je bezplatné od objektu BLOB JSON, který může zákazník použít k posílání zpráv a dat. Použijte prosím data. _KeyName_ , který odkazuje na jednotlivá pole. Například @triggerBody (). Event. data. callback vrátí hodnotu pole _zpětného volání_ uložené v části _data_. |
| @trigger(). Čas_spuštění | Čas, kdy Trigger vyvolal vyvolání spuštění kanálu. |

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak se tyto proměnné používají ve výrazech, najdete v tématu [Expression language & Functions](control-flow-expression-language-functions.md).
* Postup použití systémových proměnných oboru triggeru v kanálu najdete v tématu [metadata triggeru reference v kanálu](how-to-use-trigger-parameterization.md) .
