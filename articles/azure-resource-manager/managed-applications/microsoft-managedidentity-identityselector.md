---
title: IdentitySelector – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. ManagedIdentity. IdentitySelector pro Azure Portal. Slouží k přiřazení spravovaných identit k prostředku.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 91e3cd0b99825fd72eb342ce7a8555b046455538
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063381"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft. ManagedIdentity. IdentitySelector – element uživatelského rozhraní

Ovládací prvek pro přiřazení [spravovaných identit](../../active-directory/managed-identities-azure-resources/overview.md) pro prostředek v nasazení.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Ovládací prvek se skládá z následujících prvků:

![První krok Microsoft. ManagedIdentity. IdentitySelector](./media/managed-application-elements/microsoft-managedidentity-identityselector-1.png)

Když uživatel vybere **Přidat**, otevře se následující formulář. Uživatel může pro prostředek vybrat jednu nebo více uživatelsky přiřazených identit.

![Druhý krok Microsoft. ManagedIdentity. IdentitySelector](./media/managed-application-elements/microsoft-managedidentity-identityselector-2.png)

Vybrané identity se zobrazí v tabulce. Uživatel může přidat nebo odstranit položky z této tabulky.

![Třetí krok Microsoft. ManagedIdentity. IdentitySelector](./media/managed-application-elements/microsoft-managedidentity-identityselector-3.png)

## <a name="schema"></a>Schéma

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Poznámky

- K nastavení počáteční hodnoty pro ovládací prvek možnosti identity přiřazené systémem použijte **defaultValue.systemAssignedIdentity** . Výchozí hodnota je **off**. Jsou povoleny následující hodnoty:
  - **Zapnuto** – k prostředku je přiřazená identita přiřazená systémem.
  - **Vypnuto** – identita přiřazená systému není přiřazená k prostředku.
  - **Pouze** – k prostředku je přiřazená identita přiřazená systémem. Uživatelé nemůžou tuto hodnotu během nasazování upravovat.
  - **OffOnly** – k prostředku není přiřazená identita přiřazená systémem. Uživatelé nemůžou tuto hodnotu během nasazování upravovat.

- Pokud je **parametr options. hideSystemAssignedIdentity** nastaven na **hodnotu true**, nezobrazí se uživatelské rozhraní pro konfiguraci identity přiřazené systémem. Výchozí hodnota této možnosti je false ( **NEPRAVDA**).
- Pokud je **parametr options. hideUserAssignedIdentity** nastaven na **hodnotu true**, nezobrazí se uživatelské rozhraní pro konfiguraci identity přiřazené uživatelem. Prostředku není přiřazená identita přiřazená uživatelem. Výchozí hodnota této možnosti je false ( **NEPRAVDA**).

## <a name="next-steps"></a>Další kroky

- Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
- Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).