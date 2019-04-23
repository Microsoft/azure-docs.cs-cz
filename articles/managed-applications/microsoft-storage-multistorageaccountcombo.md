---
title: Azure prvek uživatelského rozhraní MultiStorageAccountCombo | Dokumentace Microsoftu
description: Popisuje element Microsoft.Storage.MultiStorageAccountCombo uživatelského rozhraní pro Azure portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 08b65770414e9ee1cb5e478427fe7654b2bb9a78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252432"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI element
Skupina ovládacích prvků pro vytváření několika účty úložiště s názvy, které začínají běžnou předponu.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Poznámky
- Hodnota pro `defaultValue.prefix` je zřetězená s celými čísly jeden nebo více ke generování pořadí v názvech účtů úložiště. Například pokud `defaultValue.prefix` je **sa** a `count` je **2**, pak názvy účtů úložiště **SA 1** a **SA 2** jsou generovány. Názvy účtů generované úložiště se automaticky ověří jedinečný.
- Názvy účtů úložiště jsou generovány na základě lexikograficky `count`. Například pokud `count` je 10 a názvy účtů úložiště končit dvěma číslicemi celá čísla (01, 02, 03).
- Výchozí hodnota pro `defaultValue.prefix` je **null**a pro `defaultValue.type` je **Premium_LRS**.
- Jakýkoli typ není zadáno v `constraints.allowedTypes` je skrytá a libovolný typ není zadáno v `constraints.excludedTypes` se zobrazí. `constraints.allowedTypes` a `constraints.excludedTypes` jsou volitelné, ale nelze používat současně.
- Kromě vytváření názvy účtů úložiště, `count` slouží k nastavení odpovídající multiplikátor pro element. Statická hodnota, jako je třeba podporuje **2**, nebo dynamickou hodnotu z jiných prvků, jako je `[steps('step1').storageAccountCount]`. Výchozí hodnota je **1**.

## <a name="sample-output"></a>Ukázkový výstup

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).
* Popis společné vlastnosti v prvcích uživatelského rozhraní, naleznete v tématu [CreateUiDefinition prvky](create-uidefinition-elements.md).
