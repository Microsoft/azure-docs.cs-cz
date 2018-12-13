---
title: Nasazení prostředků do předplatného Azure | Dokumentace Microsoftu
description: Popisuje, jak vytvořit šablonu Azure Resource Manageru, který se nasazuje prostředky v oboru předplatného.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2018
ms.author: tomfitz
ms.openlocfilehash: 9a9fe16f562805f1bfd6f51af063531f34ffdde2
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53308494"
---
# <a name="deploy-resources-to-an-azure-subscription"></a>Nasazení prostředků k předplatnému Azure

Obvykle nasazování prostředků do skupiny prostředků ve vašem předplatném Azure. Nicméně některé prostředky je možné nasadit na úrovni předplatného Azure. Tyto prostředky používat napříč vašeho předplatného. [Zásady](../azure-policy/azure-policy-introduction.md), [řízení přístupu na základě rolí](../role-based-access-control/overview.md), a [Azure Security Center](../security-center/security-center-intro.md) jsou služby, které můžete chtít použít na úrovni předplatného, nikoli na úrovni skupiny prostředků.

Tento článek používá Azure CLI a Powershellu k nasazení šablony. Na portálu nelze použít k nasazení šablony, protože rozhraní portálu nasadí do skupiny prostředků, ne předplatného Azure.

## <a name="name-and-location-for-deployment"></a>Název a umístění pro nasazení

Při nasazování do vašeho předplatného, je nutné zadat umístění pro nasazení. Můžete také zadat název pro nasazení. Pokud nechcete zadat název pro nasazení, název šablony se používá jako název nasazení. Například nasazení šablonu s názvem **azuredeploy.json** vytvoří výchozí název nasazení o **azuredeploy**.

Umístění nasazení na úrovni předplatného je neměnný. Nejde vytvořit nasazení v jednom místě po stávajícího nasazení se stejným názvem, ale jiné umístění. Pokud se zobrazí kód chyby: `InvalidDeploymentLocation`, použijte jiný název nebo na stejném umístění jako předchozí nasazení pro tento název.

## <a name="using-template-functions"></a>Pomocí šablony funkce

Pro předplatné úrovně nasazení existují některé důležité informace při použití funkce šablon:

* [ResourceGroup()](resource-group-template-functions-resource.md#resourcegroup) funkce je **není** podporována.
* [ResourceId()](resource-group-template-functions-resource.md#resourceid) funkce není podporována. Použijte ho k získání ID prostředku pro prostředky, které se používají na úrovni nasazení předplatných. Třeba získáte ID prostředku definice zásady pomocí `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* [Reference()](resource-group-template-functions-resource.md#reference) a [list()](resource-group-template-functions-resource.md#list) funkce jsou podporovány.

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

Použít integrované zásady ke svému předplatnému Azure, použijte následující příkazy rozhraní příkazového řádku Azure. V tomto příkladu zásady nemá parametry

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

Použít integrované zásady ke svému předplatnému Azure, použijte následující příkazy rozhraní příkazového řádku Azure. V tomto příkladu zásady mají parametry.

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

Vytvořit definici zásady v rámci vašeho předplatného a použít ho k předplatnému, použijte následující příkaz rozhraní příkazového řádku.

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

## <a name="assign-role"></a>Přiřazení role

Následující příklad přiřadí roli uživatele nebo skupinu.

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

Přiřazení skupiny služby Active Directory k roli pro vaše předplatné, použijte následující příkazy rozhraní příkazového řádku Azure.

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

## <a name="next-steps"></a>Další postup
* Příklad nasazení nastavení pracovního prostoru pro Azure Security Center najdete v tématu [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Pokud chcete vytvořit skupinu prostředků, najdete v článku [vytváření skupin prostředků v šablonách Azure Resource Manageru](create-resource-group-in-template.md).
* Další informace o vytváření šablon Azure Resource Manageru, najdete v článku [vytváření šablon](resource-group-authoring-templates.md). 
* Seznam dostupných funkcí v šabloně najdete v tématu [šablony funkce](resource-group-template-functions.md).

