---
title: ArmApiControl – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Solutions. ArmApiControl pro Azure Portal. Používá se pro volání operací rozhraní API.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: fdc299ef1945e3ee0810f1c314fc07edfb4f4873
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313296"
---
# <a name="microsoftsolutionsarmapicontrol-ui-element"></a>Microsoft. Solutions. ArmApiControl – element uživatelského rozhraní

ArmApiControl umožňuje získat výsledky z operace Azure Resource Manager API. Výsledky použijte k naplnění dynamického obsahu v jiných ovládacích prvcích.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Pro tento ovládací prvek není k dispozici žádné uživatelské rozhraní.

## <a name="schema"></a>Schéma

Následující příklad ukazuje schéma pro tento ovládací prvek:

```json
{
    "name": "testApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "{HTTP-method}",
        "path": "{path-for-the-URL}",
        "body": {
            "key1": "val1",
            "key2": "val2"
        }
    }
}
```

## <a name="sample-output"></a>Ukázkový výstup

Výstup ovládacího prvku se uživateli nezobrazí. Místo toho se výsledek operace používá v jiných ovládacích prvcích.

## <a name="remarks"></a>Poznámky

- `request.method`Vlastnost určuje metodu HTTP. `GET` `POST` Jsou povoleny pouze nebo.
- `request.path`Vlastnost určuje adresu URL, která musí být relativní cestou ke koncovému bodu ARM. Může se jednat o statickou cestu nebo může být dynamicky sestavena odkazem na výstupní hodnoty ostatních ovládacích prvků.

  Například volání ARM do `Microsoft.Network/expressRouteCircuits` poskytovatele prostředků:

  ```json
  "path": "<subid>/resourceGroup/<resourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<routecircuitName>/?api-version=2020-05-01"
  ```

- `request.body`Vlastnost je nepovinná. Použijte ho k zadání těla JSON, které se odešle s požadavkem. Tělo může být statickým obsahem nebo dynamicky sestavené odkazem na výstupní hodnoty z jiných ovládacích prvků.

## <a name="example"></a>Příklad

V následujícím příkladu `providersApi` element volá rozhraní API, aby získal pole objektů poskytovatele.

`allowedValues`Vlastnost `providersDropDown` elementu je nakonfigurována tak, aby získala názvy zprostředkovatelů. Zobrazí se v rozevíracím seznamu.

```json
{
    "name": "providersApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "GET",
        "path": "[concat(subscription().id, '/providers/Microsoft.Network/expressRouteServiceProviders?api-version=2019-02-01')]"
    }
},
{
    "name": "providerDropDown",
    "type": "Microsoft.Common.DropDown",
    "label": "Provider",
    "toolTip": "The provider that offers the express route connection.",
    "constraints": {
        "allowedValues": "[map(steps('settings').providersApi.value, (item) => parse(concat('{\"label\":\"', item.name, '\",\"value\":\"', item.name, '\"}')))]",
        "required": true
    },
    "visible": true
}
```

Příklad použití ArmApiControl ke kontrole dostupnosti názvu prostředku naleznete v tématu [Microsoft. Common. TextBox](microsoft-common-textbox.md).

## <a name="next-steps"></a>Další kroky

- Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
- Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
