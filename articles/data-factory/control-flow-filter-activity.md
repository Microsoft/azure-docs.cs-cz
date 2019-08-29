---
title: Filtrovat aktivitu v Azure Data Factory | Microsoft Docs
description: Aktivita filtru filtruje vstupy.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/04/2018
ms.openlocfilehash: a7e2e735baa7e40b4170d3397327e90fc1a5d2d5
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141678"
---
# <a name="filter-activity-in-azure-data-factory"></a>Filtrovat aktivitu v Azure Data Factory
Můžete použít aktivitu filtru v kanálu k použití výrazu filtru na vstupní pole. 

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Povolené hodnoty | Požadováno
-------- | ----------- | -------------- | --------
name | `Filter` Název aktivity | Řetězec | Ano
type | Musí být nastavené na **Filter**. | Řetězec | Ano
condition | Podmínka, která se má použít pro filtrování vstupu. | Výraz | Ano
items | Vstupní pole, ve kterém se má použít filtr | Výraz | Ano

## <a name="example"></a>Příklad

V tomto příkladu má kanál dvě aktivity: **Filtrovat** a **foreach** Aktivita filtru je nakonfigurována tak, aby vyfiltroval vstupní pole pro položky s hodnotou větší než 3. Aktivita ForEach pak provede iteraci filtrovaných hodnot a počká na počet sekund zadaný aktuální hodnotou.

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
                "name": "MyForEach",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "batchCount": 1,
                    "items": "@activity('MyFilterActivity').output.value",
                    "activities": [{
                        "type": "Wait",
                        "typeProperties": {
                            "waitTimeInSeconds": "@item()"
                        },
                        "name": "MyWaitActivity"
                    }]
                },
                "dependsOn": [{
                    "activity": "MyFilterActivity",
                    "dependencyConditions": ["Succeeded"]
                }]
            }
        ],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        }
    }
}
```

## <a name="next-steps"></a>Další kroky
Podívejte se na další aktivity toku řízení podporované Data Factory: 

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
