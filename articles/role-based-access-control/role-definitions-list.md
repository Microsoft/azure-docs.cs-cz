---
title: Seznam definic rolí v Azure RBAC pomocí Azure Portal, Azure PowerShell, Azure CLI nebo REST API | Microsoft Docs
description: Přečtěte si, jak vypsat předdefinované a vlastní role v Azure RBAC pomocí Azure Portal, Azure PowerShell, Azure CLI nebo REST API.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: aa888eedc81ceb3188f801e273c70722207bf512
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062985"
---
# <a name="list-role-definitions-in-azure-rbac"></a>Seznam definic rolí v Azure RBAC

Definice role je kolekce oprávnění, která se dají provést, jako je čtení, zápis a odstranění. Obvykle se nazývá jenom role. [Řízení přístupu na základě role (RBAC) v Azure](overview.md) má víc než 120 [integrovaných rolí](built-in-roles.md) , nebo můžete vytvořit vlastní role. Tento článek popisuje, jak vytvořit seznam integrovaných a vlastních rolí, které můžete použít k udělení přístupu k prostředkům Azure.

Seznam rolí správce pro Azure Active Directory najdete v tématu [oprávnění role správce v Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="azure-portal"></a>portál Azure

### <a name="list-all-roles"></a>Vypsat všechny role

Pomocí těchto kroků můžete zobrazit seznam všech rolí v Azure Portal.

1. V Azure Portal klikněte na **všechny služby** a pak vyberte libovolný obor. Můžete například vybrat **skupiny pro správu**, **předplatná**, **skupiny prostředků**nebo prostředek.

1. Klikněte na konkrétní prostředek.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Kliknutím na kartu **role** zobrazíte seznam všech předdefinovaných a vlastních rolí.

   Můžete zobrazit počet uživatelů a skupin, které jsou přiřazeny k jednotlivým rolím v aktuálním oboru.

   ![Seznam Role](./media/role-definitions-list/roles-list.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Vypsat všechny role

Chcete-li zobrazit seznam všech rolí v Azure PowerShell, použijte [příkaz Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-role-definition"></a>Seznam definice role

Chcete-li zobrazit podrobnosti o konkrétní roli, použijte [příkaz Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>Vypsání definice role ve formátu JSON

Chcete-li zobrazit seznam rolí ve formátu JSON, použijte [příkaz Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>Oprávnění k vypsání definice role

K zobrazení seznamu oprávnění pro konkrétní roli použijte [příkaz Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-all-roles"></a>Vypsat všechny role

Pokud chcete zobrazit seznam všech rolí v Azure CLI, použijte příkaz [AZ role definition list](/cli/azure/role/definition#az-role-definition-list).

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

Následující příklad zobrazí seznam všech předdefinovaných rolí.

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

### <a name="list-a-role-definition"></a>Seznam definice role

Chcete-li zobrazit podrobnosti o roli, použijte příkaz [AZ role definition list](/cli/azure/role/definition#az-role-definition-list).

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

### <a name="list-permissions-of-a-role-definition"></a>Oprávnění k vypsání definice role

Následující příklad vypíše pouze *Akce* a *notActions* role *přispěvatele* .

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

Následující příklad vypíše pouze akce role *Přispěvatel virtuálních počítačů* .

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

## <a name="rest-api"></a>REST API

### <a name="list-role-definitions"></a>Zobrazení seznamu definic rolí

K vypsání definic rolí použijte REST API [seznam definice rolí](/rest/api/authorization/roledefinitions/list) . K upřesnění výsledků zadáte obor a volitelný filtr.

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete vypsat definice rolí.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Prostředek |

    V předchozím příkladu je Microsoft. Web poskytovatelem prostředků, který odkazuje na instanci App Service. Podobně můžete použít jiné poskytovatele prostředků a zadat obor. Další informace najdete v tématech [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md) a podporované [Azure Resource Manager operace poskytovatele prostředků](resource-provider-operations.md).  
     
1. Nahraďte *{Filter}* podmínkou, kterou chcete použít k filtrování seznamu definic rolí.

    > [!div class="mx-tableFixed"]
    > | Filtr | Popis |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Vypisuje definice rolí pro zadaný obor a všechny podobory. |
    > | `$filter=type+eq+'{type}'` | Vypisuje definice rolí zadaného typu. Typ role může být `CustomRole` nebo. `BuiltInRole` |

### <a name="list-a-role-definition"></a>Seznam definice role

Chcete-li zobrazit podrobnosti o konkrétní roli, použijte [Definice rolí – získat](/rest/api/authorization/roledefinitions/get) nebo [Definice rolí – získat podle ID](/rest/api/authorization/roledefinitions/getbyid) REST API.

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    V případě definice role na úrovni adresáře můžete použít tuto žádost:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{Scope}* oborem, pro který chcete vypsat definici role.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Prostředek |
     
1. Nahraďte *{roleDefinitionId}* identifikátorem definice role.

## <a name="next-steps"></a>Další kroky

- [Předdefinované role pro prostředky Azure](built-in-roles.md)
- [Vlastní role pro prostředky Azure](custom-roles.md)
- [Seznam přiřazení rolí pomocí Azure RBAC a Azure Portal](role-assignments-list-portal.md)
- [Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a Azure Portal](role-assignments-portal.md)
