---
title: Vytvoření vlastních rolí pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Naučte se vytvářet vlastní role pro řízení přístupu na základě role (RBAC) pomocí rozhraní příkazového řádku Azure. To zahrnuje seznam, vytvářet, aktualizovat a odstraňovat vlastní role.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b1df50c73497e3f5ad64a7ba7210079871b155e0
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321154"
---
# <a name="create-custom-roles-using-azure-cli"></a>Vytvoření vlastních rolí pomocí rozhraní příkazového řádku Azure

Pokud [předdefinované role](built-in-roles.md) nemáte splnili specifické požadavky vaší organizace, můžete vytvořit vlastní role. Tento článek popisuje, jak vytvářet a spravovat vlastní role pomocí rozhraní příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky

Chcete-li vytvořit vlastní role, je třeba:

- Oprávnění k vytvoření vlastních rolí, například [vlastníka](built-in-roles.md#owner) nebo [správce přístupu uživatelů](built-in-roles.md#user-access-administrator)
- [Rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nainstalovány místně

## <a name="list-custom-roles"></a>Vlastní role seznamu

K zobrazení seznamu vlastní role, které jsou k dispozici pro přiřazení, použijte [seznamu definice role az](/cli/azure/role/definition#az-role-definition-list). Následující příklady zobrazí seznam všech vlastních rolí v aktuálním předplatném.

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

## <a name="create-a-custom-role"></a>Vytvořit vlastní roli

Chcete-li vytvořit vlastní roli, použijte [vytvoření definice role az](/cli/azure/role/definition#az-role-definition-create). Definice role může být popis JSON nebo cestu k souboru, který obsahuje popis JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

Následující příklad vytvoří vlastní roli s názvem *virtuální počítač operátor*. Tuto vlastní roli přiřadí přístup ke všem operacím čtení z *Microsoft.Compute*, *Microsoft.Storage*, a *Microsoft.Network* přístup k prostředkům zprostředkovatelé a přiřadí Pokud chcete spustit, restartujte a monitorování virtuálních počítačů. Tuto vlastní roli můžete použít ve dvou předplatných. Tento příklad používá soubor JSON jako vstup.

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

## <a name="update-a-custom-role"></a>Aktualizovat vlastní role

Chcete-li aktualizovat vlastní role, nejprve použijte [seznamu definice role az](/cli/azure/role/definition#az-role-definition-list) načíst definici role. Za druhé proveďte požadované změny k definici role. Nakonec použijte [aktualizaci definice role az](/cli/azure/role/definition#az-role-definition-update) se uložit definici aktualizované role.

```azurecli
az role definition update --role-definition <role_definition>
```

Následující příklad přidá *Microsoft.Insights/diagnosticSettings/* operace *akce* z *virtuální počítač operátor* vlastní role.

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
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
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
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Odstranit vlastní roli

Chcete-li odstranit vlastní roli, použijte [odstranit definici role az](/cli/azure/role/definition#az-role-definition-delete). Chcete-li zadat role, kterou chcete odstranit, použijte název role nebo ID role. Chcete-li zjistit role ID, použijte [seznamu definice role az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

Následující příklad odstraní *virtuální počítač operátor* vlastní role.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Další postup

- [Kurz: Vytvoření vlastní role pomocí rozhraní příkazového řádku Azure](tutorial-custom-role-cli.md)
- [Vlastní role v Azure](custom-roles.md)
- [Operace zprostředkovatele prostředků v Azure Resource Manager](resource-provider-operations.md)