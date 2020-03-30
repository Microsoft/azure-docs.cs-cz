---
title: Vytvoření nebo aktualizace vlastních rolí pro prostředky Azure pomocí rozhraní příkazového příkazu Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak vypsat, vytvořit, aktualizovat nebo odstranit vlastní role pomocí řízení přístupu na základě rolí (RBAC) pro prostředky Azure pomocí rozhraní příkazového příkazu Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 44676f7b92c2bcd30612295840054ab2f0c0cf12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062228"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-cli"></a>Vytvoření nebo aktualizace vlastních rolí pro prostředky Azure pomocí rozhraní příkazového příkazu Azure

> [!IMPORTANT]
> Přidání skupiny `AssignableScopes` pro správu do aplikace je aktuálně ve verzi Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud [předdefinované role pro prostředky Azure](built-in-roles.md) nesplňují specifické potřeby vaší organizace, můžete si vytvořit vlastní role. Tento článek popisuje, jak vypsat, vytvořit, aktualizovat nebo odstranit vlastní role pomocí rozhraní příkazového příkazového příkazu Azure.

Podrobný návod, jak vytvořit vlastní roli, najdete [v tématu Kurz: Vytvoření vlastní role pro prostředky Azure pomocí Azure CLI](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li vytvořit vlastní role, potřebujete:

- Oprávnění k vytváření vlastních rolí, například [Vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) nebo [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Výpis vlastních rolí

Chcete-li vypsat vlastní role, které jsou k dispozici pro přiřazení, použijte [seznam definic rolí az](/cli/azure/role/definition#az-role-definition-list). V následujících příkladech jsou uvedeny všechny vlastní role v aktuálním předplatném.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>Vypsat vlastní definici role

Chcete-li vypsat vlastní definici role, použijte [seznam definic role az](/cli/azure/role/definition#az-role-definition-list). Jedná se o stejný příkaz, který byste použili pro předdefinovanou roli.

```azurecli
az role definition list --name <role_name>
```

V následujícím příkladu je uvedena definice role *Operátor virtuálního počítače:*

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

V následujícím příkladu jsou uvedeny pouze akce role *Operátor virtuálního počítače:*

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Storage/*/read",
  "Microsoft.Network/*/read",
  "Microsoft.Compute/*/read",
  "Microsoft.Compute/virtualMachines/start/action",
  "Microsoft.Compute/virtualMachines/restart/action",
  "Microsoft.Authorization/*/read",
  "Microsoft.ResourceHealth/availabilityStatuses/read",
  "Microsoft.Resources/subscriptions/resourceGroups/read",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Insights/diagnosticSettings/*",
  "Microsoft.Support/*"
]
```

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Chcete-li vytvořit vlastní roli, použijte [vytvoření definice role az](/cli/azure/role/definition#az-role-definition-create). Definice role může být popis JSON nebo cesta k souboru obsahujícímu popis JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

Následující příklad vytvoří vlastní roli s názvem *Operátor virtuálního počítače*. Tato vlastní role přiřazuje přístup ke všem operacím čtení zprostředkovatelů prostředků *Microsoft.Compute*, *Microsoft.Storage*a *Microsoft.Network* a přiřazuje přístup ke spuštění, restartování a monitorování virtuálních počítačů. Tuto vlastní roli lze použít ve dvou předplatných. Tento příklad používá soubor JSON jako vstup.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Aktualizace vlastní role

Chcete-li aktualizovat vlastní roli, nejprve použijte [seznam definic role az](/cli/azure/role/definition#az-role-definition-list) k načtení definice role. Za druhé proveďte požadované změny definice role. Nakonec použijte [aktualizaci definice role az](/cli/azure/role/definition#az-role-definition-update) k uložení aktualizované definice role.

```azurecli
az role definition update --role-definition <role_definition>
```

Následující příklad přidá *Microsoft.Insights/diagnosticSettings/* `Actions` operace a přidá `AssignableScopes` skupinu pro správu pro vlastní roli *operátorvirtuálního počítače.* Přidání skupiny `AssignableScopes` pro správu do aplikace je aktuálně ve verzi Preview.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Odstranění vlastní role

Chcete-li odstranit vlastní roli, použijte [odstranění definice role az](/cli/azure/role/definition#az-role-definition-delete). Chcete-li určit roli, kterou chcete odstranit, použijte název role nebo ID role. Chcete-li určit ID role, použijte [seznam definic role az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

Následující příklad odstraní vlastní roli *operátora virtuálního počítače.*

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření vlastní role pro prostředky Azure pomocí azure cli](tutorial-custom-role-cli.md)
- [Vlastní role pro prostředky Azure](custom-roles.md)
- [Operace zprostředkovatele prostředků Azure Resource Manager](resource-provider-operations.md)