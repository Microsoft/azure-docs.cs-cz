---
title: Nasazení prostředků do předplatného
description: Popisuje postup vytvoření skupiny prostředků v Azure Resource Manager šabloně. Také ukazuje, jak nasadit prostředky v oboru předplatného Azure.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 17ae335fa40a71ed266211150df382404efbf61b
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84673968"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Vytvoření skupin prostředků a prostředků na úrovni předplatného

Pro zjednodušení správy prostředků můžete nasadit prostředky na úrovni předplatného Azure. Můžete například nasadit [zásady](../../governance/policy/overview.md) a [řízení přístupu na základě rolí](../../role-based-access-control/overview.md) k vašemu předplatnému a tyto prostředky se uplatní v rámci vašeho předplatného. Můžete také vytvořit skupiny prostředků a nasadit prostředky do těchto skupin prostředků.

> [!NOTE]
> V nasazení na úrovni předplatného můžete nasadit do 800 různých skupin prostředků.

Pokud chcete nasadit šablony na úrovni předplatného, použijte rozhraní příkazového řádku Azure CLI, PowerShellu nebo REST API. Azure Portal nepodporuje nasazení na úrovni předplatného.

## <a name="supported-resources"></a>Podporované prostředky

Na úrovni předplatného můžete nasadit následující typy prostředků:

* [podrobné plány](/azure/templates/microsoft.blueprint/blueprints)
* [projektů](/azure/templates/microsoft.consumption/budgets)
* [nasazení](/azure/templates/microsoft.resources/deployments) – pro vnořené šablony, které se nasazují do skupin prostředků.
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [nápravy](/azure/templates/microsoft.policyinsights/2019-07-01/remediations)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)
* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [značky](/azure/templates/microsoft.resources/tags)
* [workspacesettings](/azure/templates/microsoft.security/workspacesettings)

### <a name="schema"></a>Schéma

Schéma, které používáte pro nasazení na úrovni předplatného, se liší od schématu pro nasazení skupin prostředků.

Pro šablony použijte:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Schéma pro soubor parametrů je pro všechny obory nasazení stejné. Pro soubory parametrů použijte:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Příkazy nasazení

Příkazy pro nasazení na úrovni předplatného se liší od příkazů pro nasazení skupin prostředků.

Pro rozhraní příkazového řádku Azure CLI použijte [AZ Deployment sub Create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create). Následující příklad nasadí šablonu pro vytvoření skupiny prostředků:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Pro příkaz nasazení prostředí PowerShell použijte rutinu [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) nebo **New-AzSubscriptionDeployment**. Následující příklad nasadí šablonu pro vytvoření skupiny prostředků:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

V případě REST API použijte [nasazení – vytvořit v oboru předplatného](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Umístění a název nasazení

Pro nasazení na úrovni předplatného musíte zadat umístění pro nasazení. Umístění nasazení je oddělené od umístění prostředků, které nasazujete. Umístění nasazení určuje, kam se mají ukládat data nasazení.

Můžete zadat název nasazení nebo použít výchozí název nasazení. Výchozí název je název souboru šablony. Například nasazení šablony s názvem **azuredeploy.jsv** vytvoří výchozí název nasazení **azuredeploy**.

Pro každý název nasazení je umístění neměnné. Nasazení nelze vytvořit v jednom umístění, pokud existuje existující nasazení se stejným názvem v jiném umístění. Pokud se zobrazí kód chyby `InvalidDeploymentLocation` , použijte jiný název nebo stejné umístění jako předchozí nasazení pro tento název.

## <a name="use-template-functions"></a>Použití funkcí šablon

U nasazení na úrovni předplatného se při používání funkcí šablon vyskytly důležité předpoklady:

* Funkce [Resource ()](template-functions-resource.md#resourcegroup) **není podporována.**
* Funkce [Reference ()](template-functions-resource.md#reference) a [list ()](template-functions-resource.md#list) jsou podporovány.
* K získání ID prostředku pro prostředky, které jsou nasazeny na úrovni předplatného, použijte funkci [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) .

  Pokud například chcete získat ID prostředku pro definici zásady, použijte:

  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```

  ID vráceného prostředku má následující formát:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-resource-groups"></a>Vytvoření skupin prostředků

Pokud chcete vytvořit skupinu prostředků v Azure Resource Manager šabloně, definujte prostředek [Microsoft. Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) s názvem a umístěním pro skupinu prostředků. Můžete vytvořit skupinu prostředků a nasadit prostředky do této skupiny prostředků ve stejné šabloně.

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
      "apiVersion": "2019-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Pomocí [elementu Copy](copy-resources.md) se skupinami prostředků vytvořte více než jednu skupinu prostředků.

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
      "apiVersion": "2019-10-01",
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

Informace o iteraci prostředků najdete v tématu [nasazení více než jedné instance prostředku v šablonách Azure Resource Manager](./copy-resources.md)a [kurzu: vytvoření více instancí prostředků pomocí šablon Správce prostředků](./template-tutorial-create-multiple-instances.md).

## <a name="resource-group-and-resources"></a>Skupina prostředků a prostředky

Pokud chcete vytvořit skupinu prostředků a nasadit do ní prostředky, použijte vnořenou šablonu. Vnořená Šablona definuje prostředky pro nasazení do skupiny prostředků. Nastavte vnořenou šablonu jako závislou na skupině prostředků, abyste před nasazením prostředků zajistili, že skupina prostředků existuje. Můžete nasadit až 800 skupin prostředků.

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
      "apiVersion": "2019-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
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

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>Přiřazení definice zásady

Následující příklad přiřadí existující definici zásady k předplatnému. Pokud definice zásad přijímá parametry, poskytněte je jako objekt. Pokud definice zásady nepřijímá parametry, použijte výchozí prázdný objekt.

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

Pokud chcete tuto šablonu nasadit pomocí Azure CLI, použijte:

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Pokud chcete tuto šablonu nasadit pomocí PowerShellu, použijte:

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

### <a name="create-and-assign-policy-definitions"></a>Vytvoření a přiřazení definic zásad

Definici zásady můžete [definovat](../../governance/policy/concepts/definition-structure.md) a přiřadit ve stejné šabloně.

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

Pokud chcete vytvořit definici zásady v rámci vašeho předplatného a přiřadit ji k předplatnému, použijte následující příkaz CLI:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Pokud chcete tuto šablonu nasadit pomocí PowerShellu, použijte:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprint

### <a name="create-blueprint-definition"></a>Vytvořit definici podrobného plánu

Definici podrobného plánu můžete [vytvořit](../../governance/blueprints/tutorials/create-from-sample.md) ze šablony.

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

Pokud chcete ve svém předplatném vytvořit definici podrobného plánu, použijte následující příkaz rozhraní příkazového řádku:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Pokud chcete tuto šablonu nasadit pomocí PowerShellu, použijte:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="template-samples"></a>Ukázky šablon

* [Vytvořte skupinu prostředků, zamkněte ji a udělte jí oprávnění](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-rg-lock-role-assignment).
* [Vytvořte skupinu prostředků, zásadu a přiřazení zásad](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Další kroky

* Další informace o přiřazování rolí najdete v tématu [Správa přístupu k prostředkům Azure pomocí šablon RBAC a Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Příklad nasazení nastavení pracovního prostoru pro Azure Security Center najdete v tématu [deployASCwithWorkspaceSettings.jsna](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Ukázkové šablony najdete na [GitHubu](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments).
* Šablony můžete nasadit také na úrovni [skupiny pro správu](deploy-to-management-group.md) a na [úrovni tenanta](deploy-to-tenant.md).
