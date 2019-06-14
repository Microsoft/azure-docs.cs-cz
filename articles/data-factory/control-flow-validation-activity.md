---
title: Aktivita ověření ve službě Azure Data Factory | Dokumentace Microsoftu
description: Aktivita ověření dál spuštění kanálu, dokud ověří připojené datové sady s určitá kritéria, který uživatel zadá.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60764318"
---
# <a name="validation-activity-in-azure-data-factory"></a>Aktivita ověření ve službě Azure Data Factory
Ověřování v kanálu můžete použít k zajištění kanálu pouze pokračuje v provádění po ověří připojeného existuje odkaz na datovou sadu, že splňují zadaná kritéria nebo bylo dosaženo časového limitu.


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


## <a name="type-properties"></a>Typ vlastnosti

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
name | Název aktivity "ověření. | String | Ano |
type | Musí být nastaveno na **ověření**. | String | Ano |
Datové sady | Aktivity se zablokovat spuštění, dokud se ověřila tento odkaz na datovou sadu existuje a zda splňují zadaná kritéria, nebo bylo dosaženo časového limitu. Vlastnost "MinimumSize" nebo "ChildItems" by měla podporovat datové sady k dispozici. | Odkaz na datovou sadu | Ano |
timeout | Určuje časový limit pro spuštění aktivity. Pokud není zadána žádná hodnota, výchozí hodnota je 7 dnů ("7.00:00:00"). Formát je d.hh:mm:ss | String | Ne |
Přejít do režimu spánku | Zpoždění v sekundách mezi pokusy o ověření. Pokud není zadána žádná hodnota, výchozí hodnota je 10 sekund. | Integer | Ne |
childItems | Kontroluje, jestli složka má podřízené položky. Lze nastavit na hodnotu true: Ověřte, že složka existuje a že má položky. Blokuje, dokud alespoň jedna položka nachází ve složce nebo nebude dosaženo hodnoty časového limitu.-false: Ověřte, že složka existuje a že je prázdný. Blokuje, dokud se složka je prázdná nebo až do vypršení časového limitu nebude dosaženo hodnoty. Pokud není zadána žádná hodnota, aktivita bude blokovat, dokud složka existuje, nebo dokud nebude dosaženo časového limitu. | Boolean | Ne |
minimumSize | Minimální velikost souboru v bajtech. Pokud není zadána žádná hodnota, výchozí hodnota je 0 bajtů. | Integer | Ne |


## <a name="next-steps"></a>Další postup
Zobrazit další aktivity toku řízení podporovaných službou Data Factory:

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
