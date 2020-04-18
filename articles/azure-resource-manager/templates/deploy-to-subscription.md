---
title: Nasazení prostředků do předplatného
description: Popisuje, jak vytvořit skupinu prostředků v šabloně Azure Resource Manager. Také ukazuje, jak nasadit prostředky v oboru předplatného Azure.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 6bec29a07653ff5ad7d1e2f8317246049e127c8c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605008"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Vytvoření skupin prostředků a prostředků na úrovni předplatného

Chcete-li zjednodušit správu prostředků ve vašem předplatném Azure, můžete definovat a přiřadit [zásady](../../governance/policy/overview.md) nebo [ovládací prvky přístupu založené na rolích](../../role-based-access-control/overview.md) v rámci předplatného. Pomocí šablon na úrovni předplatného deklarativně použijete zásady a přiřadíte role k předplatnému. Můžete také vytvořit skupiny prostředků a nasadit prostředky.

Pokud chcete nasadit šablony na úrovni předplatného, použijte rozhraní API Azure, PowerShell nebo REST API. Portál Azure nepodporuje nasazení na úrovni předplatného.

## <a name="supported-resources"></a>Podporované prostředky

Na úrovni předplatného můžete nasadit následující typy prostředků:

* [Rozpočty](/azure/templates/microsoft.consumption/budgets)
* [nasazení](/azure/templates/microsoft.resources/deployments) – pro vnořené šablony, které se nasazují do skupin prostředků.
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [náprava](/azure/templates/microsoft.policyinsights/2019-07-01/remediations)
* [skupiny prostředků](/azure/templates/microsoft.resources/resourcegroups)
* [roleÚkoly](/azure/templates/microsoft.authorization/roleassignments)
* [definice rolí](/azure/templates/microsoft.authorization/roledefinitions)
* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [Tagy](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>Schéma

Schéma, které používáte pro nasazení na úrovni předplatného, se liší od schématu pro nasazení skupinprostředků.

Pro šablony použijte:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Schéma pro soubor parametrů je stejné pro všechny obory nasazení. Pro soubory parametrů použijte:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Příkazy nasazení

Příkazy pro nasazení na úrovni předplatného se liší od příkazů pro nasazení skupiny prostředků.

Pro Azure CLI použijte [az nasazení sub create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create). Následující příklad nasadí šablonu k vytvoření skupiny prostředků:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Pro příkaz nasazení prostředí PowerShell použijte [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) nebo **New-AzSubscriptionDeployment**. Následující příklad nasadí šablonu k vytvoření skupiny prostředků:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

Pro rozhraní REST API použijte [nasazení – vytvořit v oboru předplatného](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Umístění a název nasazení

Pro nasazení na úrovni předplatného je nutné zadat umístění pro nasazení. Umístění nasazení je oddělené od umístění prostředků, které nasazujete. Umístění nasazení určuje, kam se mají ukládat data nasazení.

Můžete zadat název pro nasazení nebo použít výchozí název nasazení. Výchozí název je název souboru šablony. Například nasazení šablony s názvem **azuredeploy.json** vytvoří výchozí název nasazení **azuredeploy**.

Pro každý název nasazení umístění je neměnné. Nasazení nelze vytvořit na jednom místě, pokud existuje existující nasazení se stejným názvem v jiném umístění. Pokud se zobrazí `InvalidDeploymentLocation`kód chyby , použijte jiný název nebo stejné umístění jako předchozí nasazení pro tento název.

## <a name="use-template-functions"></a>Použití funkcí šablony

Pro nasazení na úrovni předplatného existují některé důležité důležité informace při použití funkcí šablony:

* Funkce [resourceGroup()](template-functions-resource.md#resourcegroup) **není** podporována.
* [Funkce reference()](template-functions-resource.md#reference) a [list()](template-functions-resource.md#list) jsou podporovány.
* Pomocí funkce [subscriptionResourceId()](template-functions-resource.md#subscriptionresourceid) získat ID prostředku pro prostředky, které jsou nasazeny na úrovni předplatného.

  Chcete-li například získat ID prostředku pro definici zásad, použijte:
  
  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  Vrácené ID prostředku má následující formát:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-resource-groups"></a>Vytvořit skupiny prostředků

Pokud chcete vytvořit skupinu prostředků v šabloně Správce prostředků Azure, definujte prostředek [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) s názvem a umístěním pro skupinu prostředků. Skupinu prostředků můžete vytvořit a nasadit prostředky do této skupiny prostředků ve stejné šabloně.

Následující šablona vytvoří prázdnou skupinu prostředků.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Pomocí [elementu copy](copy-resources.md) se skupinami prostředků vytvořte více než jednu skupinu prostředků.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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

Informace o iteraci prostředků najdete [v tématu Nasazení více než jedné instance prostředku v šablonách Správce prostředků Azure](./copy-resources.md)a [kurz: Vytvoření více instancí prostředků pomocí šablon Správce prostředků](./template-tutorial-create-multiple-instances.md).

## <a name="resource-group-and-resources"></a>Skupina prostředků a prostředky

Chcete-li vytvořit skupinu prostředků a nasadit do ní prostředky, použijte vnořenou šablonu. Vnořená šablona definuje prostředky, které mají být nasazeny do skupiny prostředků. Nastavte vnořenou šablonu jako závislou na skupině prostředků, abyste se ujistili, že skupina prostředků existuje před nasazením prostředků.

Následující příklad vytvoří skupinu prostředků a nasadí účet úložiště do skupiny prostředků.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
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

### <a name="assign-policy"></a>Přiřadit zásadu

Následující příklad přiřadí existující definici zásady k odběru. Pokud zásada přebírá parametry, zadejte je jako objekt. Pokud zásada nepřevezme parametry, použijte výchozí prázdný objekt.

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
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

Chcete-li tuto šablonu nasadit pomocí příkazového příkazového příkazu k řešení Azure, použijte:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Chcete-li tuto šablonu nasadit pomocí PowerShellu, použijte:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Definování a přiřazení zásad

Zásadu můžete [definovat](../../governance/policy/concepts/definition-structure.md) a přiřadit ve stejné šabloně.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
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
      "apiVersion": "2018-05-01",
      "name": "location-lock",
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

Chcete-li vytvořit definici zásad v předplatném a použít ji na předplatné, použijte následující příkaz příkazu příkazu k příkazu:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Chcete-li tuto šablonu nasadit pomocí PowerShellu, použijte:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="template-samples"></a>Ukázky šablon

* [Vytvořte skupinu prostředků, uzamkněte ji a udělte jí oprávnění](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).
* [Vytvořte skupinu zdrojů, zásadu a přiřazení zásad](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Další kroky

* Další informace o přiřazování rolí najdete [v tématu Správa přístupu k prostředkům Azure pomocí šablon RBAC a Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Příklad nasazení nastavení pracovního prostoru pro Azure Security Center najdete v [tématu nasazení ASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Ukázkové šablony najdete na [GitHubu](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).
* Můžete také nasadit šablony na [úrovni skupiny pro správu](deploy-to-management-group.md) a [na úrovni klienta](deploy-to-tenant.md).
