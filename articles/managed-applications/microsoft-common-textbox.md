---
title: Azure prvek textové pole uživatelského rozhraní | Dokumentace Microsoftu
description: Popisuje element Microsoft.Common.TextBox uživatelského rozhraní pro Azure portal.
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
ms.openlocfilehash: b06e8b49efe8b6de720fa9bb819d4720e4f80fb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61044530"
---
# <a name="microsoftcommontextbox-ui-element"></a>Prvek uživatelského rozhraní Microsoft.Common.TextBox
Ovládací prvek, který slouží k úpravě neformátovaného textu.

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
- Pokud `constraints.required` je nastavena na **true**, potom do textového pole musí mít hodnotu úspěšně ověřit. Výchozí hodnota je **false**.
- `constraints.regex` je vzor regulárního výrazu jazyka JavaScript. Je-li zadána, textového pole Hodnota musí odpovídat vzoru úspěšně ověřit. Výchozí hodnota je **null**.
- `constraints.validationMessage` je řetězec k zobrazení při ověřování se nezdaří, hodnota v textovém poli. Pokud není zadán, se používají textového pole předdefinovaných ověřovacích zpráv. Výchozí hodnota je **null**.
- Je možné zadat hodnotu pro `constraints.regex` při `constraints.required` je nastavena na **false**. V tomto scénáři není vyžadována pro textové pole úspěšně ověřit hodnotu. Pokud je zadaná, musí odpovídat vzoru regulárního výrazu.

## <a name="sample-output"></a>Ukázkový výstup

```json
"my text value"
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).
* Popis společné vlastnosti v prvcích uživatelského rozhraní, naleznete v tématu [CreateUiDefinition prvky](create-uidefinition-elements.md).
