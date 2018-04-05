---
title: Azure elementu CredentialsCombo uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Compute.CredentialsCombo uživatelského rozhraní pro portál Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: c17677a9e34de4c1afca8f04c8bd22c2155c3439
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI element
Skupina ovládacích prvků pomocí integrované ověřování systému Windows a Linux hesla a veřejného klíče SSH.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
![Microsoft.Compute.CredentialsCombo](./media/managed-application-elements/microsoft.compute.credentialscombo.png)

## <a name="schema"></a>Schéma
Pokud `osPlatform` je **Windows**, je použita na následující schéma:
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

Pokud `osPlatform` je **Linux**, je použita na následující schéma:
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>Poznámky
- `osPlatform` musí být zadán, a může být buď **Windows** nebo **Linux**.
- Pokud `constraints.required` je nastaven na **true**, pak heslo nebo SSH veřejného klíče textová pole musí obsahovat hodnoty úspěšně ověřit. Výchozí hodnota je **true**.
- Pokud `options.hideConfirmation` je nastaven na **true**, druhé textové pole pro potvrzení hesla je skrytý. Výchozí hodnota je **false**.
- Pokud `options.hidePassword` je nastaven na **true**, možnost použít ověřování hesla je skrytý. Lze jej použít pouze tehdy, když `osPlatform` je **Linux**. Výchozí hodnota je **false**.
- Další omezení povolených hesel můžete implementovat pomocí `customPasswordRegex` vlastnost. Řetězec v `customValidationMessage` se zobrazí, pokud heslo vlastní ověřování se nezdaří. Výchozí hodnota pro obě vlastnosti je **null**.

## <a name="sample-output"></a>Ukázkový výstup
Pokud `osPlatform` je **Windows**, nebo uživatele zadali heslo místo veřejný klíč SSH a pak se očekává následující výstup:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

Pokud uživatel zadaný veřejný klíč SSH, je očekáván následující výstup:
```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
