---
title: Správa přístupu k prostředkům Azure pomocí RBAC a Azure CLI | Microsoft Docs
description: Naučte se spravovat přístup k prostředkům Azure pro uživatele, skupiny a aplikace pomocí řízení přístupu na základě role (RBAC) a rozhraní příkazového řádku Azure CLI. To zahrnuje výpis přístupu, udělení přístupu a odebrání přístupu.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1b898f42fa6f66fba7c84daa67769249642bd986
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996486"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Správa přístupu k prostředkům Azure pomocí RBAC a Azure CLI

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, jakým můžete spravovat přístup k prostředkům Azure. Tento článek popisuje, jak spravovat přístup pro uživatele, skupiny a aplikace pomocí RBAC a Azure CLI.

## <a name="prerequisites"></a>Požadavky

Ke správě přístupu budete potřebovat jednu z následujících možností:

* [Bash v Azure Cloud Shell](/azure/cloud-shell/overview)
* [Azure CLI](/cli/azure)

## <a name="list-roles"></a>Výpis rolí

Chcete-li zobrazit seznam všech dostupných definic rolí, použijte příkaz [AZ role definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

Následující příklad uvádí název a popis všech dostupných definic rolí:

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

V následujícím příkladu jsou uvedeny všechny předdefinované definice rolí:

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

K vypsání definice role použijte příkaz [AZ role definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

V následujícím příkladu je uvedena definice role *Přispěvatel* :

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

Následující příklad vypíše pouze *Akce* a *notActions* role přispěvatele :

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

Následující příklad vypíše pouze akce role *Přispěvatel virtuálních počítačů* :

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

V části RBAC pro vypsání přístupu k seznamu se zobrazí seznam přiřazení rolí.

### <a name="list-role-assignments-for-a-user"></a>Výpis přiřazení rolí pro uživatele

Chcete-li zobrazit seznam přiřazení rolí pro konkrétního uživatele, použijte příkaz [AZ role Assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Ve výchozím nastavení se zobrazí jenom Přímá přiřazení v oboru předplatného. Chcete-li zobrazit přiřazení v oboru podle prostředku nebo skupiny `--all` , použijte a k zobrazení zděděných `--include-inherited`přiřazení použijte.

V následujícím příkladu jsou uvedena přiřazení rolí, která jsou přiřazena přímo uživateli *patlong\@contoso.com* :

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

### <a name="list-role-assignments-at-a-resource-group-scope"></a>Vypsat přiřazení rolí v oboru skupiny prostředků

Pokud chcete zobrazit seznam přiřazení rolí, která existují v oboru skupiny prostředků, použijte [seznam AZ role Assignment](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

V následujícím příkladu jsou uvedena přiřazení rolí pro skupinu prostředků *Pharma-Sales* :

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
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

...
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>Seznam přiřazení rolí v oboru předplatného

Chcete-li zobrazit seznam všech přiřazení rolí v oboru předplatného, použijte příkaz [AZ role Assignment list](/cli/azure/role/assignment#az-role-assignment-list). Pokud chcete získat ID předplatného, najdete ho v okně **předplatná** v Azure Portal nebo můžete použít příkaz [AZ Account list](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>Vypsat přiřazení rolí v oboru skupiny pro správu

Chcete-li zobrazit seznam všech přiřazení rolí v oboru skupiny pro správu, použijte příkaz [AZ role Assignment list](/cli/azure/role/assignment#az-role-assignment-list). Chcete-li získat ID skupiny pro správu, můžete ji najít v okně **skupiny pro správu** v Azure Portal nebo můžete použít [příkaz AZ Account Management-Group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="grant-access"></a>Udělit přístup

V RBAC se přístup uděluje vytvořením přiřazení role.

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Vytvoření přiřazení role pro uživatele v oboru skupiny prostředků

Pokud chcete udělit přístup k uživateli v oboru skupiny prostředků, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

Následující příklad přiřadí roli *Přispěvatel virtuálních počítačů* k *patlong\@uživatele contoso.com* v oboru skupiny prostředků *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Vytvoření přiřazení role pomocí jedinečného ID role

V některých případech se může stát, že se název role změní například takto:

- Používáte vlastní roli a Vy se rozhodnete název změnit.
- V názvu používáte roli Preview, která má **(Preview)** . Po uvolnění role se role přejmenuje.

> [!IMPORTANT]
> Verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I když je role přejmenována, ID role se nezmění. Pokud k vytváření přiřazení rolí používáte skripty nebo automatizaci, je vhodné místo názvu role použít jedinečné ID role. Proto pokud je role přejmenována, vaše skripty budou pravděpodobnější, že budou fungovat.

K vytvoření přiřazení role pomocí jedinečného ID role místo názvu role použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

Následující příklad přiřadí roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) k uživateli *patlong\@contoso.com* v oboru skupiny prostředků *Pharma-Sales* . Pokud chcete získat jedinečné ID role, můžete použít příkaz [AZ role definition list](/cli/azure/role/definition#az-role-definition-list) nebo informace [o předdefinovaných rolích pro prostředky Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Vytvoření přiřazení role pro skupinu

Chcete-li udělit přístup ke skupině, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Pokud chcete získat ID skupiny, můžete použít příkaz [AZ AD Group list](/cli/azure/ad/group#az-ad-group-list) nebo [AZ AD Group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Následující příklad přiřadí roli *Čtenář* ke skupině *týmu Ann Mack* s ID 22222222-2222-2222-2222-222222222222 v oboru předplatného.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

Následující příklad přiřadí roli *Přispěvatel virtuálních počítačů* ke skupině *Ann Mack týmu* s ID 22222222-2222-2222-2222-222222222222 v oboru prostředků pro virtuální síť s názvem *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application-at-a-resource-group-scope"></a>Vytvoření přiřazení role pro aplikaci v oboru skupiny prostředků

Chcete-li udělit přístup k aplikaci, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Chcete-li získat ID objektu aplikace, můžete použít příkaz [AZ AD App list](/cli/azure/ad/app#az-ad-app-list) nebo [AZ AD App show](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

Následující příklad přiřadí roli *Přispěvatel virtuálních počítačů* k aplikaci s ID objektu 44444444-4444-4444-4444-444444444444 v oboru skupiny prostředků *Pharma-Sales* .

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-user-at-a-subscription-scope"></a>Vytvoření přiřazení role pro uživatele v oboru předplatného

Pokud chcete udělit přístup k uživateli v oboru předplatného, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Pokud chcete získat ID předplatného, najdete ho v okně **předplatná** v Azure Portal nebo můžete použít příkaz [AZ Account list](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

Následující příklad přiřadí roli *Čtenář* k uživateli *annm\@example.com* v oboru předplatného.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>Vytvoření přiřazení role pro uživatele v oboru skupiny pro správu

Chcete-li udělit uživateli přístup v oboru skupiny pro správu, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create). Chcete-li získat ID skupiny pro správu, můžete ji najít v okně **skupiny pro správu** v Azure Portal nebo můžete použít [příkaz AZ Account Management-Group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Následující příklad přiřadí roli *Čtenář fakturace* k uživateli *Alain\@example.com* v oboru skupiny pro správu.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="remove-access"></a>Odebrat přístup

Pokud chcete odebrat přístup, odeberte přiřazení role v RBAC pomocí funkce [AZ role Assignment Delete](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

Následující příklad odebere přiřazení role *Přispěvatel virtuálních počítačů* z uživatele *patlong\@contoso.com* ve skupině prostředků *Pharma-Sales* :

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Následující příklad odebere roli *Čtenář* z *týmové skupiny Ann Mack* s ID 22222222-2222-2222-2222-222222222222 v oboru předplatného. Pokud chcete získat ID skupiny, můžete použít příkaz [AZ AD Group list](/cli/azure/ad/group#az-ad-group-list) nebo [AZ AD Group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

Následující příklad odebere roli *Čtenář fakturace* z uživatele *Alain\@example.com* v oboru skupiny pro správu. ID skupiny pro správu můžete získat pomocí ovládacího panelu [AZ Account Management-Group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření vlastní role pro prostředky Azure pomocí Azure CLI](tutorial-custom-role-cli.md)
- [Použití rozhraní příkazového řádku Azure ke správě prostředků a skupin prostředků Azure](../azure-resource-manager/cli-azure-resource-manager.md)
