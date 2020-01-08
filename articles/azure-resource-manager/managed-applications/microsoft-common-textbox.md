---
title: TextBox – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. TextBox pro Azure Portal. Slouží k přidání neformátovaného textu.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652278"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft. Common. TextBox – element uživatelského rozhraní

Ovládací prvek, který lze použít k úpravám neformátovaného textu.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Schéma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
"my text value"
```

## <a name="remarks"></a>Poznámky

- Pokud je `constraints.required` nastaveno na **hodnotu true**, musí mít textové pole hodnotu k úspěšnému ověření. Výchozí hodnota je **false**.
- `constraints.regex` je vzor regulárního výrazu jazyka JavaScript. Je-li tento parametr zadán, musí být hodnota textového pole shodná se vzorem, aby bylo možné úspěšně ověřit. Výchozí hodnota je **null**.
- `constraints.validationMessage` je řetězec, který se zobrazí, když se hodnota textového pole nezdařila. Není-li parametr zadán, jsou použity vestavěné zprávy ověřování v textovém poli. Výchozí hodnota je **null**.
- Je možné zadat hodnotu pro `constraints.regex`, pokud je `constraints.required` nastavená na **hodnotu false**. V tomto scénáři není požadovaná hodnota pro úspěšné ověření tohoto textového pole. Pokud je zadaný, musí se shodovat se vzorem regulárního výrazu.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
