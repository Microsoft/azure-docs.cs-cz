---
title: Azure elementu MultiStorageAccountCombo uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Storage.MultiStorageAccountCombo uživatelského rozhraní pro portál Azure.
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
ms.openlocfilehash: f5fa81d53e1728e8f566a2a39aed8311828b20c7
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37108701"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Element Microsoft.Storage.MultiStorageAccountCombo uživatelského rozhraní
Skupina ovládacích prvků pro vytváření několik účtů úložiště s názvy, které začínají běžné předponou.

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
- Hodnota `defaultValue.prefix` je zřetězen s jeden nebo více celých čísel na generování pořadí názvy účtů úložiště. Například pokud `defaultValue.prefix` je **sa** a `count` je **2**, pak názvy účtů úložiště **SA 1** a **SA 2** se generují. Názvy účtů úložiště generovaného ověření jedinečnosti automaticky.
- Názvy účtů úložiště jsou generovány lexicographically podle `count`. Například pokud `count` je 10 a názvy účtů úložiště končit letopočty celých čísel (01, 02, 03).
- Výchozí hodnota pro `defaultValue.prefix` je **null**a pro `defaultValue.type` je **Premium_LRS**.
- Žádný typ, nebyly zadány v `constraints.allowedTypes` skryt a jakýmikoli nebyly zadány v `constraints.excludedTypes` se zobrazí. `constraints.allowedTypes` a `constraints.excludedTypes` obě jsou nepovinné, ale nelze používat současně.
- Kromě vytváření názvy účtů úložiště `count` slouží k nastavení odpovídající multiplikátor pro element. Podporuje statické hodnoty, jako je třeba **2**, nebo jako dynamické hodnoty z jiný element `[steps('step1').storageAccountCount]`. Výchozí hodnota je **1**.

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
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
