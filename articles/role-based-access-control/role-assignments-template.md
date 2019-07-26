---
title: Správa přístupu k prostředkům Azure pomocí RBAC a šablon Azure Resource Manager | Microsoft Docs
description: Naučte se spravovat přístup k prostředkům Azure pro uživatele, skupiny a aplikace pomocí řízení přístupu na základě rolí (RBAC) a šablon Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e6511ff84c251577a5ff483f892387ab7d3d4d41
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360446"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Správa přístupu k prostředkům Azure pomocí šablon RBAC a Azure Resource Manager

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, jakým můžete spravovat přístup k prostředkům Azure. Kromě použití Azure PowerShell nebo rozhraní příkazového řádku Azure CLI můžete spravovat přístup k prostředkům Azure pomocí šablon RBAC a [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Šablony mohou být užitečné, pokud potřebujete nasadit prostředky konzistentně a opakovaně. Tento článek popisuje, jak můžete spravovat přístup pomocí RBAC a šablon.

## <a name="assign-role-to-resource-group-or-subscription"></a>Přiřazení role ke skupině prostředků nebo předplatnému

V RBAC se přístup uděluje vytvořením přiřazení role. Následující šablona znázorňuje:
- Přiřazení role uživateli, skupině nebo aplikaci buď v rámci skupiny prostředků nebo oboru předplatného
- Jak zadat role vlastníka, přispěvatele a čtenáře jako parametr

Chcete-li použít šablonu, je nutné zadat následující vstupy:
- Jedinečný identifikátor uživatele, skupiny nebo aplikace, ke které se role přiřadí
- Role, která se má přiřadit
- Jedinečný identifikátor, který se použije pro přiřazení role, nebo můžete použít výchozí identifikátor.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

Níže vidíte příklad přiřazení role čtenáře uživateli pro skupinu prostředků po nasazení šablony.

![Přiřazení role pomocí šablony](./media/role-assignments-template/role-assignment-template.png)

Rozsah přiřazení role je určen z úrovně nasazení. V tomto článku jsou uvedené příkazy pro nasazení skupiny prostředků i na úrovni předplatného.

## <a name="assign-role-to-resource"></a>Přiřazení role k prostředku

Pokud potřebujete vytvořit přiřazení role na úrovni prostředku, formát přiřazení role se liší. Zadejte obor názvů poskytovatele prostředků a typ prostředku, ke kterému chcete přiřadit roli. Do názvu přiřazení role zadáte také název prostředku.

Pro typ a název přiřazení role použijte následující formát:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Následující šablona nasadí účet úložiště a přiřadí mu roli. Nasadíte ji pomocí příkazů skupiny prostředků.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    },
    "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "apiVersion": "2019-04-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[parameters('location')]",
      "sku": {
          "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleNameGuid'))]",
      "dependsOn": [
          "[variables('storageName')]"
      ],
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

Níže vidíte příklad přiřazení role přispěvatele uživateli pro účet úložiště po nasazení šablony.

![Přiřazení role pomocí šablony](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="deploy-template-using-azure-powershell"></a>Nasazení šablony pomocí Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Pokud chcete nasadit předchozí šablonu do skupiny prostředků nebo předplatného pomocí Azure PowerShell, postupujte podle těchto kroků.

1. Vytvořte nový soubor s názvem RBAC-RG. JSON a zkopírujte předchozí šablonu.

1. Přihlaste se k [Azure PowerShellu](/powershell/azure/authenticate-azureps).

1. Získat jedinečný identifikátor uživatele, skupiny nebo aplikace. K výpisu uživatelů Azure AD můžete použít například příkaz [Get-AzADUser](/powershell/module/az.resources/get-azaduser) .

    ```azurepowershell
    $userid = (Get-AzADUser -DisplayName "{name}").id
    ```

1. Šablona vygeneruje výchozí hodnotu pro identifikátor GUID, který se používá k identifikaci přiřazení role. Pokud potřebujete zadat konkrétní identifikátor GUID, předejte tuto hodnotu v parametru pro parametr roleNameGuid. Identifikátor má formát:`11111111-1111-1111-1111-111111111111`

Pokud chcete přiřadit roli na úrovni prostředku nebo skupiny prostředků, postupujte podle těchto kroků:

1. Vytvořte ukázkovou skupinu prostředků.

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Nasazení spustíte pomocí příkazu [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) .

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    ```

    Následující příklad ukazuje příklad výstupu.

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

Pokud chcete přiřadit roli na úrovni předplatného, použijte příkaz [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) a zadejte umístění pro nasazení.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
```

Obsahuje podobný výstup příkazu pro nasazení skupin prostředků.

## <a name="deploy-template-using-the-azure-cli"></a>Nasazení šablony pomocí Azure CLI

Pokud chcete nasadit předchozí šablonu pomocí Azure CLI do skupiny prostředků nebo předplatného, postupujte podle těchto kroků.

1. Vytvořte nový soubor s názvem RBAC-RG. JSON a zkopírujte předchozí šablonu.

1. Přihlaste se k [Azure CLI](/cli/azure/authenticate-azure-cli).

1. Získat jedinečný identifikátor uživatele, skupiny nebo aplikace. Například můžete použít příkaz [AZ AD User show](/cli/azure/ad/user#az-ad-user-show) k zobrazení uživatele Azure AD.

    ```azurecli
    userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
    ```

1. Šablona vygeneruje výchozí hodnotu pro identifikátor GUID, který se používá k identifikaci přiřazení role. Pokud potřebujete zadat konkrétní identifikátor GUID, předejte tuto hodnotu v parametru pro parametr roleNameGuid. Identifikátor má formát:`11111111-1111-1111-1111-111111111111`

Pokud chcete přiřadit roli na úrovni prostředku nebo skupiny prostředků, postupujte podle těchto kroků:

1. Vytvořte ukázkovou skupinu prostředků.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Nasazení spustíte pomocí příkazu [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) .

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    ```

    Následující příklad ukazuje příklad výstupu.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```

Pokud chcete přiřadit roli na úrovni předplatného, použijte příkaz [AZ Deployment Create](/cli/azure/deployment#az-deployment-create) a zadejte umístění pro nasazení.

```azurecli
az deployment create --location centralus --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
```

Obsahuje podobný výstup příkazu pro nasazení skupin prostředků.

## <a name="next-steps"></a>Další postup

- [Rychlé zprovoznění: Vytváření a nasazování šablon Azure Resource Manager pomocí Azure Portal](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Struktura a syntaxe šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md)
- [Šablony Azure pro rychlý start](https://azure.microsoft.com/resources/templates/?term=rbac)
