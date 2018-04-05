---
title: Azure elementu MultiStorageAccountCombo uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Storage.MultiStorageAccountCombo uživatelského rozhraní pro portál Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: c395c076a4910e124c1b93ebc61b5e491b2b53ff
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Element Microsoft.Storage.MultiStorageAccountCombo uživatelského rozhraní
Skupina ovládacích prvků pro vytváření více účtů úložiště, jejichž názvy začínají s předponou běžné.

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
- Hodnota `defaultValue.prefix` je zřetězen s jeden nebo více celých čísel na generování pořadí názvy účtů úložiště. Například pokud `defaultValue.prefix` je **foobar** a `count` je **2**, pak názvy účtů úložiště **foobar1** a **foobar2** se generují. Názvy účtů úložiště generovaného ověření jedinečnosti automaticky.
- Názvy účtů úložiště jsou generovány lexicographically podle `count`. Například pokud `count` je 10 a názvy účtů úložiště končit celá čísla 2 číslice (01, 02, 03, atd.).
- Výchozí hodnota pro `defaultValue.prefix` je **null**a pro `defaultValue.type` je **Premium_LRS**.
- Žádný typ, nebyly zadány v `constraints.allowedTypes` skryt a jakýmikoli nebyly zadány v `constraints.excludedTypes` se zobrazí.
`constraints.allowedTypes` a `constraints.excludedTypes` obě jsou nepovinné, ale nelze používat současně.
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
