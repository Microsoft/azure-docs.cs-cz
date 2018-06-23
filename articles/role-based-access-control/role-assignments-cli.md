---
title: Správa přístupu pomocí RBAC a rozhraní příkazového řádku Azure | Microsoft Docs
description: Zjistěte, jak chcete spravovat přístup pro uživatele, skupiny a aplikace, pomocí řízení přístupu na základě role (RBAC) a rozhraní příkazového řádku Azure. To zahrnuje jak přístup, udělení přístupu a odebrání přístupu.
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
ms.openlocfilehash: 16577339f1aa33fbd1a8b90f4beaef1ee4ce806c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316392"
---
# <a name="manage-access-using-rbac-and-azure-cli"></a>Správa přístupu pomocí RBAC a rozhraní příkazového řádku Azure

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, která můžete spravovat přístup k prostředkům v Azure. Tento článek popisuje, jak spravovat přístup pro uživatele, skupiny a aplikace s použitím RBAC a rozhraní příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky

Pokud chcete spravovat přístup, jedním z těchto potřebujete:

* [Bash v prostředí cloudu Azure](/azure/cloud-shell/overview)
* [Azure CLI](/cli/azure)

## <a name="list-roles"></a>Seznam rolí

K zobrazení seznamu všech definic rolí k dispozici, použijte [seznamu definice role az](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

Následující příklad uvádí název a popis všech definic rolí k dispozici:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

Následující příklad vypíše všechny definice předdefinovaná role:

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

### <a name="list-actions-of-a-role"></a>Seznam akcí role

K zobrazení seznamu akce definici role, použijte [seznamu definice role az](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

Následující příklad zobrazí *Přispěvatel* definice role:

```azurecli
az role definition list --name "Contributor"
```

```Output
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Následující příklad zobrazí *akce* a *notActions* z *Přispěvatel* role:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

Následující příklad uvádí akce *Přispěvatel virtuálních počítačů* role:

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>Přístup k seznamu

V RBAC pro přístup k seznamu, můžete seznam přiřazení rolí.

### <a name="list-role-assignments-for-a-user"></a>Seznam přiřazení role pro uživatele

K zobrazení seznamu přiřazení rolí pro konkrétního uživatele, použijte [seznamu přiřazení role az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Ve výchozím nastavení se zobrazí pouze přiřazení obor k předplatnému. Chcete-li zobrazit přiřazení vymezeny prostředek nebo skupina, použijte `--all`.

Následující příklad vypíše přiřazení rolí, které jsou přiřazeny přímo na *patlong@contoso.com* uživatele:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Seznam přiřazení rolí pro skupinu prostředků.

K zobrazení seznamu přiřazení rolí, které pro skupinu prostředků, použijte [seznamu přiřazení role az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

Následující příklad vypíše přiřazení rolí pro *pharma. prodej projectforecast* skupiny prostředků:

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="grant-access"></a>Udělení přístupu

V RBAC udělit přístup, můžete vytvořit přiřazení role.

### <a name="create-a-role-assignment-for-a-user"></a>Umožňuje vytvořit přiřazení role pro uživatele

Chcete-li vytvořit přiřazení role pro uživatele v oboru skupiny prostředků, použijte [vytvořit přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

Následující příklad přiřadí *Přispěvatel virtuálních počítačů* role *patlong@contoso.com* uživatel na *pharma. prodej projectforecast* oboru skupiny prostředků:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="create-a-role-assignment-for-a-group"></a>Umožňuje vytvořit přiřazení role pro skupinu

Chcete-li vytvořit přiřazení role pro skupinu, použijte [vytvořit přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Následující příklad přiřadí *čtečky* role *Ann Mack Team* skupiny s ID 22222222-2222-2222-2222-222222222222 na obor předplatného. Chcete-li získat ID skupiny, můžete použít [seznam skupiny ad az](/cli/azure/ad/group#az-ad-group-list) nebo [zobrazit skupiny ad az](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

Následující příklad přiřadí *Přispěvatel virtuálních počítačů* role *Ann Mack Team* skupiny s ID 22222222-2222-2222-2222-222222222222 v oboru prostředků pro virtuální síť s názvem *pharma prodej projektu sítí*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Umožňuje vytvořit přiřazení role pro aplikaci

Chcete-li vytvořit roli pro aplikaci, použijte [vytvořit přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Následující příklad přiřadí *Přispěvatel virtuálních počítačů* role aplikace s 44444444-4444-4444-4444-444444444444 ID objektu na *pharma. prodej projectforecast* skupiny prostředků rozsah. Chcete-li získat ID objektu aplikace, můžete použít [seznamu aplikací ad az](/cli/azure/ad/app#az-ad-app-list) nebo [zobrazení aplikací ad az](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-access"></a>Odebrat přístup

V RBAC, k odebrání přístupu, je odstranit přiřazení role pomocí [odstranit přiřazení role az](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

Následující příklad odebere *Přispěvatel virtuálních počítačů* přiřazení role z *patlong@contoso.com* uživatele na *pharma. prodej projectforecast* prostředků Skupina:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

Následující příklad odebere *čtečky* roli z *Ann Mack Team* skupiny s ID 22222222-2222-2222-2222-222222222222 na obor předplatného. Chcete-li získat ID skupiny, můžete použít [seznam skupiny ad az](/cli/azure/ad/group#az-ad-group-list) nebo [zobrazit skupiny ad az](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Další postup

- [Kurz: Vytvoření vlastní role pomocí rozhraní příkazového řádku Azure](tutorial-custom-role-cli.md)
- [Použití rozhraní příkazového řádku Azure ke správě prostředků Azure a skupiny prostředků](../azure-resource-manager/xplat-cli-azure-resource-manager.md)