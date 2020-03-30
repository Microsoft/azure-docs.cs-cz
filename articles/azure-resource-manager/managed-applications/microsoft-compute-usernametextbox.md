---
title: UserNameTextBox Prvek uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft.Compute.UserNameTextBox pro portál Azure. Umožňuje uživatelům poskytovat uživatelská jména systému Windows nebo Linux.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c7544ae7d872a64547cb6c57ce8af9a09fc6c3d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651901"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Element uživatelského rozhraní Microsoft.Compute.UserNameTextBox

Ovládací prvek textového pole s integrovaným ověřením pro uživatelská jména Windows a Linux.

## <a name="ui-sample"></a>Ukázka ui

![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Schéma

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
"Example name"
```

## <a name="remarks"></a>Poznámky

- Pokud `constraints.required` je nastavena na **hodnotu true**, musí mít textové pole hodnotu, která má být úspěšně ověřena. Výchozí hodnota je **true**.
- `osPlatform`musí být zadána a může být buď **Windows** nebo **Linux**.
- `constraints.regex`je vzor regulárního výrazu JavaScriptu. Pokud je zadán, pak hodnota textového pole musí odpovídat vzorek úspěšně ověřit. Výchozí hodnota je **null**.
- `constraints.validationMessage`je řetězec, který se zobrazí, když hodnota textového pole selže při ověření zadaném . `constraints.regex` Pokud není zadán, pak se používají předdefinované ověřovací zprávy textového pole. Výchozí hodnota je **null**.
- Tento prvek má integrované ověření, které je `osPlatform`založeno na hodnotě zadané pro . Předdefinované ověření lze použít spolu s vlastním regulárním výrazem. Pokud je `constraints.regex` zadána hodnota pro, pak jsou spuštěny předdefinované i vlastní ověření.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).
