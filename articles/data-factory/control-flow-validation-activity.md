---
title: Aktivita ověření ve Službě Azure Data Factory
description: Ověřovací aktivita nepokračuje v provádění kanálu, dokud neověří připojenou datovou sadu s určitými kritérii, která uživatel zadá.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 764b41d1823e8edce134c5099e066486f4f08acc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417925"
---
# <a name="validation-activity-in-azure-data-factory"></a>Aktivita ověření ve Službě Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ověření v kanálu můžete použít k zajištění kanálu pouze pokračuje v provádění, jakmile ověří připojený odkaz datové sady existuje, že splňuje zadaná kritéria nebo časový rozsah bylo dosaženo.


## <a name="syntax"></a>Syntaxe

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
jméno | Název "validační" činnosti | Řetězec | Ano |
type | Musí být nastavena na **ověření**. | Řetězec | Ano |
Dataset | Aktivita bude blokovat provádění, dokud neověří tento odkaz na datovou sadu existuje a že splňuje zadaná kritéria nebo bylo dosaženo časového parametru. Zadaný datový soubor by měl podporovat vlastnost "MinimumSize" nebo "ChildItems". | Odkaz na datovou sadu | Ano |
timeout | Určuje časový limit pro spuštění aktivity. Pokud není zadána žádná hodnota, výchozí hodnota je 7 dní ("7.00:00:00"). Formát je d.hh:mm:ss | Řetězec | Ne |
Spát | Zpoždění v sekundách mezi pokusy o ověření. Pokud není zadána žádná hodnota, výchozí hodnota je 10 sekund. | Integer | Ne |
podřízené položky | Zkontroluje, zda složka obsahuje podřízené položky. Lze nastavit na hodnotu true : Ověřte, zda složka existuje a zda obsahuje položky. Blokuje, dokud alespoň jedna položka je k dispozici ve složce nebo je dosaženo hodnoty časového času.-false: Ověření, že složka existuje a že je prázdný. Blokuje, dokud není složka prázdná nebo dokud není dosaženo hodnoty časového času. Pokud není zadána žádná hodnota, aktivita bude blokovat, dokud složka existuje nebo dokud není dosaženo časového času. | Logická hodnota | Ne |
Minimumsize | Minimální velikost souboru v bajtech. Pokud není zadána žádná hodnota, výchozí hodnota je 0 bajtů | Integer | Ne |


## <a name="next-steps"></a>Další kroky
Podívejte se na další aktivity toku řízení podporované factory:

- [Aktivita podmínky Když](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita For Each](control-flow-for-each-activity.md)
- [Aktivita získání metadat](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
