---
title: CredentialsCombo – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Compute. CredentialsCombo pro Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: 47c88e08e5d2eac09fbcd5b60a8ccd73b46c9616
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063799"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft. Compute. CredentialsCombo – element uživatelského rozhraní

Skupina ovládacích prvků s integrovaným ověřováním pro hesla systémů Windows a Linux a veřejných klíčů SSH.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Pro Windows uživatelé uvidí:

![Okna Microsoft. Compute. CredentialsCombo](./media/managed-application-elements/microsoft-compute-credentialscombo-windows.png)

Pro systém Linux s vybraným heslem uvidí uživatelé tyto informace:

![Heslo k Microsoft. Compute. CredentialsCombo Linux](./media/managed-application-elements/microsoft-compute-credentialscombo-linux-password.png)

Pro Linux s vybraným veřejným klíčem SSH uvidí uživatelé tyto informace:

![Microsoft. Compute. CredentialsCombo – klíč Linux](./media/managed-application-elements/microsoft-compute-credentialscombo-linux-key.png)

## <a name="schema"></a>Schéma

Pro Windows použijte následující schéma:

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
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

Pro **Linux** použijte následující schéma:

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
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="sample-output"></a>Ukázkový výstup

Pokud `osPlatform` je **systém Windows** nebo `osPlatform` se systémem **Linux** a uživatel zadal heslo místo veřejného klíče SSH, vrátí tento ovládací prvek následující výstup:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Pokud `osPlatform` je systém **Linux** a uživatel ZADAL veřejný klíč SSH, vrátí ovládací prvek následující výstup:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="remarks"></a>Poznámky

- `osPlatform` musí být zadáno a může být buď **Windows** , nebo **Linux**.
- Pokud `constraints.required` je nastavená na **hodnotu true**, musí být v textových polích heslo nebo veřejný klíč SSH hodnoty k úspěšnému ověření. Výchozí hodnota je **true (pravda**).
- Pokud `options.hideConfirmation` je nastaveno na **hodnotu true**, pak je skryté druhé textové pole pro potvrzení hesla uživatele. Výchozí hodnota je **false** (nepravda).
- Pokud `options.hidePassword` je parametr nastaven na **hodnotu true**, možnost použití ověřování heslem je skrytá. Dá se použít jenom v případě, že `osPlatform` je **Linux**. Výchozí hodnota je **false** (nepravda).
- Další omezení povolených hesel lze implementovat pomocí `customPasswordRegex` Vlastnosti. Řetězec v `customValidationMessage` se zobrazí, když se heslo nezdařilo vlastní ověření. Výchozí hodnota pro obě vlastnosti je **null**.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
