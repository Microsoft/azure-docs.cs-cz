---
title: Přidání přiřazení rolí Azure pomocí šablon Azure Resource Manager – Azure RBAC
description: Naučte se, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí šablon Azure Resource Manager a řízení přístupu na základě role v Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 400f0b1b55136f133c9ad01fd0ba4b5dbc5e6bcb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612740"
---
# <a name="add-azure-role-assignments-using-azure-resource-manager-templates"></a>Přidání přiřazení rolí Azure pomocí šablon Azure Resource Manager

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Kromě používání Azure PowerShell nebo rozhraní příkazového řádku Azure CLI můžete role přiřadit pomocí [šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Šablony mohou být užitečné, pokud potřebujete nasadit prostředky konzistentně a opakovaně. Tento článek popisuje, jak přiřadit role pomocí šablon.

## <a name="get-object-ids"></a>Získat ID objektů

Chcete-li přiřadit roli, je nutné zadat ID uživatele, skupiny nebo aplikace, ke které chcete přiřadit roli. ID má formát: `11111111-1111-1111-1111-111111111111` . ID můžete získat pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure.

### <a name="user"></a>Uživatel

Pokud chcete získat ID uživatele, můžete použít příkazy [Get-AzADUser](/powershell/module/az.resources/get-azaduser) nebo [AZ AD User show](/cli/azure/ad/user#az-ad-user-show) .

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Group (Skupina)

Pokud chcete získat ID skupiny, můžete použít příkazy [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) nebo [AZ AD Group show](/cli/azure/ad/group#az-ad-group-show) .

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

Pokud chcete získat ID instančního objektu (identity používaného aplikací), můžete použít příkazy [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) nebo [AZ AD SP list](/cli/azure/ad/sp#az-ad-sp-list) . U instančního objektu použijte ID objektu, **nikoli** ID aplikace.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Přidat přiřazení role

Když v Azure RBAC udělíte přístup, přidáte přiřazení role.

### <a name="resource-group-scope-without-parameters"></a>Obor skupiny prostředků (bez parametrů)

Následující šablona ukazuje základní způsob, jak přidat přiřazení role. Některé hodnoty jsou zadány v rámci šablony. Následující šablona znázorňuje:

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

Tady jsou příklady [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) Commands, jak spustit nasazení ve skupině prostředků s názvem example.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
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

Rozsah přiřazení role je určen z úrovně nasazení. Tady jsou příklady [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) Commands, jak spustit nasazení v oboru skupiny prostředků.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Tady jsou příklady [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) a [AZ Deployment Create](/cli/azure/deployment#az-deployment-create) Command, jak spustit nasazení v oboru předplatného a zadat umístění.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>Obor prostředku

Pokud potřebujete přidat přiřazení role na úrovni prostředku, formát přiřazení role se liší. Zadejte obor názvů poskytovatele prostředků a typ prostředku, ke kterému chcete přiřadit roli. Do názvu přiřazení role zadáte také název prostředku.

Pro typ a název přiřazení role použijte následující formát:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Následující šablona znázorňuje:

- Vytvoření nového účtu úložiště
- Přiřazení role uživateli, skupině nebo aplikaci v oboru účtu úložiště
- Jak zadat role vlastníka, přispěvatele a čtenáře jako parametr

Chcete-li použít šablonu, je nutné zadat následující vstupy:

- ID uživatele, skupiny, spravované identity nebo aplikace, ke které se role přiřadí

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
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(variables('storageName'))))]",
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
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Níže vidíte příklad přiřazení role přispěvatele uživateli pro účet úložiště po nasazení šablony.

![Přiřazení role v oboru prostředků](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Nový instanční objekt

Pokud vytvoříte nový instanční objekt a hned se pokusíte přiřadit roli k tomuto instančnímu objektu, toto přiřazení role může v některých případech selhat. Pokud například vytvoříte novou spravovanou identitu a pak se pokusíte přiřadit roli k tomuto instančnímu objektu ve stejné šabloně Azure Resource Manager, přiřazení role může selhat. Důvodem této chyby je nejspíš zpoždění replikace. Instanční objekt se vytvoří v jedné oblasti. přiřazení role se ale může vyskytnout v jiné oblasti, která ještě nereplikoval instanční objekt. Pro vyřešení tohoto scénáře byste měli nastavit `principalType` vlastnost na `ServicePrincipal` při vytváření přiřazení role.

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

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

Když ve službě Azure RBAC odeberete přístup k prostředku Azure, odeberete přiřazení role. Neexistuje způsob, jak odebrat přiřazení role pomocí šablony. Chcete-li odebrat přiřazení role, je nutné použít jiné nástroje, jako například:

- [Azure Portal](role-assignments-portal.md#remove-a-role-assignment)
- [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)
- [Azure CLI](role-assignments-cli.md#remove-role-assignment)
- [REST API](role-assignments-rest.md#remove-a-role-assignment)

## <a name="next-steps"></a>Další kroky

- [Rychlý start: Vytvoření a nasazení šablony Azure Resource Manageru pomocí portálu Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Pochopení struktury a syntaxe šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
- [Vytvoření skupin prostředků a prostředků na úrovni předplatného](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Šablony Azure pro rychlý start](https://azure.microsoft.com/resources/templates/?term=rbac)
