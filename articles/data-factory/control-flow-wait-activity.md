---
title: Aktivita čekání v Azure Data Factory
description: Aktivita čekání pozastaví provádění kanálu za zadané období.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 12c9027067c00a3db84e6610a0d73090cc011713
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485905"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Spustit aktivitu čekání v Azure Data Factory
Pokud v kanálu použijete aktivitu Wait, kanál před pokračováním v provádění dalších aktivit počká zadanou dobu. 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>Syntax

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

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | --------
name | Název `Wait` aktivity | Řetězec | Yes
typ | Musí být nastavené na **čekání**. | Řetězec | Yes
waitTimeInSeconds | Počet sekund, po které bude kanál čekat, než bude pokračovat ve zpracování. | Integer | Yes

## <a name="example"></a>Příklad

> [!NOTE]
> Tato část poskytuje definice JSON a ukázkové příkazy PowerShellu pro spuštění kanálu. Návod s podrobnými pokyny k vytvoření Data Factory kanálu pomocí definic Azure PowerShell a JSON najdete v tématu [kurz: vytvoření datové továrny pomocí Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Kanál s aktivitou čekání
V tomto příkladu má kanál dvě aktivity: **do** a **Wait**. Aktivita čekání je nakonfigurovaná tak, aby čekala jednu sekundu. Kanál spustí aktivitu webu ve smyčce s jednou sekundou čekací dobou mezi jednotlivými spuštěními. 

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
Podívejte se na další aktivity toku řízení podporované Data Factory: 

- [Aktivita podmínky Když](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita For Each](control-flow-for-each-activity.md)
- [Aktivita získání metadat](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
