---
title: Azure elementu UserNameTextBox uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Compute.UserNameTextBox uživatelského rozhraní pro portál Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 9f07c5bf9ba1f1880fa142beb52455522425e68d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063297"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Element Microsoft.Compute.UserNameTextBox uživatelského rozhraní
Ovládací prvek textové pole s integrované ověřování systému Windows a Linux uživatelská jména.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
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

## <a name="remarks"></a>Poznámky
- Pokud `constraints.required` je nastaven na **true**, pak textového pole musí mít hodnotu úspěšně ověřit. Výchozí hodnota je **true**.
- `osPlatform` musí být zadán, a může být buď **Windows** nebo **Linux**.
- `constraints.regex` je vzor regulárního výrazu jazyka JavaScript. -Li zadána, hodnota textového pole musí odpovídat vzorku úspěšně ověřit. Výchozí hodnota je **null**.
- `constraints.validationMessage` je řetězec k zobrazení při jeho hodnotu neprojde ověřením určeného `constraints.regex`. Pokud není zadaný, se používají zpráv integrované ověření textového pole. Výchozí hodnota je **null**.
- Tento element má integrované ověřování, který je založen na hodnotu zadanou pro `osPlatform`. Integrované ověřování můžete použít vlastní regulární výraz. Pokud nezadáte hodnotu `constraints.regex` je určeno, aktivaci předdefinované a vlastní ověření.

## <a name="sample-output"></a>Ukázkový výstup
```json
"Example name"
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
