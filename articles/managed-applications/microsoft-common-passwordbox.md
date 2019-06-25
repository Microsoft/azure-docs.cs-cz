---
title: Azure prvek uživatelského rozhraní pole pro zadání hesla | Dokumentace Microsoftu
description: Popisuje element Microsoft.Common.PasswordBox uživatelského rozhraní pro Azure portal.
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
ms.openlocfilehash: 944f59da680c3a058a3cd245cca48d903e44ab87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64710941"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Prvek uživatelského rozhraní Microsoft.Common.PasswordBox
Ovládací prvek, který slouží k poskytování a potvrzení hesla.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
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
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Poznámky
- Tento prvek nepodporuje `defaultValue` vlastnost.
- Pro podrobnosti implementace `constraints`, naleznete v tématu [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Pokud `options.hideConfirmation` je nastavena na **true**, do druhého textového pole pro potvrzení hesla je skrytá. Výchozí hodnota je **false**.

## <a name="sample-output"></a>Ukázkový výstup
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).
* Popis společné vlastnosti v prvcích uživatelského rozhraní, naleznete v tématu [CreateUiDefinition prvky](create-uidefinition-elements.md).
