---
title: Azure elementu UserNameTextBox uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Compute.UserNameTextBox uživatelského rozhraní pro portál Azure.
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
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: b65b62389fbb66c9461430d1dd2df42e71bf6cff
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260718"
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
- Pokud `constraints.required` je nastaven na **true**, pak textového pole musí obsahovat hodnotu úspěšně ověřit. Výchozí hodnota je **true**.
- `osPlatform` musí být zadán, a může být buď **Windows** nebo **Linux**.
- `constraints.regex` je vzor regulárního výrazu jazyka JavaScript. -Li zadána, hodnota textového pole musí odpovídat vzorku úspěšně ověřit. Výchozí hodnota je **null**.
- `constraints.validationMessage` je řetězec k zobrazení při jeho hodnotu neprojde ověřením určeného `constraints.regex`. Pokud není zadaný, se používají zpráv integrované ověření textového pole. Výchozí hodnota je **null**.
- Tento element má integrované ověřování, který je založen na hodnotu zadanou pro `osPlatform`. Integrované ověřování můžete použít vlastní regulární výraz.
Pokud nezadáte hodnotu `constraints.regex` je určeno, aktivaci předdefinované a vlastní ověření.

## <a name="sample-output"></a>Ukázkový výstup
```json
"tabrezm"
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
