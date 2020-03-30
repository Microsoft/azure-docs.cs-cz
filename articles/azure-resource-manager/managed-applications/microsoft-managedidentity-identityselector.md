---
title: Prvek ui voliče identitySelector
description: Popisuje prvek ui Microsoft.ManagedIdentity.IdentitySelector pro portál Azure. Slouží k přiřazení spravovaných identit k prostředku.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087541"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Prvek ui rozhraní Microsoft.ManagedIdentity.IdentitySelector

Ovládací prvek pro přiřazení [spravovaných identit](../../active-directory/managed-identities-azure-resources/overview.md) pro prostředek v nasazení.

## <a name="ui-sample"></a>Ukázka ui

Ovládací prvek se skládá z následujících prvků:

![Microsoft.ManagedIdentity.IdentitySelector první krok](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

Když uživatel vybere **přidat**, otevře se následující formulář. Uživatel může vybrat jednu nebo více identit přiřazených uživatelem pro prostředek.

![Microsoft.ManagedIdentity.IdentitySelector druhý krok](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

Vybrané identity jsou zobrazeny v tabulce. Uživatel může přidávat nebo odstraňovat položky z této tabulky.

![Microsoft.ManagedIdentity.IdentitySelector třetí krok](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

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

- Pomocí **defaultValue.systemAssignedIdentity** nastavte počáteční hodnotu pro systémově přiřazený ovládací prvek možností identity. Výchozí hodnota je **Vypnuto**. Jsou povoleny následující hodnoty:
  - **Zapnuto** – prostředku je přiřazena identita přiřazená k systému.
  - **Vypnuto** – k prostředku není přiřazena identita přiřazená systému.
  - **OnOnly** – prostředku je přiřazena identita přiřazená k systému. Uživatelé nemohou tuto hodnotu během nasazení upravovat.
  - **OffOnly** – systém přiřazená identita není přiřazena k prostředku. Uživatelé nemohou tuto hodnotu během nasazení upravovat.

- Pokud **options.hideSystemAssignedIdentity** je nastavena na **hodnotu true**, rozhraní pro konfiguraci systému přiřazené identity se nezobrazí. Výchozí hodnota pro tuto možnost je **false**.
- Pokud **options.hideUserAssignedIdentity** je nastavena na **hodnotu true**, uživatelské rozhraní pro konfiguraci uživatele přiřazenou identitu se nezobrazí. Prostředku není přiřazena identita přiřazená uživateli. Výchozí hodnota pro tuto možnost je **false**.

## <a name="next-steps"></a>Další kroky

- Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
- Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).