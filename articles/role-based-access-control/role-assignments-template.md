---
title: Správa přístupu pomocí RBAC a Azure Resource Manageru šablon | Dokumentace Microsoftu
description: Zjistěte, jak spravovat přístup pro uživatele, skupiny a aplikace pomocí řízení přístupu na základě role (RBAC) a šablon Azure Resource Manageru.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5e080614d4f0001a0bf1b44dd402f37db2463e03
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206100"
---
# <a name="manage-access-using-rbac-and-azure-resource-manager-templates"></a>Správa přístupu pomocí RBAC a Azure Resource Manageru šablony

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob správy přístupu k prostředkům v Azure. Kromě použití Azure Powershellu nebo rozhraní příkazového řádku Azure, můžete spravovat přístup k prostředkům Azure pomocí RBAC a [šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md). Šablony můžou být užitečné, pokud budete potřebovat k nasazení prostředků konzistentně a opakovaně. Tento článek popisuje, jak můžete spravovat přístupu pomocí RBAC a šablony.

## <a name="example-template-to-create-a-role-assignment"></a>Příklad šablony k vytvoření přiřazení role

V RBAC se přístup uděluje vytvořením přiřazení role. Následující šablona ukazuje:
- Jak přiřadit roli uživatele, skupinu nebo aplikaci v oboru skupiny prostředků
- Určení role vlastník, Přispěvatel a čtenář jako parametr

Použití šablony, je nutné zadat následující vstupy:
- Název skupiny prostředků
- Jedinečný identifikátor uživatele, skupinu nebo aplikaci přiřadit roli
- Přiřazení role
- Jedinečný identifikátor, který se použije pro přiřazení role

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
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "scope": "[resourceGroup().id]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2017-05-01",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('scope')]"
      }
    }
  ]
}
```

Následuje příklad čtečku přiřazení role uživateli po nasazení šablony.

![Přiřazení role pomocí šablony](./media/role-assignments-template/role-assignment-template.png)

## <a name="deploy-template-using-azure-powershell"></a>Nasazení šablony pomocí Azure Powershellu

Pokud chcete nasadit předchozí šablonu pomocí prostředí Azure PowerShell, postupujte takto.

1. Vytvořte nový soubor s názvem rbac rg.json a zkopírujte předchozí šablony.

1. Přihlaste se k [Azure PowerShellu](/powershell/azure/authenticate-azureps).

1. Získá jedinečný identifikátor uživatele, skupinu nebo aplikaci. Například můžete použít [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) zobrazte seznam uživatelů Azure AD.

    ```azurepowershell
    Get-AzureRmADUser
    ```

1. Generovat jedinečný identifikátor, který se použije pro přiřazení role pomocí nástroje identifikátor GUID. Tento identifikátor má následující formát: `11111111-1111-1111-1111-111111111111`

1. Vytvořte skupinu prostředků příklad.

    ```azurepowershell
    New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Použití [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) příkaz ke spuštění nasazení.

    ```azurepowershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    ```

    Zobrazí se výzva k zadání požadovaných parametrů. Následuje příklad výstupu.

    ```Output
    PS /home/user> New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    
    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    principalId: 22222222-2222-2222-2222-222222222222
    builtInRoleType: Reader
    roleNameGuid: 11111111-1111-1111-1111-111111111111
    
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

## <a name="deploy-template-using-the-azure-cli"></a>Nasazení šablony pomocí rozhraní příkazového řádku Azure

Pokud chcete nasadit předchozí šablonu pomocí Azure CLI, postupujte takto.

1. Vytvořte nový soubor s názvem rbac rg.json a zkopírujte předchozí šablony.

1. Přihlaste se k [rozhraní příkazového řádku Azure](/cli/azure/authenticate-azure-cli).

1. Získá jedinečný identifikátor uživatele, skupinu nebo aplikaci. Například můžete použít [seznamu uživatelů služby ad az](/cli/azure/ad/user#az-ad-user-list) zobrazte seznam uživatelů Azure AD.

    ```azurecli
    az ad user list
    ```

1. Generovat jedinečný identifikátor, který se použije pro přiřazení role pomocí nástroje identifikátor GUID. Tento identifikátor má následující formát: `11111111-1111-1111-1111-111111111111`

1. Vytvořte skupinu prostředků příklad.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Použití [vytvořit nasazení skupiny pro az](/cli/azure/group/deployment#az-group-deployment-create) příkaz ke spuštění nasazení.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    ```

    Zobrazí se výzva k zadání požadovaných parametrů. Následuje příklad výstupu.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    Please provide string value for 'principalId' (? for help): 22222222-2222-2222-2222-222222222222
    Please provide string value for 'builtInRoleType' (? for help):
     [1] Owner
     [2] Contributor
     [3] Reader
    Please enter a choice [1]: 3
    Please provide string value for 'roleNameGuid' (? for help): 11111111-1111-1111-1111-111111111111
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
    
## <a name="next-steps"></a>Další postup

- [Vytvoření a nasazení první šablony Azure Resource Manageru](../azure-resource-manager/resource-manager-create-first-template.md)
- [Princip struktury a syntaxe šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md)
- [Šablony rychlý start Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
