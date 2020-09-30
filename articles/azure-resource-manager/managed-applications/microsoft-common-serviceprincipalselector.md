---
title: ServicePrincipalSelector – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. ServicePrincipalSelector pro Azure Portal. Poskytuje rozevírací seznam pro výběr identifikátoru aplikace a textového pole pro zadání hesla nebo kryptografického otisku certifikátu.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: tomfitz
ms.openlocfilehash: 73b242754bfae53b6df5abd9c2c8dee33b973dad
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575992"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Microsoft. Common. ServicePrincipalSelector – element uživatelského rozhraní

Ovládací prvek, který umožňuje uživatelům vybrat existující instanční objekt nebo zaregistrovat nové. Když vyberete **vytvořit nový**, provedete postup registrace nové aplikace. Když vyberete existující aplikaci, ovládací prvek poskytne textové pole pro zadání hesla nebo kryptografického otisku certifikátu.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Výchozí zobrazení je určeno hodnotami ve `defaultValue` Vlastnosti. Pokud `principalId` vlastnost obsahuje platný globálně jedinečný identifikátor (GUID), ovládací prvek vyhledá ID objektu aplikace. Výchozí hodnota platí v případě, že uživatel neprovede výběr z rozevíracího seznamu.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-initial.png" alt-text="Úvodní zobrazení Microsoft. Common. ServicePrincipalSelector":::

Když vyberete možnost **vytvořit nový** nebo existující identifikátor aplikace z rozevíracího seznamu, zobrazí se **typ ověřování** , který v textovém poli zadá heslo nebo kryptografický otisk certifikátu.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-selection.png" alt-text="Úvodní zobrazení Microsoft. Common. ServicePrincipalSelector":::

## <a name="schema"></a>Schéma

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "principalId": "App Id",
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

- Požadované vlastnosti jsou:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Určuje výchozí `principalId` a `name` .

- Volitelné vlastnosti jsou:
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
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "principalId": "App Id",
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

`appId`Je ID registrace aplikace, kterou jste vybrali nebo vytvořili. `objectId`Je pole objectID pro instanční objekty, které jsou nakonfigurovány pro vybranou registraci aplikace.

Pokud není proveden žádný výběr z rozevíracího seznamu, `newOrExisting` je hodnota vlastnosti **Nová**:

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

Když je v rozevíracím seznamu vybraná možnost **vytvořit nový** nebo existující identifikátor aplikace, `newOrExisting` je hodnota vlastnosti **existující**:

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
