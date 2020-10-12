---
title: Nastavit aktivitu proměnné v Azure Data Factory
description: Naučte se používat aktivitu nastavit proměnnou k nastavení hodnoty existující proměnné definované v kanálu Data Factory.
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
ms.openlocfilehash: a0b5fa16658d3e354bcb4f90ad998997fc844a84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83832777"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Nastavit aktivitu proměnné v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivitu nastavit proměnnou použijte k nastavení hodnoty existující proměnné typu String, bool nebo Array definovaného v kanálu Data Factory.

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Povinné
-------- | ----------- | --------
name | Název aktivity v kanálu | ano
Popis | Text popisující, co aktivita dělá | ne
typ | Musí být nastavené na **SetVariable** | ano
value | Hodnota řetězcového literálu nebo objektu výrazu, ke kterému je přiřazena proměnná | ano
variableName | Název proměnné, která je nastavená touto aktivitou | ano

## <a name="incrementing-a-variable"></a>Zvýšení proměnné

Běžný scénář zahrnující proměnné v Azure Data Factory používá proměnnou jako iterátor v rámci aktivity do nebo foreach. V aktivitě set Variable nelze odkazovat na proměnnou, která je nastavena v `value` poli. Chcete-li toto omezení vyřešit, nastavte dočasnou proměnnou a pak vytvořte druhou aktivitu sady proměnných. Druhá aktivita sady proměnných nastaví hodnotu iterátoru na dočasnou proměnnou. 

Níže je uveden příklad tohoto vzoru:

![Zvýšit proměnnou](media/control-flow-set-variable-activity/increment-variable.png "Zvýšit proměnnou")

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
Přečtěte si o související aktivitě toku ovládacích prvků, kterou podporuje Data Factory: 

- [Aktivita připojení proměnné](control-flow-append-variable-activity.md)
