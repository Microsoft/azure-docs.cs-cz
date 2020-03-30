---
title: Aktivita čekání v Azure Data Factory
description: Wait aktivita pozastaví provádění kanálu za zadané období.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: f9dd53fded06eec169219d00993620a0f2aa2bf0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73678245"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Spuštění aktivity čekání v Azure Data Factory
Pokud v kanálu použijete aktivitu Wait, kanál před pokračováním v provádění dalších aktivit počká zadanou dobu. 

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
jméno | Název aktivity. `Wait` | Řetězec | Ano
type | Musí být nastavena na **Wait**. | Řetězec | Ano
waitTimeInSeconds | Počet sekund, které čeká kanálu před pokračováním zpracování. | Integer | Ano

## <a name="example"></a>Příklad

> [!NOTE]
> Tato část obsahuje definice JSON a ukázkové příkazy prostředí PowerShell pro spuštění kanálu. Návod s podrobnými pokyny k vytvoření kanálu Datové továrny pomocí definic Azure PowerShellu a JSON najdete [v kurzu: vytvoření datové továrny pomocí Azure PowerShellu](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Kanál s aktivitou Čekání
V tomto příkladu má kanál dvě aktivity: **Until** and **Wait**. Aktivita Wait je nakonfigurována tak, aby čekala jednu sekundu. Kanál spustí aktivitu webu ve smyčce s jednou sekundou čekací doby mezi jednotlivými spuštěními. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Další kroky
Podívejte se na další aktivity toku řízení podporované factory: 

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita For Each](control-flow-for-each-activity.md)
- [Získat aktivitu metadat](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
