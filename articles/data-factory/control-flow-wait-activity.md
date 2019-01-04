---
title: Počkejte aktivity ve službě Azure Data Factory | Dokumentace Microsoftu
description: Aktivita Wait pozastaví provádění kanál zadanou dobu.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 731df55a11f4671670a65dac8a83927d81da454c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015793"
---
# <a name="wait-activity-in-azure-data-factory"></a>Aktivita ve službě Azure Data Factory wait
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

## <a name="type-properties"></a>Typ vlastnosti

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
jméno | Název `Wait` aktivity. | Řetězec | Ano
type | Musí být nastaveno na **počkejte**. | Řetězec | Ano
waitTimeInSeconds | Počet sekund, po které kanálu počká, než budete pokračovat se zpracováním. | Integer | Ano

## <a name="example"></a>Příklad:

> [!NOTE]
> Tato část obsahuje definice JSON a vzorové příkazy prostředí PowerShell pro spuštění kanálu. Návod s podrobnými pokyny k vytvoření kanálu datové továrny pomocí Azure Powershellu a JSON definic najdete v tématu [kurz: vytvoření datové továrny pomocí Azure Powershellu](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Kanál s aktivitou čekání
V tomto příkladu tento kanál obsahuje dvě aktivity: **Dokud** a **počkejte**. Čekání na jedné sekundy je nakonfigurovaný aktivitu čekání. Aktivita webu ve smyčce spuštění kanálu pomocí jedné sekundy čekání mezi každé spuštění. 

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
Zobrazit další aktivity toku řízení podporovaných službou Data Factory: 

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
