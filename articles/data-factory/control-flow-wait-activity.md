---
title: Počkejte aktivity v Azure Data Factory | Microsoft Docs
description: Aktivity čekat pozastaví spuštění kanálu zadanou dobu.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 9eaa8f369c69468a9bd6f4c5403d6a11476e8595
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619010"
---
# <a name="wait-activity-in-azure-data-factory"></a>Počkejte aktivity v Azure Data Factory
Pokud v kanálu použijete aktivitu Wait, kanál před pokračováním v provádění dalších aktivit počká zadanou dobu. 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [Data Factory V1 dokumentaci](v1/data-factory-introduction.md).

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
jméno | Název `Wait` aktivity. | Řetězec | Ano
type | Musí být nastavena na **počkejte**. | Řetězec | Ano
waitTimeInSeconds | Počet sekund, po které čeká, než budete pokračovat zpracování kanálu. | Integer | Ano

## <a name="example"></a>Příklad:

> [!NOTE]
> Tato část obsahuje definice JSON a vzorové příkazy prostředí PowerShell ke spuštění kanálu. Návod s podrobné pokyny k vytvoření kanálu pro vytváření dat pomocí Azure PowerShell a JSON definice najdete v tématu [kurz: vytvoření objekt pro vytváření dat pomocí Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Kanál s aktivitou čekání
V tomto příkladu kanálu má dvě aktivity: **dokud** a **počkejte**. Aktivity čekat nastaven tak, aby Počkejte jednu sekundu. Kanál běží webové aktivity ve smyčce s sekundu čekání na čas mezi každé spuštění. 

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
Najdete v části Další aktivity toku řízení podporovaných službou Data Factory: 

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webové aktivity](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
