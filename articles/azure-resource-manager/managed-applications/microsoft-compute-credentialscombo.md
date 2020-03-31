---
title: PověřeníPrvek ui rozhraní
description: Popisuje element microsoft.compute.credentialscombo ui pro portál Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: baee83e08505ddabb4680b5bbb4101479010ea47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652135"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Element rozhraní Microsoft.Compute.CredentialsCombo

Skupina ovládacích prvků s integrovanou validací pro hesla Systému Windows a Linux a veřejné klíče SSH.

## <a name="ui-sample"></a>Ukázka ui

V systému Windows se uživatelé zobrazují na příkladu:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Pro Linux s vybraným heslem se uživatelé podívat na:

![Microsoft.Compute.CredentialsCombo Linux heslo](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Pro Linux s vybraným veřejným klíčem SSH se uživateli viz:

![Klíč Microsoft.Compute.CredentialsCombo Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Schéma

V systému Windows použijte následující schéma:

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

Pokud `osPlatform` **Windows**je Windows `osPlatform` , nebo je **Linux** a uživatel zapředpokladu heslo namísto ssh veřejný klíč, ovládací prvek vrátí následující výstup:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Pokud `osPlatform` je **Linux** a uživatel za předpokladu, SSH veřejný klíč, ovládací prvek vrátí následující výstup:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="remarks"></a>Poznámky

- `osPlatform`musí být zadána a může být buď **Windows** nebo **Linux**.
- Pokud `constraints.required` je nastavena na **hodnotu true**, musí mít textová pole s heslem nebo veřejným klíčem SSH hodnoty, které budou úspěšně ověřovat. Výchozí hodnota je **true**.
- Pokud `options.hideConfirmation` je nastavena na **hodnotu true**, pak druhé textové pole pro potvrzení hesla uživatele je skrytý. Výchozí hodnota je **false** (nepravda).
- Pokud `options.hidePassword` je nastavena na **hodnotu true**, možnost použít ověřování hesla je skryta. To může být `osPlatform` použit pouze tehdy, když je **Linux**. Výchozí hodnota je **false** (nepravda).
- Další omezení povolených hesel lze implementovat pomocí vlastnosti. `customPasswordRegex` Řetězec v `customValidationMessage` je zobrazen, když heslo selže vlastní ověření. Výchozí hodnota pro obě vlastnosti je **null**.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).
