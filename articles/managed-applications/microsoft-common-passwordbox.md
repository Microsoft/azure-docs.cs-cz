---
title: Azure elementu Položka PasswordBox uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Common.PasswordBox uživatelského rozhraní pro portál Azure.
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
ms.openlocfilehash: adaece4a450f758c22ffecb356826962806d9d9b
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060099"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Element Microsoft.Common.PasswordBox uživatelského rozhraní
Ovládací prvek, který slouží k zadání a potvrzení hesla.

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
- Tento element nepodporuje `defaultValue` vlastnost.
- Podrobnosti implementace `constraints`, najdete v části [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Pokud `options.hideConfirmation` je nastaven na **true**, druhé textové pole pro potvrzení hesla je skrytá. Výchozí hodnota je **false**.

## <a name="sample-output"></a>Ukázkový výstup
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
