---
title: Správa přístupu k prostředkům Azure pomocí RBAC a rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Další informace o správě přístupu k prostředkům Azure pro uživatele, skupiny nebo aplikace pomocí rozhraní příkazového řádku Azure a řízení přístupu na základě rolí (RBAC). To zahrnuje výpis přístupu, udělení přístupu a odebrání přístupu.
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
ms.date: 04/17/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: bc5deb614e2ac6e47ff3bf241943df92d97699b2
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295179"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Správa přístupu k prostředkům Azure pomocí RBAC a rozhraní příkazového řádku Azure

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, která můžete spravovat přístup k prostředkům Azure. Tento článek popisuje, jak spravovat přístup pro uživatele, skupiny a aplikace pomocí RBAC a rozhraní příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky

Ke správě přístupu, potřebujete jeden z následujících akcí:

* [Bash ve službě Azure Cloud Shell](/azure/cloud-shell/overview)
* [Azure CLI](/cli/azure)

## <a name="list-roles"></a>Výpis rolí

K zobrazení seznamu všech definic rolí k dispozici, použijte [az role definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

Následující příklad vypíše název a popis všech definic rolí k dispozici:

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

Následující příklad zobrazí seznam všech definic předdefinovaných rolí:

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

## <a name="list-a-role-definition"></a>Seznam definice role

Seznam definice role, použít [az role definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

Následující příklad zobrazí *Přispěvatel* definice role:

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
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

### <a name="list-actions-of-a-role"></a>Seznam akcí role

Následující příklad vypíše pouze *akce* a *notActions* z *Přispěvatel* role:

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

Následující příklad vypíše pouze akce *Přispěvatel virtuálních počítačů* role:

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

## <a name="list-access"></a>Výpis přístupu

V RBAC pro přístup k seznamu, můžete seznam přiřazení rolí.

### <a name="list-role-assignments-for-a-user"></a>Výpis přiřazení rolí pro uživatele

K zobrazení seznamu přiřazení rolí pro konkrétního uživatele, použijte [seznam přiřazení rolí az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Ve výchozím nastavení se zobrazí pouze přímé přiřazení omezená na předplatné. Chcete-li zobrazit přiřazení vymezeny prostředek nebo skupinu, použijte `--all` a pokud chcete zobrazit zděděné asisgnments, použijte `--include-inherited`.

Následující příklad vypíše přiřazení rolí, které jsou přiřazeny přímo *patlong\@contoso.com* uživatele:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Výpis přiřazení rolí pro skupinu prostředků

K zobrazení seznamu přiřazení rolí, která platí pro skupinu prostředků, použijte [seznam přiřazení rolí az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

Následující příklad vypíše přiřazení rolí pro *pharma sales* skupina prostředků:

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="grant-access"></a>Udělení přístupu

V RBAC se přístup uděluje vytvořením přiřazení role.

### <a name="create-a-role-assignment-for-a-user"></a>Vytvořit přiřazení role pro uživatele

Chcete-li vytvořit přiřazení role pro uživatele v oboru skupiny prostředků, použijte [vytvořit přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

Následující příklad přiřadí *Přispěvatel virtuálních počítačů* role *patlong\@contoso.com* uživatele na *pharma sales* oboru skupiny prostředků:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Vytvořit přiřazení role pomocí ID jedinečné role

Existuje několik případů, kdy název role může změnit, například:

- Používáte vlastní vlastní role a změníte název.
- Používáte roli ve verzi preview, která má **(Preview)** v názvu. Po vydání role se přejmenuje roli.

> [!IMPORTANT]
> Verze preview je k dispozici bez smlouvy o úrovni služeb a se nedoporučuje pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I v případě, že role je přejmenována, nezmění se role ID. Pokud používáte k vytvoření přiřazení role, skriptů a automatizaci, je osvědčeným postupem je použít jedinečné ID role místo názvu role. Proto pokud přejmenování role skripty budou pravděpodobně fungovat.

Chcete-li vytvořit přiřazení role pomocí ID jedinečné role místo názvu role, použít [vytvořit přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

Následující příklad přiřadí [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) role *patlong\@contoso.com* uživatele na *pharma sales* oboru skupiny prostředků. K získání ID jedinečnou roli, můžete použít [az role definition list](/cli/azure/role/definition#az-role-definition-list) nebo naleznete v tématu [předdefinované role pro prostředky Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Vytvořit přiřazení role pro skupinu

Chcete-li vytvořit přiřazení role pro skupinu, použijte [vytvořit přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Následující příklad přiřadí *čtečky* role *Ann Mack týmu* skupina s ID 22222222-2222-2222-2222-222222222222 v oboru předplatného. Pokud chcete získat ID skupiny, můžete použít [seznamu skupin ad az](/cli/azure/ad/group#az-ad-group-list) nebo [zobrazit skupiny az ad](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

Následující příklad přiřadí *Přispěvatel virtuálních počítačů* role *Ann Mack týmu* skupina s ID 22222222-2222-2222-2222-222222222222 v oboru prostředků pro virtuální síť s názvem *pharma sales projektu sítě*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Vytvořit přiřazení role aplikace

Chcete-li vytvořit role pro aplikaci, použijte [vytvořit přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Následující příklad přiřadí *Přispěvatel virtuálních počítačů* role k aplikaci s 44444444-4444-4444-4444-444444444444 ID objektu na *pharma sales* oboru skupiny prostředků. Pokud chcete získat ID objektu aplikace, můžete použít [az ad app list](/cli/azure/ad/app#az-ad-app-list) nebo [az ad app show](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

## <a name="remove-access"></a>Odebrání přístupu

V RBAC, k odebrání přístupu, můžete odebrat přiřazení role pomocí [odstranit přiřazení role az](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

Následující příklad odebere *Přispěvatel virtuálních počítačů* přiřazení role z *patlong\@contoso.com* uživatele na *pharma sales* Skupina prostředků:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Následující příklad odebere *čtečky* role *Ann Mack týmu* skupina s ID 22222222-2222-2222-2222-222222222222 v oboru předplatného. Pokud chcete získat ID skupiny, můžete použít [seznamu skupin ad az](/cli/azure/ad/group#az-ad-group-list) nebo [zobrazit skupiny az ad](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="next-steps"></a>Další postup

- [Kurz: Vytvoření vlastní role pro prostředky Azure pomocí Azure CLI](tutorial-custom-role-cli.md)
- [Pomocí Azure CLI ke správě prostředků a skupin prostředků Azure](../azure-resource-manager/cli-azure-resource-manager.md)
