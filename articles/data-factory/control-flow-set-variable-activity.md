---
title: Nastavení aktivity proměnných ve Službě Azure Data Factory
description: Přečtěte si, jak pomocí aktivity Nastavit proměnnou nastavit hodnotu nastavit hodnotu existující proměnné definované v kanálu datové továrny.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/07/2020
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: e736cc95628bd0e15bdb7ffd425608278788c353
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879257"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Nastavení aktivity proměnných ve Službě Azure Data Factory

Aktivita Nastavit proměnnou slouží k nastavení hodnoty existující proměnné typu String, Bool nebo Array definované v kanálu Datové továrny.

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
jméno | Název aktivity v kanálu | ano
description | Text popisující, co aktivita dělá | ne
type | Musí být nastavena na **SetVariable** | ano
value | Hodnota objektu literálu řetězce nebo výrazu, ke které bude proměnná přiřazena | ano
variableName | Název proměnné, která bude nastavena touto aktivitou | ano

## <a name="incrementing-a-variable"></a>Zvýšení proměnné

Běžný scénář zahrnující proměnné v Azure Data Factory používá proměnnou jako iterátor v rámci do nebo foreach aktivity. V aktivitě nastavené proměnné nelze odkazovat `value` na proměnnou nastavenou v poli. Chcete-li toto omezení vyřešit, nastavte dočasnou proměnnou a vytvořte aktivitu proměnné druhé sady. Druhá nastavená proměnná aktivita nastaví hodnotu iterátoru na dočasnou proměnnou. 

Níže je uveden příklad tohoto vzoru:

![Proměnná přírůstek](media/control-flow-set-variable-activity/increment-variable.png "Proměnná přírůstek")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>Další kroky
Informace o související aktivitě toku řízení podporované aplikací Data Factory: 

- [Aktivita připojení proměnné](control-flow-append-variable-activity.md)
