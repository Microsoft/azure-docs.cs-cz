---
title: TextBox – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. TextBox pro Azure Portal. Slouží k přidání neformátovaného textu.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c89bc434d9d67144a95b5c2f23e7664078fe7825
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87474304"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft. Common. TextBox – element uživatelského rozhraní

Ovládací prvek, který lze použít k úpravám neformátovaného textu.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textbox.png)

## <a name="schema"></a>Schéma

```json
{
    "name": "nameInstance",
    "type": "Microsoft.Common.TextBox",
    "label": "Name",
    "defaultValue": "contoso123",
    "toolTip": "Use only allowed characters",
    "placeholder": "",
    "constraints": {
        "required": true,
        "validations": [
            {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
            },
            {
                "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
                "message": "Must start with 'contoso'."
            }
        ]
    },
    "visible": true
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
"contoso123"
```

## <a name="remarks"></a>Poznámky

- Pokud `constraints.required` je vlastnost nastavena na **hodnotu true**, musí mít textové pole hodnotu k úspěšnému ověření. Výchozí hodnota je **false** (nepravda).
- `validations`Vlastnost je pole, kde přidáte podmínky pro kontrolu hodnoty zadané v textovém poli.
- `regex`Vlastnost je vzor regulárního výrazu jazyka JavaScript. Je-li tento parametr zadán, musí být hodnota textového pole shodná se vzorem, aby bylo možné úspěšně ověřit. Výchozí hodnota je **null**.
- `isValid`Vlastnost obsahuje výraz, který je vyhodnocen na hodnotu true nebo false. V rámci výrazu Definujte podmínku, která určuje, zda je textové pole platné.
- `message`Vlastnost je řetězec, který se zobrazí, když se hodnota textového pole nezdařila.
- Je možné zadat hodnotu, která `regex` `required` je nastavena na **hodnotu false**. V tomto scénáři není požadovaná hodnota pro úspěšné ověření tohoto textového pole. Pokud je zadaný, musí se shodovat se vzorem regulárního výrazu.
- `placeholder`Vlastnost je text v nápovědě, který zmizí, když uživatel začne upravovat. Pokud `placeholder` jsou a `defaultValue` definovány obě, má `defaultValue` přednost a je zobrazena.

## <a name="example"></a>Příklad

Následující příklad používá textové pole s ovládacím prvkem [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md) ke kontrole dostupnosti názvu prostředku.

```json
"basics": [
    {
        "name": "nameApi",
        "type": "Microsoft.Solutions.ArmApiControl",
        "request": {
            "method": "POST",
            "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
            "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
        }
    },
    {
        "name": "txtStorageName",
        "type": "Microsoft.Common.TextBox",
        "label": "Storage account name",
        "constraints": {
            "validations": [
                {
                    "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
                    "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
                }
            ]
        }
    }
]
```

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
