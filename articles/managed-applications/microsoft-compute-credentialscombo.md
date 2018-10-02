---
title: Azure prvek uživatelského rozhraní CredentialsCombo | Dokumentace Microsoftu
description: Popisuje element Microsoft.Compute.CredentialsCombo uživatelského rozhraní pro Azure portal.
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
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: 0412d55fe60524cde404e6a640723d3259e020e1
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586236"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Prvek uživatelského rozhraní Microsoft.Compute.CredentialsCombo
Skupina ovládacích prvků pomocí integrované ověřování Windows a Linuxem hesla a veřejného klíče SSH.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Pro Windows, které uživatelé uvidí:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Pro Linux s heslem, které jsou vybrané se uživatelům zobrazí:

![Microsoft.Compute.CredentialsCombo Linuxové heslo](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Pro Linux s veřejným klíčem SSH vybrali se uživatelům zobrazí:

![Klíč Microsoft.Compute.CredentialsCombo Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Schéma
Pokud používáte Windows, použijte následující schéma:

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
    "customValidationMessage": "The password must contain at least 12 characters, with at least 1 letter and 1 number."
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
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must contain at least 12 characters, with at least 1 letter and 1 number."
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
- Pokud `constraints.required` je nastavena na **true**, hesla nebo veřejného klíče textová pole SSH musí mít hodnoty úspěšně ověřit. Výchozí hodnota je **true**.
- Pokud `options.hideConfirmation` je nastavena na **true**, do druhého textového pole pro potvrzení hesla je skrytý. Výchozí hodnota je **false**.
- Pokud `options.hidePassword` je nastavena na **true**, možnost použít ověřování pomocí hesla je skrytý. Dá se použít jenom v případě `osPlatform` je **Linux**. Výchozí hodnota je **false**.
- Další omezení na povolené hesel můžete implementovat pomocí `customPasswordRegex` vlastnost. Řetězec v `customValidationMessage` se zobrazí, když selže vlastní ověřovací heslo. Výchozí hodnota pro obě vlastnosti je **null**.

## <a name="sample-output"></a>Ukázkový výstup
Pokud `osPlatform` je **Windows**, nebo `osPlatform` je **Linux** a uživatel zadal heslo místo veřejný klíč SSH, ovládací prvek vrátí následující výstup:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Pokud `osPlatform` je **Linux** a uživatel poskytl veřejný klíč SSH, ovládací prvek vrátí následující výstup:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).
* Popis společné vlastnosti v prvcích uživatelského rozhraní, naleznete v tématu [CreateUiDefinition prvky](create-uidefinition-elements.md).
