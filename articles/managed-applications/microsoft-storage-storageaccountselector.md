---
title: Element uživatelského rozhraní Azure StorageAccountSelector | Microsoft Docs
description: Popisuje prvek uživatelského rozhraní Microsoft. Storage. StorageAccountSelector pro Azure Portal.
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
ms.openlocfilehash: e1f96b42e58bcb09cfc2836c993626a889669fc0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151454"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft. Storage. StorageAccountSelector – element uživatelského rozhraní

Ovládací prvek pro výběr nového nebo existujícího účtu úložiště.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Ovládací prvek zobrazuje výchozí hodnotu.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

Ovládací prvek umožňuje uživateli vytvořit nový účet úložiště nebo vybrat existující účet úložiště.

![Microsoft. Storage. StorageAccountSelector – nové](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

## <a name="schema"></a>Schéma

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="remarks"></a>Poznámky

- Je-li tento parametr zadán, je `defaultValue.name` automaticky ověřována jedinečnost. Pokud název účtu úložiště není jedinečný, musí uživatel zadat jiný název nebo zvolit existující účet úložiště.
- Výchozí hodnota pro `defaultValue.type` je **Premium_LRS**.
- Libovolný typ, který není zadán v `constraints.allowedTypes` je skrytý, a je zobrazen libovolný typ, který není zadán v `constraints.excludedTypes`. `constraints.allowedTypes` a `constraints.excludedTypes` jsou obě volitelné, ale nejde je použít současně.
- Pokud má `options.hideExisting` **hodnotu true**, nemůže uživatel zvolit existující účet úložiště. Výchozí hodnota je **false**.

## <a name="next-steps"></a>Další kroky
* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
