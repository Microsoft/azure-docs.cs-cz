---
title: Vytvoření skupiny prostředků a prostředků na předplatné – šablony Azure Resource Manageru
description: Popisuje, jak vytvořit skupinu prostředků v šabloně Azure Resource Manageru. Také ukazuje, jak nasadit prostředky v oboru předplatného Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: bdba294e1ee776d90b93f715e930ec26765abb7f
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343030"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Vytvoření skupiny prostředků a prostředků na úrovni předplatného

Obvykle nasazení prostředků Azure do skupiny prostředků ve vašem předplatném Azure. Ale můžete také vytvářet skupiny prostředků Azure a vytvářet prostředky Azure na úrovni předplatného. K nasazení šablon na úrovni předplatného, použijte rozhraní příkazového řádku Azure a Azure Powershellu. Na webu Azure portal nepodporuje nasazení na úrovni předplatného.

Chcete-li vytvořit skupinu prostředků v šabloně Azure Resource Manageru, definujte [ **Microsoft.Resources/resourceGroups** ](/azure/templates/microsoft.resources/allversions) prostředků s názvem a umístění pro skupinu prostředků. Můžete vytvořit skupinu prostředků a nasazování prostředků do této skupiny prostředků ve stejné šabloně. Prostředky, které můžete nasadit na úrovni předplatného patří: [Zásady](../governance/policy/overview.md), a [řízení přístupu na základě rolí](../role-based-access-control/overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-considerations"></a>Aspekty nasazování

Nasazení na úrovni předplatného se liší od nasazení skupiny prostředků na následující aspekty:

### <a name="schema-and-commands"></a>Schéma a příkazy

Schéma a příkazy, které používáte pro nasazení na úrovni předplatného se liší od nasazení skupiny prostředků. 

Pro schéma, použijte `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`.

Pro nasazení příkazu Azure CLI, použijte [az nasazení vytvořit](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). Například následující příkaz rozhraní příkazového řádku nasadí šablonu pro vytvoření skupiny prostředků:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Nasazení příkazového prostředí PowerShell, použijte [New-AzDeployment](/powershell/module/az.resources/new-azdeployment). Například následující příkaz Powershellu nasadí šablonu pro vytvoření skupiny prostředků:

```azurepowershell
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

### <a name="deployment-name-and-location"></a>Název nasazení a umístění

Při nasazování do vašeho předplatného, je nutné zadat umístění pro nasazení. Můžete také zadat název pro nasazení. Pokud nechcete zadat název pro nasazení, název šablony se používá jako název nasazení. Například nasazení šablonu s názvem **azuredeploy.json** vytvoří výchozí název nasazení o **azuredeploy**.

Umístění nasazení na úrovni předplatného je neměnný. Nejde vytvořit nasazení v jednom místě po stávajícího nasazení se stejným názvem, ale jiné umístění. Pokud se zobrazí kód chyby: `InvalidDeploymentLocation`, použijte jiný název nebo na stejném umístění jako předchozí nasazení pro tento název.

### <a name="use-template-functions"></a>Použití šablony funkcí

Pro nasazení na úrovni předplatného existují některé důležité informace při použití funkce šablon:

* [ResourceGroup()](resource-group-template-functions-resource.md#resourcegroup) funkce je **není** podporována.
* [ResourceId()](resource-group-template-functions-resource.md#resourceid) funkce není podporována. Použijte ho k získání ID prostředku pro prostředky, které se používají na úrovni nasazení předplatných. Třeba získáte ID prostředku definice zásady pomocí `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* [Reference()](resource-group-template-functions-resource.md#reference) a [list()](resource-group-template-functions-resource.md#list) funkce jsou podporovány.

## <a name="create-resource-groups"></a>Vytvoření skupiny prostředků

Následující šablony vytvoří prázdné skupiny prostředků.

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

Schéma šablony lze nalézt v [tady](/azure/templates/microsoft.resources/allversions). Podobně jako šablony lze nalézt v [Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).

## <a name="create-multiple-resource-groups"></a>Vytvořte více skupin prostředků

Použití [elementu copy](resource-group-create-multiple.md) se skupinami prostředků k vytvoření více než jednu skupinu prostředků. 

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

Informace o prostředku iterací, naleznete v tématu [nasadit více než jednu instanci zdroje nebo vlastnosti v šablonách Azure Resource Manageru](./resource-group-create-multiple.md), a [kurzu: Vytvoření víc instancí prostředků pomocí šablon Resource Manageru](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="create-resource-group-and-deploy-resources"></a>Vytvořte skupinu prostředků a nasazení prostředků

Jak vytvořit skupinu prostředků a nasazení prostředků do ní, vnořené šablony. Vnořené šablony definuje prostředky k nasazení do skupiny prostředků. Nastavení vnořené šablony jako závisí na skupině prostředků, abyste měli jistotu, že skupina prostředků existuje před nasazením prostředky.

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

Následující příklad přiřadí existující definici zásady k předplatnému. Pokud zásady parametry, zadejte je jako objekt. Pokud zásady nepřijímá parametry, použijte výchozí prázdný objekt.

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

Použít integrované zásady ke svému předplatnému Azure, použijte následující příkazy rozhraní příkazového řádku Azure:

```azurecli
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

Pokud chcete nasadit tuto šablonu pomocí Powershellu, použijte:

```azurepowershell
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit resource location matches resource group location' }

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName auditRGLocation
```

Použít integrované zásady ke svému předplatnému Azure, použijte následující příkazy rozhraní příkazového řádku Azure:

```azurecli
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Pokud chcete nasadit tuto šablonu pomocí Powershellu, použijte:

```azurepowershell
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

Je možné [definovat](../governance/policy/concepts/definition-structure.md) a přiřazení zásad ve stejné šabloně.

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

Vytvořit definici zásady v rámci vašeho předplatného a použít ho k předplatnému, použijte následující příkaz rozhraní příkazového řádku:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Pokud chcete nasadit tuto šablonu pomocí Powershellu, použijte:

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="create-roles"></a>Vytvoření rolí

### <a name="assign-role-at-subscription"></a>Přiřazení role na předplatné

Následující příklad přiřadí roli uživatele nebo skupiny pro předplatné. V tomto příkladu nezadáte oboru přiřazení, protože obor je automaticky nastaven na předplatné.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "[guid(parameters('principalId'), deployment().name)]",
            "apiVersion": "2017-09-01",
            "properties": {
                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Přiřazení skupiny služby Active Directory k roli pro vaše předplatné, použijte následující příkazy rozhraní příkazového řádku Azure:

```azurecli
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

Pokud chcete nasadit tuto šablonu pomocí Powershellu, použijte:

```azurepowershell
$role = Get-AzRoleDefinition -Name Contributor

$adgroup = Get-AzADGroup -DisplayName demogroup

New-AzDeployment `
  -Name demoRole `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id
```

### <a name="assign-role-at-scope"></a>Přiřazení role v oboru

Následující šablony úrovně předplatného přiřadí roli uživatele nebo skupinu, jejímž oborem jsou skupiny prostředků v rámci předplatného. Rozsah musí být nižší než úroveň nasazení. Můžete nasadit na předplatné a zadejte přiřazení role omezená na skupinu prostředků v rámci tohoto předplatného. Však nelze nasadit do skupiny prostředků a zadejte rozsah přiřazení rolí k předplatnému.

Přiřazení role v oboru, použijte vnořené nasazení. Všimněte si, že je zadaný název skupiny prostředků, ve vlastnostech prostředku nasazení a ve vlastnosti oboru přiřazení role.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        },
        "rgName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "assignRole",
            "resourceGroup": "[parameters('rgName')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Authorization/roleAssignments",
                            "name": "[guid(parameters('principalId'), deployment().name)]",
                            "apiVersion": "2017-09-01",
                            "properties": {
                                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                                "principalId": "[parameters('principalId')]",
                                "scope": "[concat(subscription().id, '/resourceGroups/', parameters('rgName'))]"
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

Přiřazení skupiny služby Active Directory k roli pro vaše předplatné, použijte následující příkazy rozhraní příkazového řádku Azure:

```azurecli
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json \
  --parameters principalId=$principalid roleDefinitionId=$role rgName demoRg
```

Pokud chcete nasadit tuto šablonu pomocí Powershellu, použijte:

```azurepowershell
$role = Get-AzRoleDefinition -Name Contributor

$adgroup = Get-AzADGroup -DisplayName demogroup

New-AzDeployment `
  -Name demoRole `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id `
  -rgName demoRg
```

## <a name="next-steps"></a>Další postup

* Příklad nasazení nastavení pracovního prostoru pro Azure Security Center najdete v tématu [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Další informace o vytváření šablon Azure Resource Manageru, najdete v článku [vytváření šablon](resource-group-authoring-templates.md). 
* Seznam dostupných funkcí v šabloně najdete v tématu [šablony funkce](resource-group-template-functions.md).
