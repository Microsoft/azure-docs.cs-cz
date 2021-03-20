---
title: ArmApiControl – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Solutions. ArmApiControl pro Azure Portal. Používá se pro volání operací rozhraní API.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: bbe36e072d10b81c421331b2212d8b161afd2693
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87096937"
---
# <a name="microsoftcommonarmapicontrol-ui-element"></a>Microsoft. Common. ArmApiControl – element uživatelského rozhraní

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
- `request.path`Vlastnost určuje relativní cestu adresy URL. Může se jednat o statickou cestu nebo může být dynamicky sestavena odkazem na výstupní hodnoty ostatních ovládacích prvků.
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

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
