---
title: UserNameTextBox – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Compute. UserNameTextBox pro Azure Portal. Umožňuje uživatelům poskytovat uživatelská jména pro Windows nebo Linux.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 82478f322e1df22bde50769b90f0424140920e9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063570"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft. Compute. UserNameTextBox – element uživatelského rozhraní

Ovládací prvek textové pole s integrovaným ověřováním pro uživatelská jména pro systémy Windows a Linux.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft-compute-usernametextbox.png)

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

- Pokud `constraints.required` je vlastnost nastavena na **hodnotu true**, musí mít textové pole hodnotu k úspěšnému ověření. Výchozí hodnota je **true (pravda**).
- `osPlatform` musí být zadáno a může být buď **Windows** , nebo **Linux**.
- `constraints.regex` je vzor regulárního výrazu jazyka JavaScript. Je-li tento parametr zadán, musí být hodnota textového pole shodná se vzorem, aby bylo možné úspěšně ověřit. Výchozí hodnota je **null**.
- `constraints.validationMessage` je řetězec, který se zobrazí, pokud hodnota textového pole nefunguje při ověřování určeného parametrem `constraints.regex` . Není-li parametr zadán, jsou použity vestavěné zprávy ověřování v textovém poli. Výchozí hodnota je **null**.
- Tento prvek obsahuje integrované ověřování, které je založeno na hodnotě zadané pro `osPlatform` . Integrované ověřování lze použít společně s vlastním regulárním výrazem. Pokud `constraints.regex` je zadaná hodnota, pak se aktivují jak předdefinované, tak i vlastní ověřování.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
