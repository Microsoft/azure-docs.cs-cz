---
title: Vytvoření skupiny prostředků a prostředků v předplatném – šablona Azure Resource Manager
description: Popisuje postup vytvoření skupiny prostředků v Azure Resource Manager šabloně. Také ukazuje, jak nasadit prostředky v oboru předplatného Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: tomfitz
ms.openlocfilehash: 37f2b04a62d94cce42b095540380460c38bc5b79
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772939"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Vytvoření skupin prostředků a prostředků na úrovni předplatného

Prostředky Azure se obvykle nasazují do skupiny prostředků ve vašem předplatném Azure. Můžete ale také vytvořit skupiny prostředků Azure a vytvořit prostředky Azure na úrovni předplatného. Pokud chcete nasadit šablony na úrovni předplatného, použijte Azure CLI a Azure PowerShell. Azure Portal nepodporuje nasazení na úrovni předplatného.

Pokud chcete vytvořit skupinu prostředků v Azure Resource Manager šabloně, definujte prostředek [Microsoft. Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) s názvem a umístěním pro skupinu prostředků. Můžete vytvořit skupinu prostředků a nasadit prostředky do této skupiny prostředků ve stejné šabloně. Mezi prostředky, které můžete nasadit na úrovni předplatného, patří: [Zásady](../governance/policy/overview.md)a [řízení přístupu na základě role](../role-based-access-control/overview.md).

## <a name="deployment-considerations"></a>Aspekty nasazování

Nasazení na úrovni předplatného se liší od nasazení skupiny prostředků v následujících aspektech:

### <a name="schema-and-commands"></a>Schéma a příkazy

Schéma a příkazy, které používáte pro nasazení na úrovni předplatného, se liší od nasazení skupin prostředků. 

Pro schéma použijte `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`.

Pro příkaz Azure CLI Deployment použijte [AZ Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). Například následující příkaz rozhraní příkazového řádku nasadí šablonu pro vytvoření skupiny prostředků:

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Pro příkaz nasazení prostředí PowerShell použijte rutinu [New-AzDeployment](/powershell/module/az.resources/new-azdeployment). Například následující příkaz prostředí PowerShell nasadí šablonu pro vytvoření skupiny prostředků:

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

### <a name="deployment-name-and-location"></a>Název a umístění nasazení

Při nasazování do svého předplatného musíte zadat umístění pro nasazení. Můžete také zadat název nasazení. Pokud název nasazení nezadáte, použije se jako název nasazení název šablony. Například nasazení šablony s názvem **azuredeploy. JSON** vytvoří výchozí název nasazení **azuredeploy**.

Umístění nasazení na úrovni předplatného je neměnné. Nasazení nelze vytvořit v jednom umístění, pokud existuje existující nasazení se stejným názvem, ale s jiným umístěním. Pokud se zobrazí kód `InvalidDeploymentLocation`chyby, použijte jiný název nebo stejné umístění jako předchozí nasazení pro tento název.

### <a name="use-template-functions"></a>Použití funkcí šablon

U nasazení na úrovni předplatného se při používání funkcí šablon vyskytly důležité předpoklady:

* Funkce [Resource ()](resource-group-template-functions-resource.md#resourcegroup) **není podporována.**
* Funkce [ResourceID ()](resource-group-template-functions-resource.md#resourceid) je podporována. Použijte ho k získání ID prostředku pro prostředky, které se používají v nasazeních na úrovni předplatného. Například Získejte ID prostředku pro definici zásady s`resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* Funkce [Reference ()](resource-group-template-functions-resource.md#reference) a [list ()](resource-group-template-functions-resource.md#list) jsou podporovány.

## <a name="create-resource-groups"></a>Vytvoření skupin prostředků

Následující šablona vytvoří prázdnou skupinu prostředků.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Schéma šablony lze nalézt [zde](/azure/templates/microsoft.resources/allversions). Podobné šablony najdete na [GitHubu](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).

## <a name="create-multiple-resource-groups"></a>Vytvoření více skupin prostředků

Pomocí [elementu Copy](resource-group-create-multiple.md) se skupinami prostředků vytvořte více než jednu skupinu prostředků. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgNamePrefix": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "instanceCount": {
            "type": "int"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
            "copy": {
                "name": "rgCopy",
                "count": "[parameters('instanceCount')]"
            },
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Informace o iteraci prostředků najdete v tématu [nasazení více než jedné instance prostředku nebo vlastnosti v šablonách Azure Resource Manager](./resource-group-create-multiple.md)a [v kurzu: Vytvořte více instancí prostředků pomocí šablon](./resource-manager-tutorial-create-multiple-instances.md)správce prostředků.

## <a name="create-resource-group-and-deploy-resources"></a>Vytvoření skupiny prostředků a nasazení prostředků

Pokud chcete vytvořit skupinu prostředků a nasadit do ní prostředky, použijte vnořenou šablonu. Vnořená Šablona definuje prostředky pro nasazení do skupiny prostředků. Nastavte vnořenou šablonu jako závislou na skupině prostředků, abyste před nasazením prostředků zajistili, že skupina prostředků existuje.

Následující příklad vytvoří skupinu prostředků a nasadí účet úložiště do skupiny prostředků.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

## <a name="create-policies"></a>Vytvoření zásad

### <a name="assign-policy"></a>Přiřadit zásady

Následující příklad přiřadí existující definici zásady k předplatnému. Pokud zásady přebírají parametry, poskytněte je jako objekt. Pokud zásada nepřijímá parametry, použijte výchozí prázdný objekt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "policyDefinitionID": {
            "type": "string"
        },
        "policyName": {
            "type": "string"
        },
        "policyParameters": {
            "type": "object",
            "defaultValue": {}
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```

Pokud chcete tuto šablonu nasadit pomocí Azure CLI, použijte:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Pokud chcete tuto šablonu nasadit pomocí PowerShellu, použijte:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Definování a přiřazení zásad

Můžete [definovat](../governance/policy/concepts/definition-structure.md) a přiřadit zásady ve stejné šabloně.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
            "properties": {
                "policyType": "Custom",
                "parameters": {},
                "policyRule": {
                    "if": {
                        "field": "location",
                        "equals": "northeurope"
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2018-05-01",
            "dependsOn": [
                "locationpolicy"
            ],
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
            }
        }
    ]
}
```

Pokud chcete ve svém předplatném vytvořit definici zásady a použít ji v předplatném, použijte následující příkaz CLI:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Pokud chcete tuto šablonu nasadit pomocí PowerShellu, použijte:

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="next-steps"></a>Další postup

* Další informace o přiřazování rolí najdete v tématu [Správa přístupu k prostředkům Azure pomocí šablon RBAC a Azure Resource Manager](../role-based-access-control/role-assignments-template.md).
* Příklad nasazení nastavení pracovního prostoru pro Azure Security Center najdete v tématu [deployASCwithWorkspaceSettings. JSON](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Další informace o vytváření šablon Azure Resource Manager najdete v tématu [vytváření šablon](resource-group-authoring-templates.md). 
* Seznam dostupných funkcí v šabloně najdete v tématu [funkce šablon](resource-group-template-functions.md).
