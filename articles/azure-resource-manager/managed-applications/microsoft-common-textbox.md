---
title: TextBox – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. TextBox pro Azure Portal. Slouží k přidání neformátovaného textu.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8e4cfcc7fe46c19bf1e58ee5eadf1e0bb8c7bd8e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124325"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft. Common. TextBox – element uživatelského rozhraní

Prvek uživatelského rozhraní (UI), který lze použít k přidání neformátovaného textu. `Microsoft.Common.TextBox`Element je jediné řádkové vstupní pole, ale podporuje víceřádkové vstupy s `multiLine` vlastností.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

`TextBox`Prvek používá jednořádkové nebo víceřádkové textové pole.

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox.png" alt-text="Textové pole s jedním řádkem prvku Microsoft. Common. TextBox.":::

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox-multi-line.png" alt-text="Do víceřádkového textového pole prvku Microsoft. Common. TextBox.":::

## <a name="schema"></a>Schéma

```json
{
  "name": "nameInstance",
  "type": "Microsoft.Common.TextBox",
  "label": "Name",
  "defaultValue": "contoso123",
  "toolTip": "Use only allowed characters",
  "placeholder": "",
  "multiLine": false,
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

- Použijte `toolTip` vlastnost k zobrazení textu prvku, když je ukazatel myši umístěn nad symbolem informací.
- `placeholder`Vlastnost je text v nápovědě, který zmizí, když uživatel začne upravovat. Pokud `placeholder` jsou a `defaultValue` definovány obě, má `defaultValue` přednost a je zobrazena.
- `multiLine`Vlastnost je logická hodnota `true` nebo `false` . Chcete-li použít víceřádkové textové pole, nastavte vlastnost na hodnotu `true` . Pokud není víceřádkové textové pole potřeba, nastavte vlastnost na `false` nebo vylučte vlastnost. Pro nové řádky zobrazuje výstup JSON `\n` pro čárový kanál. Víceřádkové textové pole přijímá znak návratu na začátek řádku `\r` (CR) a `\n` pro posun řádku (LF). Například výchozí hodnota může zahrnovat `\r\n` zadání CRLF.
- Pokud `constraints.required` je nastaveno na `true` , pak musí mít textové pole hodnotu k úspěšnému ověření. Výchozí hodnota je `false`.
- `validations`Vlastnost je pole, kde přidáte podmínky pro kontrolu hodnoty zadané v textovém poli.
- `regex`Vlastnost je vzor regulárního výrazu jazyka JavaScript. Je-li tento parametr zadán, musí být hodnota textového pole shodná se vzorem, aby bylo možné úspěšně ověřit. Výchozí hodnota je `null`. Další informace o syntaxi regulárního výrazu naleznete v tématu [rychlý přehled regulárních výrazů](/dotnet/standard/base-types/regular-expression-language-quick-reference).
- `isValid`Vlastnost obsahuje výraz, který je vyhodnocen jako `true` nebo `false` . V rámci výrazu Definujte podmínku, která určuje, zda je textové pole platné.
- `message`Vlastnost je řetězec, který se zobrazí, když se hodnota textového pole nezdařila.
- Je možné zadat hodnotu, `regex` Pokud `required` je nastavena na `false` . V tomto scénáři není požadovaná hodnota pro úspěšné ověření tohoto textového pole. Pokud je zadaný, musí se shodovat se vzorem regulárního výrazu.

## <a name="examples"></a>Příklady

Příklady ukazují, jak použít jednořádkové textové pole a víceřádkové textové pole.

### <a name="single-line"></a>Jednořádkový

Následující příklad používá textové pole s ovládacím prvkem [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md) ke kontrole dostupnosti názvu prostředku.

```json
"basics": [
  {
    "name": "nameApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
      "method": "POST",
      "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
      "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
    }
  },
  {
    "name": "txtStorageName",
    "type": "Microsoft.Common.TextBox",
    "label": "Storage account name",
    "constraints": {
      "validations": [
        {
          "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
          "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
        }
      ]
    }
  }
]
```

### <a name="multi-line"></a>Více řádků

Tento příklad používá `multiLine` vlastnost k vytvoření víceřádkového textového pole se zástupným textem.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "demoTextBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Multi-line text box",
        "defaultValue": "",
        "toolTip": "Use 1-60 alphanumeric characters, hyphens, spaces, periods, and colons.",
        "placeholder": "This is a multi-line text box:\nLine 1.\nLine 2.\nLine 3.",
        "multiLine": true,
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z -.:\n]{1,60}$",
              "message": "Only 1-60 alphanumeric characters, hyphens, spaces, periods, and colons are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "textBox": "[basics('demoTextBox')]"
    }
  }
}
```

## <a name="next-steps"></a>Další kroky

- Úvod k vytváření definic uživatelského rozhraní najdete v tématu [CreateUiDefinition.jspro prostředí pro vytváření spravované aplikace Azure](create-uidefinition-overview.md).
- Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
