---
title: Vytvoření nebo aktualizace vlastních rolí Azure pomocí Azure CLI – Azure RBAC
description: Naučte se, jak pomocí rozhraní příkazového řádku Azure a řízení přístupu na základě role Azure (Azure RBAC) zobrazit seznam, vytvořit, aktualizovat nebo odstranit vlastní role Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 31dabcf77f0db76047919fa76d00f1c5ed3c96d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369136"
---
# <a name="create-or-update-azure-custom-roles-using-azure-cli"></a>Vytvoření nebo aktualizace vlastních rolí Azure pomocí Azure CLI

> [!IMPORTANT]
> Přidání skupiny pro správu do `AssignableScopes` je aktuálně ve verzi Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud [předdefinované role Azure](built-in-roles.md) nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní role. Tento článek popisuje, jak pomocí Azure CLI vypsat, vytvořit, aktualizovat nebo odstranit vlastní role.

Podrobný návod, jak vytvořit vlastní roli, najdete v tématu [kurz: Vytvoření vlastní role Azure pomocí Azure CLI](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Požadavky

K vytvoření vlastních rolí budete potřebovat:

- Oprávnění k vytváření vlastních rolí, například [Vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) nebo [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Výpis vlastních rolí

K vypsání vlastních rolí, které jsou k dispozici pro přiřazení, použijte příkaz [AZ role definition list](/cli/azure/role/definition#az-role-definition-list). Následující příklad vypíše všechny vlastní role v aktuálním předplatném.

```azurecli
az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
```

```json
[
  {
    "roleName": "My Management Contributor",
    "type": "CustomRole"
  },
  {
    "roleName": "My Service Reader Role",
    "type": "CustomRole"
  },
  {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole"
  }
]
```

## <a name="list-a-custom-role-definition"></a>Seznam definice vlastní role

Pokud chcete zobrazit seznam definice vlastní role, použijte příkaz [AZ role definition list](/cli/azure/role/definition#az-role-definition-list). Jedná se o stejný příkaz, který byste použili pro předdefinovanou roli.

```azurecli
az role definition list --name {roleName}
```

V následujícím příkladu je uveden seznam definice role *operátora virtuálního počítače* :

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
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

Následující příklad vypíše pouze akce role *operátora virtuálního počítače* :

```azurecli
az role definition list --name "Virtual Machine Operator" --output json --query '[].permissions[0].actions'
```

```json
[
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
]
```

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Pokud chcete vytvořit vlastní roli, použijte příkaz [AZ role definice Create](/cli/azure/role/definition#az-role-definition-create). Definice role může být popis JSON nebo cesta k souboru obsahujícímu popis JSON.

```azurecli
az role definition create --role-definition {roleDefinition}
```

Následující příklad vytvoří vlastní roli s názvem *operátor virtuálního počítače*. Tato vlastní role přiřadí přístup ke všem operacím čtení pro poskytovatele prostředků *Microsoft. COMPUTE*, *Microsoft. Storage* a *Microsoft. Network* a přiřadí přístup ke spouštění, restartování a monitorování virtuálních počítačů. Tato vlastní role se dá použít ve dvou předplatných. V tomto příkladu se jako vstup používá soubor JSON.

vmoperator.jsna

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
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Aktualizace vlastní role

Pokud chcete aktualizovat vlastní roli, napřed načtěte definici role pomocí [seznamu AZ role definition list](/cli/azure/role/definition#az-role-definition-list) . Za druhé proveďte požadované změny definice role. Nakonec pomocí příkazu [AZ role definition Update](/cli/azure/role/definition#az-role-definition-update) uložte aktualizovanou definici role.

```azurecli
az role definition update --role-definition {roleDefinition}
```

Následující příklad přidá operaci *Microsoft. Insights/diagnosticSettings/* Operation do `Actions` a přidá skupinu pro správu do `AssignableScopes` vlastní role *operátoru virtuálního počítače* . Přidání skupiny pro správu do `AssignableScopes` je aktuálně ve verzi Preview.

vmoperator.jsna

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
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Odstranění vlastní role

Pokud chcete odstranit vlastní roli, použijte příkaz [AZ role definition Delete](/cli/azure/role/definition#az-role-definition-delete). Chcete-li určit roli, kterou chcete odstranit, použijte název role nebo ID role. Chcete-li zjistit ID role, použijte příkaz [AZ role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name {roleNameOrId}
```

Následující příklad odstraní vlastní roli *operátoru virtuálního počítače* .

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření vlastní role Azure pomocí Azure CLI](tutorial-custom-role-cli.md)
- [Vlastní role Azure](custom-roles.md)
- [Operace poskytovatele prostředků Azure](resource-provider-operations.md)