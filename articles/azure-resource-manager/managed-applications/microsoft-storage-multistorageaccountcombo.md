---
title: MultiStorageAccountCombo – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Storage. MultiStorageAccountCombo pro Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 06412a1f08f1f242a3f3bd9be17b795ee09fcf9d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "75651875"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft. Storage. MultiStorageAccountCombo – element uživatelského rozhraní

Skupina ovládacích prvků pro vytváření několika účtů úložiště s názvy, které začínají společnou předponou.

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

## <a name="sample-output"></a>Ukázkový výstup

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>Poznámky

- Hodnota pro `defaultValue.prefix` je zřetězena s jedním nebo více celými čísly, aby bylo možné vytvořit posloupnost názvů účtů úložiště. Například pokud `defaultValue.prefix` je **SA** a `count` **2**, vygenerují se názvy účtů úložiště **SA1** a **SA2** . Vygenerované názvy účtů úložiště mají automatické ověření jedinečnosti.
- Názvy účtů úložiště se generují lexikograficky na základě `count` . Pokud `count` je například 10, pak názvy účtů úložiště končí dvěma číslicemi (01, 02, 03).
- Výchozí hodnota pro `defaultValue.prefix` je **null**a pro `defaultValue.type` je **Premium_LRS**.
- Libovolný typ, který není zadán v `constraints.allowedTypes` , je skrytý a je zobrazen libovolný typ, který není zadán v `constraints.excludedTypes` . `constraints.allowedTypes`a `constraints.excludedTypes` jsou zároveň volitelné, ale nelze je použít současně.
- Kromě generování názvů účtů úložiště `count` se používá k nastavení vhodného násobitele pro element. Podporuje statickou hodnotu, například **2**, nebo dynamickou hodnotu z jiného elementu, například `[steps('step1').storageAccountCount]` . Výchozí hodnota je **1**.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
