---
title: Nastavit aktivitu proměnné v Azure Data Factory
description: Naučte se používat aktivitu nastavit proměnnou k nastavení hodnoty existující proměnné definované v kanálu Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/07/2020
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.openlocfilehash: 113829dd35c14b5efae39c55a8085dcd2c1ecef4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786154"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Nastavit aktivitu proměnné v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivitu nastavit proměnnou použijte k nastavení hodnoty existující proměnné typu String, bool nebo Array definovaného v kanálu Data Factory.

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Vyžadováno
-------- | ----------- | --------
name | Název aktivity v kanálu | ano
description | Text popisující, co aktivita dělá | ne
typ | Musí být nastavené na **SetVariable** | ano
hodnota | Hodnota řetězcového literálu nebo objektu výrazu, ke kterému je přiřazena proměnná | ano
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
