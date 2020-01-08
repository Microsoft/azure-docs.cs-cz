---
title: PasswordBox – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. PasswordBox pro Azure Portal. Umožňuje uživatelům při nasazování spravovaných aplikací zadat tajnou hodnotu.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5f7d3a47482ccec9778b102d3509b5f8ef343185
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652304"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft. Common. PasswordBox – element uživatelského rozhraní

Ovládací prvek, který lze použít k zadání a potvrzení hesla.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Schéma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
"p4ssw0rd"
```

## <a name="remarks"></a>Poznámky

- Tento prvek nepodporuje vlastnost `defaultValue`.
- Podrobnosti o implementaci `constraints`naleznete v tématu [Microsoft. Common. TextBox](microsoft-common-textbox.md).
- Pokud je `options.hideConfirmation` nastaveno na **hodnotu true**, druhé textové pole pro potvrzení hesla uživatele je skryté. Výchozí hodnota je **false**.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
