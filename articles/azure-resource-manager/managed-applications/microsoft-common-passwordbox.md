---
title: Element ui PasswordBox
description: Popisuje prvek microsoft.common.passwordbox ui pro portál Azure. Umožňuje uživatelům poskytovat tajnou hodnotu při nasazování spravovaných aplikací.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5f7d3a47482ccec9778b102d3509b5f8ef343185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652304"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Element ui rozhraní Microsoft.Common.PasswordBox

Ovládací prvek, který lze použít k zadání a potvrzení hesla.

## <a name="ui-sample"></a>Ukázka ui

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

- Tento prvek nepodporuje `defaultValue` vlastnost.
- Podrobnosti o `constraints`implementaci naleznete v tématu [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Pokud `options.hideConfirmation` je nastavena hodnota **true**, druhé textové pole pro potvrzení hesla uživatele je skryté. Výchozí hodnota je **false** (nepravda).

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).
