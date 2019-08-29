---
title: Ověřovací aktivita v Azure Data Factory | Microsoft Docs
description: Ověřovací aktivita nepokračuje v provádění kanálu, dokud neověřuje připojenou datovou sadu s určitými kritérii, které uživatel zadá.
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
ms.openlocfilehash: 77fdab04e03429d135875cb2ef223e8c23d312a2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141609"
---
# <a name="validation-activity-in-azure-data-factory"></a>Ověřovací aktivita v Azure Data Factory
Ověřování v kanálu můžete použít k tomu, abyste zajistili, že kanál bude pokračovat jenom po ověření, že existuje odkaz na připojenou datovou sadu, který splňuje zadaná kritéria, nebo se dosáhlo vypršení časového limitu.


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

Vlastnost | Popis | Povolené hodnoty | Požadováno
-------- | ----------- | -------------- | --------
name | Název aktivity ověření | Řetězec | Ano |
type | Musí být nastavené na **ověřování**. | Řetězec | Ano |
integrován | Aktivita bude blokovat provádění, dokud neověří, zda existuje tento odkaz na datovou sadu a zda splňuje zadaná kritéria, nebo byl dosažen časový limit. Zadaná datová sada by měla podporovat vlastnost "MinimumSize" nebo "ChildItems". | Odkaz na datovou sadu | Ano |
časový limit | Určuje časový limit pro spuštění aktivity. Pokud není zadaná žádná hodnota, výchozí hodnota je 7 dní ("7.00:00:00"). Formát je d. hh: mm: SS | Řetězec | Ne |
spat | Zpoždění v sekundách mezi pokusy o ověření. Pokud není zadaná žádná hodnota, výchozí hodnota je 10 sekund. | Integer | Ne |
childItems | Kontroluje, zda složka obsahuje podřízené položky. Lze nastavit na hodnotu-true: Ověřte, zda složka existuje a zda obsahuje položky. Blokuje, dokud není k dispozici alespoň jedna položka ve složce nebo hodnota časového limitu.-false: Ověřte, že složka existuje a že je prázdná. Blokuje, dokud není složka prázdná, nebo dokud nedosáhnete hodnoty časového limitu. Pokud není zadaná žádná hodnota, aktivita se zablokuje, dokud složka neexistuje nebo dokud nebude dosaženo časového limitu. | Logická hodnota | Ne |
minimumSize | Minimální velikost souboru v bajtech Pokud není zadaná žádná hodnota, výchozí hodnota je 0 bajtů. | Integer | Ne |


## <a name="next-steps"></a>Další postup
Podívejte se na další aktivity toku řízení podporované Data Factory:

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
