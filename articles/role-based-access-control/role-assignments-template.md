---
title: Přiřazení rolí Azure pomocí šablon Azure Resource Manager – Azure RBAC
description: Naučte se, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí šablon Azure Resource Manager a řízení přístupu na základě role v Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/21/2021
ms.author: rolyon
ms.openlocfilehash: ba1df23b40de82a8ef901541884ef29ea0b504a1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771870"
---
# <a name="assign-azure-roles-using-azure-resource-manager-templates"></a>Přiřazení rolí Azure pomocí šablon Azure Resource Manager

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Kromě používání Azure PowerShell nebo rozhraní příkazového řádku Azure CLI můžete role přiřadit pomocí [šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Šablony mohou být užitečné, pokud potřebujete nasadit prostředky konzistentně a opakovaně. Tento článek popisuje, jak přiřadit role pomocí šablon.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="get-object-ids"></a>Získat ID objektů

Chcete-li přiřadit roli, je nutné zadat ID uživatele, skupiny nebo aplikace, ke které chcete přiřadit roli. ID má formát: `11111111-1111-1111-1111-111111111111` . ID můžete získat pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure.

### <a name="user"></a>Uživatel

Pokud chcete získat ID uživatele, můžete použít příkazy [Get-AzADUser](/powershell/module/az.resources/get-azaduser) nebo [AZ AD User show](/cli/azure/ad/user#az_ad_user_show) .

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Group (Skupina)

Pokud chcete získat ID skupiny, můžete použít příkazy [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) nebo [AZ AD Group show](/cli/azure/ad/group#az_ad_group_show) .

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>Spravované identity

Pokud chcete získat ID spravované identity, můžete použít příkaz [Get-AzAdServiceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) nebo [AZ AD SP](/cli/azure/ad/sp) .

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Aplikace

Pokud chcete získat ID instančního objektu (identity používaného aplikací), můžete použít příkazy [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) nebo [AZ AD SP list](/cli/azure/ad/sp#az_ad_sp_list) . U instančního objektu použijte ID objektu, **nikoli** ID aplikace.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="assign-an-azure-role"></a>Přiřazení role Azure

V Azure RBAC pro udělení přístupu přiřadíte roli.

### <a name="resource-group-scope-without-parameters"></a>Obor skupiny prostředků (bez parametrů)

Následující šablona ukazuje základní způsob přiřazení role. Některé hodnoty jsou zadány v rámci šablony. Následující šablona znázorňuje:

-  Přiřazení role [čtenáře](built-in-roles.md#reader) k uživateli, skupině nebo aplikaci v oboru skupiny prostředků

Chcete-li použít šablonu, je nutné provést následující akce:

- Vytvořit nový soubor JSON a zkopírovat šablonu
- Nahraďte `<your-principal-id>` ID uživatele, skupiny, spravované identity nebo aplikace, ke které se má přiřadit role.

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

Tady jsou příklady [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) Commands, jak spustit nasazení ve skupině prostředků s názvem example.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json
```

Níže vidíte příklad přiřazení role čtenáře uživateli pro skupinu prostředků po nasazení šablony.

![Přiřazení role v oboru skupiny prostředků](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>Rozsah skupiny prostředků nebo předplatného

Předchozí šablona není příliš flexibilní. Následující šablona používá parametry a lze ji použít v různých oborech. Následující šablona znázorňuje:

- Postup přiřazení role uživateli, skupině nebo aplikaci v oboru skupiny prostředků nebo předplatném
- Jak zadat role vlastníka, přispěvatele a čtenáře jako parametr

Chcete-li použít šablonu, je nutné zadat následující vstupy:

- ID uživatele, skupiny, spravované identity nebo aplikace, ke které se role přiřadí
- Jedinečné ID, které se bude používat pro přiřazení role, nebo můžete použít výchozí ID.

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

> [!NOTE]
> Tato šablona není idempotentní, pokud není stejná `roleNameGuid` hodnota zadána jako parametr pro každé nasazení šablony. Pokud se nezadá žádný `roleNameGuid` , ve výchozím nastavení se v každém nasazení vygeneruje nový identifikátor GUID a další nasazení se nezdaří s `Conflict: RoleAssignmentExists` chybou.

Rozsah přiřazení role je určen z úrovně nasazení. Tady jsou příklady [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) Commands, jak spustit nasazení v oboru skupiny prostředků.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Tady jsou příklady [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) a [AZ Deployment sub Create](/cli/azure/deployment/sub#az_deployment_sub_create) Commands, jak spustit nasazení v oboru předplatného a zadat umístění.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment sub create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>Obor prostředku

Pokud potřebujete přiřadit roli na úrovni prostředku, nastavte `scope` u přiřazení role vlastnost na název prostředku.

Následující šablona znázorňuje:

- Vytvoření nového účtu úložiště
- Přiřazení role uživateli, skupině nebo aplikaci v oboru účtu úložiště
- Jak zadat role vlastníka, přispěvatele a čtenáře jako parametr

Chcete-li použít šablonu, je nutné zadat následující vstupy:

- ID uživatele, skupiny, spravované identity nebo aplikace, ke které se role přiřadí

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "scope": "[concat('Microsoft.Storage/storageAccounts', '/', variables('storageName'))]",
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

K nasazení předchozí šablony použijte příkazy skupiny prostředků. Tady jsou příklady [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) Commands, jak spustit nasazení v oboru prostředků.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Níže vidíte příklad přiřazení role přispěvatele uživateli pro účet úložiště po nasazení šablony.

![Přiřazení role v oboru prostředků](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Nový instanční objekt

Pokud vytvoříte nový instanční objekt a hned se pokusíte přiřadit roli k tomuto instančnímu objektu, toto přiřazení role může v některých případech selhat. Pokud například vytvoříte novou spravovanou identitu a pak se pokusíte přiřadit roli k tomuto instančnímu objektu ve stejné šabloně Azure Resource Manager, přiřazení role může selhat. Důvodem této chyby je nejspíš zpoždění replikace. Instanční objekt se vytvoří v jedné oblasti. přiřazení role se ale může vyskytnout v jiné oblasti, která ještě nereplikoval instanční objekt.

Pro vyřešení tohoto scénáře byste měli nastavit `principalType` vlastnost na `ServicePrincipal` při vytváření přiřazení role. Musíte také nastavit `apiVersion` přiřazení role na `2018-09-01-preview` nebo vyšší.

Následující šablona znázorňuje:

- Postup vytvoření nového objektu spravované služby identity
- Jak zadat `principalType`
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
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Tady jsou příklady [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) Commands, jak spustit nasazení v oboru skupiny prostředků.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Následuje příklad přiřazení role přispěvatele k novému instančnímu objektu služby spravované identity po nasazení šablony.

![Přiřazení role nového objektu spravované služby identity](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: vytvoření a nasazení šablon ARM pomocí Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Vysvětlení struktury a syntaxe šablon ARM](../azure-resource-manager/templates/template-syntax.md)
- [Vytvoření skupin prostředků a prostředků na úrovni předplatného](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Šablony Azure pro rychlý start](https://azure.microsoft.com/resources/templates/?term=rbac)
