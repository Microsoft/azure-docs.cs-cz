---
title: ServicePrincipalSelector – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. ServicePrincipalSelector pro Azure Portal. Poskytuje ovládací prvek pro výběr aplikace a textového pole pro zadání hesla nebo kryptografického otisku certifikátu.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 2fdbbad467d8c762db485fc7935e9cef78313fd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184446"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Microsoft. Common. ServicePrincipalSelector – element uživatelského rozhraní

Ovládací prvek, který umožňuje uživatelům vybrat existující [instanční objekt](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) nebo zaregistrovat novou aplikaci. Když vyberete **vytvořit nový**, postupujte podle kroků k registraci nové aplikace. Když vyberete existující aplikaci, ovládací prvek poskytne textové pole pro zadání hesla nebo kryptografického otisku certifikátu.

## <a name="ui-samples"></a>Ukázky uživatelského rozhraní

Můžete použít výchozí aplikaci, vytvořit novou aplikaci nebo použít existující aplikaci.

### <a name="use-default-application-or-create-new"></a>Použít výchozí aplikaci nebo vytvořit novou

Výchozí zobrazení závisí na hodnotách `defaultValue` vlastnosti a **typ instančního objektu** je nastaven na **vytvořit nový**. Pokud `principalId` vlastnost obsahuje platný globálně jedinečný identifikátor (GUID), ovládací prvek vyhledá aplikaci `objectId` . Výchozí hodnota se použije, pokud uživatel neprovede výběr z ovládacího prvku.

Pokud chcete zaregistrovat novou aplikaci, vyberte možnost **změnit výběr** a zobrazí se dialogové okno **zaregistrovat aplikaci** . Zadejte **název**, **podporovaný typ účtu** a klikněte na tlačítko **Registrovat** .

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="Úvodní zobrazení Microsoft. Common. ServicePrincipalSelector":::

Po registraci nové aplikace použijte **typ ověřování** a zadejte heslo nebo kryptografický otisk certifikátu.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="Ověřování Microsoft. Common. ServicePrincipalSelector.":::

### <a name="use-existing-application"></a>Použít existující aplikaci

Chcete-li použít existující aplikaci, zvolte **Vybrat existující** a pak vyberte **provést výběr**. Pomocí dialogového okna **Vybrat aplikaci** můžete vyhledat název aplikace. Z výsledků vyberte aplikaci a pak tlačítko **Vybrat** . Po výběru aplikace ovládací prvek zobrazí **typ ověřování** pro zadání hesla nebo kryptografického otisku certifikátu.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="Microsoft. Common. ServicePrincipalSelector vyberte existující aplikaci.":::

## <a name="schema"></a>Schéma

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Poznámky

- Požadované vlastnosti jsou následující:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Určuje výchozí `principalId` a `name` .

- Volitelné vlastnosti jsou následující:
  - `toolTip`: Připojí `infoBalloon` k jednotlivým jmenovkám popis.
  - `visible`: Skrytí nebo zobrazení ovládacího prvku.
  - `options`: Určuje, zda má být k dispozici možnost kryptografický otisk certifikátu.
  - `constraints`: Omezení regulárního výrazu pro ověřování hesla.

## <a name="example"></a>Příklad

Následuje příklad `Microsoft.Common.ServicePrincipalSelector` ovládacího prvku. `defaultValue`Vlastnost nastaví `principalId` `<default guid>` jako zástupný symbol pro výchozí identifikátor GUID identifikátoru aplikace.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Příklad výstupu

`appId`Je ID registrace aplikace, kterou jste vybrali nebo vytvořili. `objectId`Je pole ID objektů pro instanční objekty, které jsou nakonfigurovány pro vybranou registraci aplikace.

Pokud z ovládacího prvku není proveden žádný výběr, `newOrExisting` je hodnota vlastnosti **Nová**:

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

Když je vybrána možnost **vytvořit novou** nebo existující aplikaci z ovládacího prvku, `newOrExisting` je hodnota vlastnosti **existující**:

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Další kroky

- Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
- Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).