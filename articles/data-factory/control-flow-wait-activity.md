---
title: Aktivita čekání v Azure Data Factory | Microsoft Docs
description: Aktivita čekání pozastaví provádění kanálu za zadané období.
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
ms.openlocfilehash: 48a722979e61209a855dd1fec22fcdcc756ae1ce
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142423"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Spustit aktivitu čekání v Azure Data Factory
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

Vlastnost | Popis | Povolené hodnoty | Požadováno
-------- | ----------- | -------------- | --------
name | `Wait` Název aktivity | Řetězec | Ano
type | Musí být nastavené na **čekání**. | Řetězec | Ano
waitTimeInSeconds | Počet sekund, po které bude kanál čekat, než bude pokračovat ve zpracování. | Integer | Ano

## <a name="example"></a>Příklad

> [!NOTE]
> Tato část poskytuje definice JSON a ukázkové příkazy PowerShellu pro spuštění kanálu. Návod s podrobnými pokyny k vytvoření Data Factory kanálu pomocí definic Azure PowerShell a JSON najdete v tématu [kurz: vytvoření datové továrny pomocí Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Kanál s aktivitou čekání
V tomto příkladu má kanál dvě aktivity: **Do** a **počkejte**. Aktivita čekání je nakonfigurovaná tak, aby čekala jednu sekundu. Kanál spustí aktivitu webu ve smyčce s jednou sekundou čekací dobou mezi jednotlivými spuštěními. 

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

## <a name="next-steps"></a>Další postup
Podívejte se na další aktivity toku řízení podporované Data Factory: 

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
