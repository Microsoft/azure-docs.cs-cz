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
ms.date: 01/15/2018
ms.author: tomfitz
ms.openlocfilehash: 542993d803282bbf62e2e401cab1968a656a8971
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352270"
---
# <a name="create-resource-groups-and-resources-for-an-azure-subscription"></a>Vytvoření skupiny prostředků a prostředků pro předplatné Azure

Obvykle nasazování prostředků do skupiny prostředků ve vašem předplatném Azure. Však můžete použít nasazení úroveň předplatného k vytvoření skupiny prostředků a prostředky, které se vztahují rámci vašeho předplatného.

Chcete-li vytvořit skupinu prostředků v šabloně Azure Resource Manageru, definujte **Microsoft.Resources/resourceGroups** prostředků s názvem a umístění pro skupinu prostředků. Můžete vytvořit skupinu prostředků a nasazování prostředků do této skupiny prostředků ve stejné šabloně.

[Zásady](../azure-policy/azure-policy-introduction.md), [řízení přístupu na základě rolí](../role-based-access-control/overview.md), a [Azure Security Center](../security-center/security-center-intro.md) jsou služby, které můžete chtít použít na úrovni předplatného, nikoli na úrovni skupiny prostředků.

Tento článek popisuje, jak vytvářet skupiny prostředků a jak vytvořit prostředky, které se vztahují napříč předplatným. Používá Azure CLI a Powershellu k nasazení šablony. Na portálu nelze použít k nasazení šablony, protože rozhraní portálu nasadí do skupiny prostředků, ne předplatného Azure.

## <a name="schema-and-commands"></a>Schéma a příkazy

Schéma a příkazy, které používáte pro nasazení na úrovni předplatného se liší od nasazení skupiny prostředků. 

Pro schéma, použijte `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`.

Pro nasazení příkazu Azure CLI, použijte [az nasazení vytvořit](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create).

Nasazení příkazového prostředí PowerShell, použijte [New-AzureRmDeployment](/powershell/module/azurerm.resources/new-azurermdeployment).

## <a name="name-and-location"></a>Název a umístění

Při nasazování do vašeho předplatného, je nutné zadat umístění pro nasazení. Můžete také zadat název pro nasazení. Pokud nechcete zadat název pro nasazení, název šablony se používá jako název nasazení. Například nasazení šablonu s názvem **azuredeploy.json** vytvoří výchozí název nasazení o **azuredeploy**.

Umístění nasazení na úrovni předplatného je neměnný. Nejde vytvořit nasazení v jednom místě po stávajícího nasazení se stejným názvem, ale jiné umístění. Pokud se zobrazí kód chyby: `InvalidDeploymentLocation`, použijte jiný název nebo na stejném umístění jako předchozí nasazení pro tento název.

## <a name="using-template-functions"></a>Pomocí šablony funkce

Pro předplatné úrovně nasazení existují některé důležité informace při použití funkce šablon:

* [ResourceGroup()](resource-group-template-functions-resource.md#resourcegroup) funkce je **není** podporována.
* [ResourceId()](resource-group-template-functions-resource.md#resourceid) funkce není podporována. Použijte ho k získání ID prostředku pro prostředky, které se používají na úrovni nasazení předplatných. Třeba získáte ID prostředku definice zásady pomocí `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* [Reference()](resource-group-template-functions-resource.md#reference) a [list()](resource-group-template-functions-resource.md#list) funkce jsou podporovány.

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Následující příklad vytvoří prázdné skupiny prostředků.

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

Pokud chcete nasadit tuto šablonu pomocí Azure CLI, použijte:

```azurecli-interactive
az deployment create \
  -n demoEmptyRG \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoRG rgLocation=northcentralus
```

Pokud chcete nasadit tuto šablonu pomocí Powershellu, použijte:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name demoEmptyRG `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demogroup `
  -rgLocation northcentralus
```

## <a name="create-several-resource-groups"></a>Vytvořit několik skupin prostředků

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

Pokud chcete nasadit tuto šablonu pomocí rozhraní příkazového řádku Azure a vytvořit tři skupiny prostředků, použijte:

```azurecli-interactive
az deployment create \
  -n demoCopyRG \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/copyRG.json \
  --parameters rgNamePrefix=demoRG rgLocation=northcentralus instanceCount=3
```

Pokud chcete nasadit tuto šablonu pomocí Powershellu, použijte:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name demoCopyRG `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/copyRG.json `
  -rgNamePrefix demogroup `
  -rgLocation northcentralus `
  -instanceCount 3
```

## <a name="create-resource-group-and-deploy-resource"></a>Vytvořte skupinu prostředků a nasazení prostředků

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

Pokud chcete nasadit tuto šablonu pomocí Azure CLI, použijte:

```azurecli-interactive
az deployment create \
  -n demoRGStorage \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/newRGWithStorage.json \
  --parameters rgName=rgStorage rgLocation=northcentralus storagePrefix=storage
```

Pokud chcete nasadit tuto šablonu pomocí Powershellu, použijte:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name demoRGStorage `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/newRGWithStorage.json `
  -rgName rgStorage `
  -rgLocation northcentralus `
  -storagePrefix storage
```

## <a name="assign-policy"></a>Přiřadit zásady

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

```azurecli-interactive
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

Pokud chcete nasadit tuto šablonu pomocí Powershellu, použijte:

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit resource location matches resource group location' }

New-AzureRmDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName auditRGLocation
```

Použít integrované zásady ke svému předplatnému Azure, použijte následující příkazy rozhraní příkazového řádku Azure:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Pokud chcete nasadit tuto šablonu pomocí Powershellu, použijte:

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzureRmDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

## <a name="define-and-assign-policy"></a>Definování a přiřazení zásad

Je možné [definovat](../azure-policy/policy-definition.md) a přiřazení zásad ve stejné šabloně.

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

```azurecli-interactive
az deployment create \
  -n definePolicy \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Pokud chcete nasadit tuto šablonu pomocí Powershellu, použijte:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name definePolicy `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="assign-role-at-subscription"></a>Přiřazení role na předplatné

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

```azurecli-interactive
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  -n demoRole \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

Pokud chcete nasadit tuto šablonu pomocí Powershellu, použijte:

```azurepowershell-interactive
$role = Get-AzureRmRoleDefinition -Name Contributor

$adgroup = Get-AzureRmADGroup -DisplayName demogroup

New-AzureRmDeployment `
  -Name demoRole `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id
```

## <a name="assign-role-at-scope"></a>Přiřazení role v oboru

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

```azurecli-interactive
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  -n demoRole \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json \
  --parameters principalId=$principalid roleDefinitionId=$role rgName demoRg
```

Pokud chcete nasadit tuto šablonu pomocí Powershellu, použijte:

```azurepowershell-interactive
$role = Get-AzureRmRoleDefinition -Name Contributor

$adgroup = Get-AzureRmADGroup -DisplayName demogroup

New-AzureRmDeployment `
  -Name demoRole `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id `
  -rgName demoRg
```

## <a name="next-steps"></a>Další postup
* Příklad nasazení nastavení pracovního prostoru pro Azure Security Center najdete v tématu [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Další informace o vytváření šablon Azure Resource Manageru, najdete v článku [vytváření šablon](resource-group-authoring-templates.md). 
* Seznam dostupných funkcí v šabloně najdete v tématu [šablony funkce](resource-group-template-functions.md).
