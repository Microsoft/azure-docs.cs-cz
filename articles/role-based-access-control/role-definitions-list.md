---
title: Seznam definic rolí v Azure RBAC pomocí Azure Portal, Azure PowerShell, Azure CLI nebo REST API | Dokumenty společnosti Microsoft
description: Zjistěte, jak vypsat předdefinované a vlastní role v Azure RBAC pomocí Azure Portal, Azure PowerShell, Azure CLI nebo REST API.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062985"
---
# <a name="list-role-definitions-in-azure-rbac"></a>Seznam definic rolí v Azure RBAC

Definice role je kolekce oprávnění, která lze provést, například čtení, zápis a odstranění. Obvykle se tomu říká jen role. [Azure řízení přístupu na základě rolí (RBAC)](overview.md) má více než 120 [předdefinovaných rolí](built-in-roles.md) nebo můžete vytvořit vlastní role. Tento článek popisuje, jak vypsat předdefinované a vlastní role, které můžete použít k udělení přístupu k prostředkům Azure.

Seznam rolí správce služby Azure Active Directory najdete v tématu [Oprávnění rolí správce ve službě Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="azure-portal"></a>portál Azure

### <a name="list-all-roles"></a>Vypsat všechny role

Podle těchto kroků zobrazíte všechny role na webu Azure Portal.

1. Na webu Azure Portal klikněte na **Všechny služby** a vyberte libovolný obor. Můžete například vybrat **skupiny pro správu**, **Odběry**, **Skupiny prostředků**nebo prostředek.

1. Klikněte na konkrétní zdroj.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Kliknutím na kartu **Role** zobrazíte seznam všech předdefinovaných a vlastních rolí.

   Můžete zobrazit počet uživatelů a skupin, které jsou přiřazeny ke každé roli v aktuálním oboru.

   ![Seznam Role](./media/role-definitions-list/roles-list.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Vypsat všechny role

Chcete-li vypsat všechny role v Azure PowerShellu, použijte [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

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

### <a name="list-a-role-definition"></a>Vypsat definici role

Chcete-li uvést podrobnosti o konkrétní roli, použijte [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

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

### <a name="list-a-role-definition-in-json-format"></a>Vypsat definici role ve formátu JSON

Chcete-li uvést roli ve formátu JSON, použijte [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

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

### <a name="list-permissions-of-a-role-definition"></a>Seznam oprávnění definice role

Chcete-li vypsat oprávnění pro určitou roli, použijte [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

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

Chcete-li vypsat všechny role v rozhraní příkazového příkazového [příkazového příkazu](/cli/azure/role/definition#az-role-definition-list)k webu Azure, použijte seznam definic rolí az .

```azurecli
az role definition list
```

V následujícím příkladu je uveden název a popis všech dostupných definic rolí:

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

V následujícím příkladu jsou uvedeny všechny předdefinované role.

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

### <a name="list-a-role-definition"></a>Vypsat definici role

Chcete-li zobrazit podrobnosti o roli, použijte [seznam definic role az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list --name <role_name>
```

V následujícím příkladu je uvedena definice role *přispěvatele:*

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

### <a name="list-permissions-of-a-role-definition"></a>Seznam oprávnění definice role

V následujícím příkladu jsou uvedeny pouze *akce* a *notActions* role *přispěvatele.*

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

V následujícím příkladu jsou uvedeny pouze akce role *přispěvatele virtuálního počítače.*

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

Chcete-li vypsat definice rolí, použijte definice rolí – rozhraní REST API [seznamu.](/rest/api/authorization/roledefinitions/list) Chcete-li upřesnit výsledky, zadejte obor a volitelný filtr.

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{scope}* oborem, pro který chcete vypsat definice rolí.

    > [!div class="mx-tableFixed"]
    > | Rozsah | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Skupina pro správu |
    > | `subscriptions/{subscriptionId1}` | Předplatné |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Skupina prostředků |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Prostředek |

    V předchozím příkladu microsoft.web je poskytovatel prostředků, který odkazuje na instanci služby App Service. Podobně můžete použít jiné zprostředkovatele prostředků a určit obor. Další informace najdete [v tématu Zprostředkovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md) a podporované operace [zprostředkovatele prostředků Azure Resource Manager](resource-provider-operations.md).  
     
1. Nahraďte *{filter}* podmínkou, kterou chcete použít pro filtrování seznamu definic rolí.

    > [!div class="mx-tableFixed"]
    > | Filtr | Popis |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Uvádí definice rolí pro zadaný obor a všechny podobory. |
    > | `$filter=type+eq+'{type}'` | Uvádí definice rolí zadaného typu. Typ role může `CustomRole` `BuiltInRole`být nebo . |

### <a name="list-a-role-definition"></a>Vypsat definici role

Chcete-li zobrazit podrobnosti o konkrétní roli, použijte [definice rolí – získat](/rest/api/authorization/roledefinitions/get) nebo definice rolí – získat pomocí [id](/rest/api/authorization/roledefinitions/getbyid) rozhraní REST API.

1. Začněte s následujícím požadavkem:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    Pro definici role na úrovni adresáře můžete použít tento požadavek:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. V rámci identifikátoru URI nahraďte *{scope}* oborem, pro který chcete uvést definici role.

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
- [Seznam přiřazení rolí pomocí Azure RBAC a portálu Azure](role-assignments-list-portal.md)
- [Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a portálu Azure](role-assignments-portal.md)
