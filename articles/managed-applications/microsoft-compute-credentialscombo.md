---
title: Azure elementu CredentialsCombo uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Compute.CredentialsCombo uživatelského rozhraní pro portál Azure.
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
ms.openlocfilehash: 183075f7407b0a0ca6ea53871e239ab8c2d89490
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098616"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Element Microsoft.Compute.CredentialsCombo uživatelského rozhraní
Skupina ovládacích prvků pomocí integrované ověřování systému Windows a Linux hesla a veřejného klíče SSH.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Pro systém Windows najdete v části uživatelé:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Pro Linux s heslem, které jsou vybrané se uživatelům zobrazí:

![Microsoft.Compute.CredentialsCombo Linux heslo](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Pro Linux s veřejný klíč SSH vybraný najdete v části uživatelé:

![Klíč Microsoft.Compute.CredentialsCombo Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Schéma
Pro systém Windows použijte následující schéma:

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

Pro **Linux**, použít následující schéma:

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
- Pokud `constraints.required` je nastaven na **true**, pak heslo nebo veřejný klíč textová pole SSH musí mít hodnoty úspěšně ověřit. Výchozí hodnota je **true**.
- Pokud `options.hideConfirmation` je nastaven na **true**, druhé textové pole pro potvrzení hesla je skrytý. Výchozí hodnota je **false**.
- Pokud `options.hidePassword` je nastaven na **true**, možnost použít ověřování hesla je skrytý. Lze jej použít pouze tehdy, když `osPlatform` je **Linux**. Výchozí hodnota je **false**.
- Další omezení povolených hesel můžete implementovat pomocí `customPasswordRegex` vlastnost. Řetězec v `customValidationMessage` se zobrazí, pokud heslo vlastní ověřování se nezdaří. Výchozí hodnota pro obě vlastnosti je **null**.

## <a name="sample-output"></a>Ukázkový výstup
Pokud `osPlatform` je **Windows**, nebo `osPlatform` je **Linux** a uživatele zadali heslo místo veřejný klíč SSH, ovládacího prvku vrátí následující výstup:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

Pokud `osPlatform` je **Linux** a uživatele zadali veřejný klíč SSH, ovládacího prvku vrátí následující výstup:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
