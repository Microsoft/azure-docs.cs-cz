---
title: Azure prvek uživatelského rozhraní UserNameTextBox | Dokumentace Microsoftu
description: Popisuje element Microsoft.Compute.UserNameTextBox uživatelského rozhraní pro Azure portal.
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
ms.openlocfilehash: 88ab13329a719ba1e1b8a7b5fba2f7a2d381eca2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64700847"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Prvek uživatelského rozhraní Microsoft.Compute.UserNameTextBox
Ovládací prvek textové pole s integrované ověřování Windows a Linuxem uživatelská jména.

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
- Pokud `constraints.required` je nastavena na **true**, potom do textového pole musí mít hodnotu úspěšně ověřit. Výchozí hodnota je **true**.
- `osPlatform` musí být zadán, a může být buď **Windows** nebo **Linux**.
- `constraints.regex` je vzor regulárního výrazu jazyka JavaScript. Je-li zadána, textového pole Hodnota musí odpovídat vzoru úspěšně ověřit. Výchozí hodnota je **null**.
- `constraints.validationMessage` řetězec má zobrazit, když hodnota v textovém poli neprojde ověřením určené `constraints.regex`. Pokud není zadán, se používají textového pole předdefinovaných ověřovacích zpráv. Výchozí hodnota je **null**.
- Tento element má integrované ověřování, který je založen na hodnotu zadanou pro `osPlatform`. Integrované ověřování lze použít spolu s vlastní regulární výraz. Pokud hodnotu `constraints.regex` není zadán, pak se aktivují předdefinované a vlastní ověření.

## <a name="sample-output"></a>Ukázkový výstup
```json
"Example name"
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).
* Popis společné vlastnosti v prvcích uživatelského rozhraní, naleznete v tématu [CreateUiDefinition prvky](create-uidefinition-elements.md).
