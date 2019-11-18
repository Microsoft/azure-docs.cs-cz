---
title: Element uživatelského rozhraní textového pole Azure | Microsoft Docs
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. TextBox pro Azure Portal. Slouží k přidání neformátovaného textu.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a5f28b763023bb60bca8f79bcaa9f2169281e466
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151108"
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
