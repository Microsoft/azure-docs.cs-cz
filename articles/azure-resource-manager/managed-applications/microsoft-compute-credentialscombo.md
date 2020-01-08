---
title: CredentialsCombo – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Compute. CredentialsCombo pro Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: baee83e08505ddabb4680b5bbb4101479010ea47
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652135"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft. Compute. CredentialsCombo – element uživatelského rozhraní

Skupina ovládacích prvků s integrovaným ověřováním pro hesla systémů Windows a Linux a veřejných klíčů SSH.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Pro Windows uživatelé uvidí:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Pro systém Linux s vybraným heslem uvidí uživatelé tyto informace:

![Heslo k Microsoft. Compute. CredentialsCombo Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Pro Linux s vybraným veřejným klíčem SSH uvidí uživatelé tyto informace:

![Microsoft. Compute. CredentialsCombo – klíč Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

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

Pro **Linux**použijte následující schéma:

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

Pokud je `osPlatform` **Windows**, nebo `osPlatform` se systémem **Linux** a uživatel zadal heslo místo veřejného klíče SSH, vrátí tento ovládací prvek následující výstup:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Pokud je `osPlatform` **Linux** a uživatel zadal veřejný klíč SSH, vrátí ovládací prvek následující výstup:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="remarks"></a>Poznámky

- je nutné zadat `osPlatform` a může být buď **Windows** , nebo **Linux**.
- Pokud je `constraints.required` nastavené na **hodnotu true**, musí být v textových polích heslo nebo veřejný klíč SSH hodnoty k úspěšnému ověření. Výchozí hodnota je **true (pravda**).
- Pokud je `options.hideConfirmation` nastaveno na **hodnotu true**, pak je skryté druhé textové pole pro potvrzení hesla uživatele. Výchozí hodnota je **false**.
- Pokud je `options.hidePassword` nastavené na **hodnotu true**, pak je možnost použít ověřování heslem skrytá. Dá se použít jenom v případě, že je `osPlatform` **Linux**. Výchozí hodnota je **false**.
- Další omezení povolených hesel lze implementovat pomocí vlastnosti `customPasswordRegex`. Řetězec v `customValidationMessage` se zobrazí, když se heslo nezdařilo vlastní ověření. Výchozí hodnota pro obě vlastnosti je **null**.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
