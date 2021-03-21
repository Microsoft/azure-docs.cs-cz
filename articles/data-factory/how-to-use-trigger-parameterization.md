---
title: Předat informace o triggeru kanálu
description: Informace o tom, jak odkazovat na metadata triggeru v kanálu
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 50a9f9cd59ebeecae89580c878442eb20788f462
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593641"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>Metadata triggeru reference v spuštěních kanálu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak lze v běhu kanálu použít metadata triggeru, jako je čas spuštění triggeru.

Kanál někdy potřebuje pochopit a číst metadata z triggeru, která je vyvolá. Například při spuštění aktivační události pro bubny v závislosti na počátečním a koncovém čase okna kanál zpracuje různé datové řezy nebo složky. V Azure Data Factory používáme Parametrizace a [systémová proměnná](control-flow-system-variables.md) k předání metadat z triggeru do kanálu.

Tento model je zvláště užitečný pro [aktivační událost bubnu](how-to-create-tumbling-window-trigger.md), kde Trigger poskytuje počáteční a koncový čas okna a [vlastní Trigger události](how-to-create-custom-event-trigger.md), kde Trigger aktivuje a zpracovává hodnoty ve [vlastním definovaném _datovém_ poli](../event-grid/event-schema.md).

> [!NOTE]
> Jiný typ triggeru poskytuje různé informace o metadatech. Další informace najdete v tématu [systémová proměnná](control-flow-system-variables.md) .

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory

V této části se dozvíte, jak předat informace o metadatech z triggeru do kanálu v rámci Azure Data Factory uživatelského rozhraní.

1. Přejít na **plátno pro vytváření obsahu** a upravit kanál

1. Kliknutím na prázdné plátno zobrazte nastavení kanálu. Nevybírejte žádnou aktivitu. Je možné, že budete muset vyžádat panel nastavení z dolní části plátna, jak je možné ho sbalit.

1. Vyberte oddíl **Parameters** a vyberte **+ Nová** a přidejte parametry.

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="Snímek obrazovky s nastavením kanálu ukazující, jak definovat parametry v kanálu":::

1. Kliknutím na **+ Trigger** přidejte triggery do kanálu.

1. Vytvořte nebo připojte Trigger k kanálu a klikněte na **OK** .

1. Na následující stránce vyplňte metadata triggeru pro každý parametr. K načtení informací o aktivační události použijte formát definovaný v [systémové proměnné](control-flow-system-variables.md) . Nemusíte vyplnit informace pro všechny parametry, a to pouze ty, které budou předpokládat hodnoty metadat triggeru. Tady například přiřadíme počáteční čas spuštění triggeru *parameter_1*.

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="Obrazovka stránky definice triggeru, která ukazuje, jak předat aktivační informace k parametrům kanálu.":::

1. Chcete-li použít hodnoty v kanálu, využijte parametry _@pipeline (). Parameters. ParameterName_, __nikoli__ systémovou proměnnou v definicích kanálu. Například v našem případě se pro čtení počátečního času triggeru odkazujeme @pipeline () .Parameters.parameter_1.

## <a name="json-schema"></a>Schéma JSON

Aby bylo možné předávat informace aktivační události do spuštění kanálu, musí být aktivační událost i JSON kanálu aktualizovány pomocí oddílu _Parameters_ .

### <a name="pipeline-definition"></a>Definice kanálu

V části **vlastnosti** přidejte do oddílu **Parameters** definice parametrů.

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>Definice aktivační události

V části **kanály** přiřaďte hodnoty parametrů v oddílu **Parameters** . Nemusíte vyplnit informace pro všechny parametry, a to pouze ty, které budou předpokládat hodnoty metadat triggeru.

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>Použití aktivačních informací v kanálu

Chcete-li použít hodnoty v kanálu, využijte parametry _@pipeline (). Parameters. ParameterName_, __nikoli__ systémovou proměnnou v definicích kanálu.

## <a name="next-steps"></a>Další kroky

Podrobné informace o aktivačních událostech najdete v tématu [spuštění kanálu a triggery](concepts-pipeline-execution-triggers.md#trigger-execution).
