---
title: Element ui textového pole
description: Popisuje prvek microsoft.common.textbox ui pro portál Azure. Používá se pro přidání neformátovaného textu.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652278"
---
# <a name="microsoftcommontextbox-ui-element"></a>Element ui rozhraní Microsoft.Common.TextBox

Ovládací prvek, který lze použít k úpravě neformátovaného textu.

## <a name="ui-sample"></a>Ukázka ui

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

- Pokud `constraints.required` je nastavena na **hodnotu true**, musí mít textové pole hodnotu, která má být úspěšně ověřena. Výchozí hodnota je **false** (nepravda).
- `constraints.regex`je vzor regulárního výrazu JavaScriptu. Pokud je zadán, pak hodnota textového pole musí odpovídat vzorek úspěšně ověřit. Výchozí hodnota je **null**.
- `constraints.validationMessage`je řetězec, který se zobrazí, když hodnota textového pole selže ověření. Pokud není zadán, pak se používají předdefinované ověřovací zprávy textového pole. Výchozí hodnota je **null**.
- Je možné zadat hodnotu, `constraints.regex` `constraints.required` kdy je nastavena na **false**. V tomto scénáři není vyžadována hodnota pro textové pole úspěšně ověřit. Pokud je zadán, musí odpovídat vzoru regulárního výrazu.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).
