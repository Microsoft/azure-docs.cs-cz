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
ms.date: 09/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b4eebf7dac4d388411f570b1546c96e3b82b2a98
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950062"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Správa přístupu k prostředkům Azure pomocí šablon RBAC a Azure Resource Manager

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, jakým můžete spravovat přístup k prostředkům Azure. Kromě použití Azure PowerShell nebo rozhraní příkazového řádku Azure CLI můžete spravovat přístup k prostředkům Azure pomocí [šablon Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Šablony mohou být užitečné, pokud potřebujete nasadit prostředky konzistentně a opakovaně. Tento článek popisuje, jak můžete spravovat přístup pomocí RBAC a šablon.

## <a name="create-a-role-assignment-at-a-resource-group-scope-without-parameters"></a>Vytvoření přiřazení role v oboru skupiny prostředků (bez parametrů)

Pokud chcete udělit přístup, vytvořte v nástroji RBAC přiřazení role. Následující šablona ukazuje základní způsob, jak vytvořit přiřazení role. Některé hodnoty jsou zadány v rámci šablony. Následující šablona znázorňuje:

-  Přiřazení role [čtenáře](built-in-roles.md#reader) k uživateli, skupině nebo aplikaci v oboru skupiny prostředků

Chcete-li použít šablonu, je nutné provést následující akce:

- Vytvořit nový soubor JSON a zkopírovat šablonu
- Nahraďte `<your-principal-id>` jedinečným identifikátorem uživatele, skupiny nebo aplikace, ke které se role přiřadí. Identifikátor má formát: `11111111-1111-1111-1111-111111111111`

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Tady jsou příklady [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) Commands, jak spustit nasazení ve skupině prostředků s názvem example.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Níže vidíte příklad přiřazení role čtenáře uživateli pro skupinu prostředků po nasazení šablony.

![Přiřazení role v oboru skupiny prostředků](./media/role-assignments-template/role-assignment-template.png)

## <a name="create-a-role-assignment-at-a-resource-group-or-subscription-scope"></a>Vytvoření přiřazení role v oboru skupiny prostředků nebo předplatného

Předchozí šablona není příliš flexibilní. Následující šablona používá parametry a lze ji použít v různých oborech. Následující šablona znázorňuje:

- Postup přiřazení role uživateli, skupině nebo aplikaci v oboru skupiny prostředků nebo předplatném
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

Pokud chcete získat jedinečný identifikátor uživatele, ke kterému chcete přiřadit roli, můžete použít příkazy [Get-AzADUser](/powershell/module/az.resources/get-azaduser) nebo [AZ AD User show](/cli/azure/ad/user#az-ad-user-show) .

```azurepowershell
$userid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
```

Rozsah přiřazení role je určen z úrovně nasazení. Tady jsou příklady [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) Commands, jak spustit nasazení v oboru skupiny prostředků.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

Tady jsou příklady [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) a [AZ Deployment Create](/cli/azure/deployment#az-deployment-create) Command, jak spustit nasazení v oboru předplatného a zadat umístění.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

> [!NOTE]
> Tato šablona není idempotentní, pokud není stejná hodnota `roleNameGuid` uvedena jako parametr pro každé nasazení šablony. Pokud není k dispozici žádná `roleNameGuid`, ve výchozím nastavení se v každém nasazení vygeneruje nový identifikátor GUID a následná nasazení selžou s chybou `Conflict: RoleAssignmentExists`.

## <a name="create-a-role-assignment-at-a-resource-scope"></a>Vytvoření přiřazení role v oboru prostředků

Pokud potřebujete vytvořit přiřazení role na úrovni prostředku, formát přiřazení role se liší. Zadejte obor názvů poskytovatele prostředků a typ prostředku, ke kterému chcete přiřadit roli. Do názvu přiřazení role zadáte také název prostředku.

Pro typ a název přiřazení role použijte následující formát:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Následující šablona znázorňuje:

- Jak vytvořit nový účet úložiště
- Přiřazení role uživateli, skupině nebo aplikaci v oboru účtu úložiště
- Jak zadat role vlastníka, přispěvatele a čtenáře jako parametr

Chcete-li použít šablonu, je nutné zadat následující vstupy:

- Jedinečný identifikátor uživatele, skupiny nebo aplikace, ke které se role přiřadí
- Role, která se má přiřadit

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
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(parameters('storageName'))))]",
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

K nasazení předchozí šablony použijte příkazy skupiny prostředků. Tady jsou příklady [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) Commands, jak spustit nasazení v oboru prostředků.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Contributor
```

Níže vidíte příklad přiřazení role přispěvatele uživateli pro účet úložiště po nasazení šablony.

![Přiřazení role v oboru prostředků](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="create-a-role-assignment-for-a-new-service-principal"></a>Vytvoření přiřazení role pro nový instanční objekt

Pokud vytvoříte nový instanční objekt a hned se pokusíte přiřadit roli k tomuto instančnímu objektu, toto přiřazení role může v některých případech selhat. Pokud například vytvoříte novou spravovanou identitu a pak se pokusíte přiřadit roli k tomuto instančnímu objektu ve stejné šabloně Azure Resource Manager, přiřazení role může selhat. Důvodem této chyby je nejspíš zpoždění replikace. Instanční objekt se vytvoří v jedné oblasti. přiřazení role se ale může vyskytnout v jiné oblasti, která ještě nereplikoval instanční objekt. Chcete-li tento scénář vyřešit, nastavte vlastnost `principalType` na hodnotu `ServicePrincipal` při vytváření přiřazení role.

Následující šablona znázorňuje:

- Postup vytvoření nového objektu spravované služby identity
- Určení `principalType`
- Přiřazení role Přispěvatel k tomuto instančnímu objektu v oboru skupiny prostředků

Chcete-li použít šablonu, je nutné zadat následující vstupy:

- Základní název spravované identity nebo můžete použít výchozí řetězec.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Tady jsou příklady [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) Commands, jak spustit nasazení v oboru skupiny prostředků.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Následuje příklad přiřazení role přispěvatele k novému instančnímu objektu služby spravované identity po nasazení šablony.

![Přiřazení role nového objektu spravované služby identity](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: vytvoření a nasazení šablon Azure Resource Manager pomocí Azure Portal](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Pochopení struktury a syntaxe šablon Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
- [Vytvoření skupin prostředků a prostředků na úrovni předplatného](../azure-resource-manager/deploy-to-subscription.md)
- [Šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
