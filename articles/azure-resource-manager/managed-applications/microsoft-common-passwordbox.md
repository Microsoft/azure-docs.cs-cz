---
title: PasswordBox – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. PasswordBox pro Azure Portal. Umožňuje uživatelům při nasazování spravovaných aplikací zadat tajnou hodnotu.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 7902ea2e30dec20e57d88344dc9507aec3993600
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87064075"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft. Common. PasswordBox – element uživatelského rozhraní

Ovládací prvek, který lze použít k zadání a potvrzení hesla.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft-common-passwordbox.png)

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

- Tento prvek nepodporuje `defaultValue` vlastnost.
- Podrobnosti o implementaci nástroje naleznete `constraints` v tématu [Microsoft. Common. TextBox](microsoft-common-textbox.md).
- Pokud `options.hideConfirmation` je nastavená **hodnota true**, druhé textové pole pro potvrzení hesla uživatele je skryté. Výchozí hodnota je **false** (nepravda).

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
