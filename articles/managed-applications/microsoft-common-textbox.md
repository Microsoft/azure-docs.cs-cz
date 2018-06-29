---
title: Azure elementu TextBox uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Common.TextBox uživatelského rozhraní pro portál Azure.
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
ms.openlocfilehash: fa3e5fff8080acb9e824ffe27f6c149054804830
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063642"
---
# <a name="microsoftcommontextbox-ui-element"></a>Element Microsoft.Common.TextBox uživatelského rozhraní
Ovládací prvek, který lze upravit neformátovaný text.

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

## <a name="remarks"></a>Poznámky
- Pokud `constraints.required` je nastaven na **true**, pak textového pole musí mít hodnotu úspěšně ověřit. Výchozí hodnota je **false**.
- `constraints.regex` je vzor regulárního výrazu jazyka JavaScript. -Li zadána, hodnota textového pole musí odpovídat vzorku úspěšně ověřit. Výchozí hodnota je **null**.
- `constraints.validationMessage` je řetězec k zobrazení při ověřování se nezdaří, jeho hodnotu. Pokud není zadaný, se používají zpráv integrované ověření textového pole. Výchozí hodnota je **null**.
- Je možné zadat hodnotu pro `constraints.regex` při `constraints.required` je nastaven na **false**. V tomto scénáři není nutné u textového pole úspěšně ověřit hodnotu. Pokud je zadaná, musí se shodovat regulární výraz.

## <a name="sample-output"></a>Ukázkový výstup

```json
"my text value"
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
