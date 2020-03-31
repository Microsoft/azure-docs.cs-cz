---
title: Přidání přiřazení rolí pomocí šablon RBAC a Azure Resource Manager
description: Zjistěte, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí azure role-based řízení přístupu (RBAC) a Azure Resource Manager šablony.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9f817880f938f5d03024e3aacd9b84817a5ac721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138288"
---
# <a name="add-role-assignments-using-azure-rbac-and-azure-resource-manager-templates"></a>Přidání přiřazení rolí pomocí šablon Azure RBAC a Azure Resource Manager

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Kromě použití Azure PowerShellu nebo rozhraní příkazového uživatelského rozhraní Azure můžete přiřadit role pomocí [šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Šablony mohou být užitečné, pokud potřebujete nasadit prostředky konzistentně a opakovaně. Tento článek popisuje, jak přiřadit role pomocí šablon.

## <a name="get-object-ids"></a>Získání ID objektů

Chcete-li přiřadit roli, je třeba zadat ID uživatele, skupiny nebo aplikace, ke které chcete roli přiřadit. ID má formát: `11111111-1111-1111-1111-111111111111`. ID můžete získat pomocí portálu Azure, Azure PowerShell nebo Azure CLI.

### <a name="user"></a>Uživatel

Chcete-li získat ID uživatele, můžete použít [příkazy get-azaduser](/powershell/module/az.resources/get-azaduser) nebo [az ad ad user show.](/cli/azure/ad/user#az-ad-user-show)

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Skupina

Chcete-li získat ID skupiny, můžete použít příkazy [Zobrazit get-AzADGroup](/powershell/module/az.resources/get-azadgroup) nebo [az reklamní sestavu.](/cli/azure/ad/group#az-ad-group-show)

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="application"></a>Aplikace

Chcete-li získat ID instančního objektu (identity používané aplikací), můžete použít příkazy seznamu [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) nebo [az az ad sp.](/cli/azure/ad/sp#az-ad-sp-list) Pro instanční objekt použijte ID objektu a **ne** ID aplikace.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Přidat přiřazení role

V RBAC, chcete-li udělit přístup, přidáte přiřazení role.

### <a name="resource-group-without-parameters"></a>Skupina prostředků (bez parametrů)

Následující šablona ukazuje základní způsob přidání přiřazení role. Některé hodnoty jsou zadány v šabloně. Následující šablona ukazuje:

-  Přiřazení role [Čtenáře](built-in-roles.md#reader) uživateli, skupině nebo aplikaci v oboru skupiny prostředků

Chcete-li šablonu použít, musíte provést následující kroky:

- Vytvoření nového souboru JSON a zkopírování šablony
- Nahraďte `<your-principal-id>` ID uživatele, skupiny nebo aplikace, které chcete přiřadit roli

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

Zde jsou příklady [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [nasazení skupiny az vytvořit](/cli/azure/group/deployment#az-group-deployment-create) příkazy pro spuštění nasazení ve skupině prostředků s názvem ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Následující text ukazuje příklad přiřazení role Čtečka uživateli pro skupinu prostředků po nasazení šablony.

![Přiřazení role v oboru skupiny zdrojů](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription"></a>Skupina prostředků nebo předplatné

Předchozí šablona není příliš flexibilní. Následující šablona používá parametry a lze je použít v různých oborech. Následující šablona ukazuje:

- Jak přiřadit roli uživateli, skupině nebo aplikaci ve skupině prostředků nebo oboru předplatného
- Jak určit role Vlastník, Přispěvatel a Čtenář jako parametr

Chcete-li použít šablonu, musíte zadat následující vstupy:

- ID uživatele, skupiny nebo aplikace pro přiřazení role
- Jedinečné ID, které bude použito pro přiřazení role, nebo můžete použít výchozí ID

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
> Tato šablona není idempotentní, pokud není jako parametr pro každé nasazení šablony poskytnuta stejná `roleNameGuid` hodnota. Pokud `roleNameGuid` není k dispozici, ve výchozím nastavení je při každém nasazení generován nový `Conflict: RoleAssignmentExists` identifikátor GUID a následné nasazení se nezdaří s chybou.

Rozsah přiřazení role je určen z úrovně nasazení. Zde jsou příklady [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [nasazení skupiny az vytvořit](/cli/azure/group/deployment#az-group-deployment-create) příkazy pro spuštění nasazení v oboru skupiny prostředků.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Zde jsou [příklady New-AzDeployment](/powershell/module/az.resources/new-azdeployment) a [az nasazení vytvořit](/cli/azure/deployment#az-deployment-create) příkazy, jak spustit nasazení v oboru předplatného a určit umístění.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource"></a>Prostředek

Pokud potřebujete přidat přiřazení role na úrovni zdroje, formát přiřazení role se liší. Zadáte obor názvů poskytovatele prostředků a typ prostředku prostředku, ke kterým chcete roli přiřadit. Název zdroje zahrnete také do názvu přiřazení role.

Pro typ a název přiřazení role použijte následující formát:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Následující šablona ukazuje:

- Vytvoření nového účtu úložiště
- Jak přiřadit roli uživateli, skupině nebo aplikaci v oboru účtu úložiště
- Jak určit role Vlastník, Přispěvatel a Čtenář jako parametr

Chcete-li použít šablonu, musíte zadat následující vstupy:

- ID uživatele, skupiny nebo aplikace pro přiřazení role

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

Chcete-li nasadit předchozí šablonu, použijte příkazy skupiny prostředků. Zde jsou příklady [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [nasazení skupiny az vytvořit](/cli/azure/group/deployment#az-group-deployment-create) příkazy pro spuštění nasazení v oboru prostředků.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Následující text ukazuje příklad přiřazení role přispěvatele uživateli pro účet úložiště po nasazení šablony.

![Přiřazení role v oboru zdrojů](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Nový instanční objekt

Pokud vytvoříte nový instanční objekt a okamžitě se pokusíte přiřadit roli tomuto instančnímu objektu, může toto přiřazení role v některých případech selhat. Pokud například vytvoříte novou spravovanou identitu a pak se pokusíte přiřadit roli tomuto instančnímu objektu ve stejné šabloně Azure Resource Manager, přiřazení role může selhat. Důvodem této chyby je pravděpodobně zpoždění replikace. Instanční objekt je vytvořen v jedné oblasti; přiřazení role však může dojít v jiné oblasti, která dosud replikovat instanční objekt. Chcete-li tento scénář vyřešit, měli byste nastavit `principalType` vlastnost při `ServicePrincipal` vytváření přiřazení role.

Následující šablona ukazuje:

- Jak vytvořit nový objekt zabezpečení služby spravované identity
- Jak určit`principalType`
- Jak přiřadit roli přispěvatele k tomuto instančnímu objektu v oboru skupiny prostředků

Chcete-li použít šablonu, musíte zadat následující vstupy:

- Základní název spravované identity nebo můžete použít výchozí řetězec

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

Zde jsou příklady [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a [nasazení skupiny az vytvořit](/cli/azure/group/deployment#az-group-deployment-create) příkazy pro spuštění nasazení v oboru skupiny prostředků.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Následující text ukazuje příklad přiřazení role přispěvatele k novému objektu služby spravované identity po nasazení šablony.

![Přiřazení role pro nový objekt zabezpečení spravované služby identity](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Další kroky

- [Rychlý start: Vytvoření a nasazení šablony Azure Resource Manageru pomocí portálu Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Principy struktury a syntaxe šablon Azure Resource Manageru](../azure-resource-manager/templates/template-syntax.md)
- [Vytvoření skupin prostředků a prostředků na úrovni předplatného](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Šablony azure quickstart](https://azure.microsoft.com/resources/templates/?term=rbac)
