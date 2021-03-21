---
title: Nasazení prostředků do předplatného
description: Popisuje postup vytvoření skupiny prostředků v Azure Resource Manager šabloně. Také ukazuje, jak nasadit prostředky v oboru předplatného Azure.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: b5c99e5dc21c2b93f1c9da3977302a2dd311277f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491499"
---
# <a name="subscription-deployments-with-arm-templates"></a>Nasazení předplatných pomocí šablon ARM

Pro zjednodušení správy prostředků můžete použít šablonu Azure Resource Manager (šablonu ARM) k nasazení prostředků na úrovni předplatného Azure. Můžete třeba nasadit [zásady](../../governance/policy/overview.md) a [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) do svého předplatného, které je aplikuje v rámci vašeho předplatného. V rámci předplatného můžete také vytvářet skupiny prostředků a nasazovat prostředky do skupin prostředků v rámci předplatného.

> [!NOTE]
> V nasazení na úrovni předplatného můžete nasadit do 800 různých skupin prostředků.

Pokud chcete nasadit šablony na úrovni předplatného, použijte Azure CLI, PowerShell, REST API nebo portál.

## <a name="supported-resources"></a>Podporované prostředky

Ne všechny typy prostředků se dají nasadit na úroveň předplatného. V této části jsou uvedeny typy prostředků, které jsou podporovány.

Pro plány Azure použijte:

* [artefakty](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [podrobné plány](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [verze (modrotisky)](/azure/templates/microsoft.blueprint/blueprints/versions)

Pro zásady Azure použijte:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [nápravy](/azure/templates/microsoft.policyinsights/remediations)

Pro řízení přístupu na základě role Azure (Azure RBAC) použijte:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

U vnořených šablon, které se nasazují do skupin prostředků, použijte:

* [nasazení](/azure/templates/microsoft.resources/deployments)

Pro vytváření nových skupin prostředků použijte:

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Pro správu předplatného použijte:

* [Konfigurace Advisoru](/azure/templates/microsoft.advisor/configurations)
* [projektů](/azure/templates/microsoft.consumption/budgets)
* [Změnit profil analýzy](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [značky](/azure/templates/microsoft.resources/tags)

Mezi další podporované typy patří:

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>Schéma

Schéma, které používáte pro nasazení na úrovni předplatného, se liší od schématu pro nasazení skupin prostředků.

Pro šablony použijte:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    ...
}
```

Schéma pro soubor parametrů je pro všechny obory nasazení stejné. Pro soubory parametrů použijte:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Příkazy nasazení

K nasazení do předplatného použijte příkazy pro nasazení na úrovni předplatného.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pro rozhraní příkazového řádku Azure CLI použijte [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create). Následující příklad nasadí šablonu pro vytvoření skupiny prostředků:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pro příkaz nasazení prostředí PowerShell použijte rutinu [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) nebo její alias `New-AzSubscriptionDeployment` . Následující příklad nasadí šablonu pro vytvoření skupiny prostředků:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

---

Podrobnější informace o příkazech nasazení a možnostech nasazení šablon ARM najdete v těchto tématech:

* [Nasazení prostředků pomocí šablon ARM a Azure Portal](deploy-portal.md)
* [Nasazení prostředků pomocí šablon ARM a Azure CLI](deploy-cli.md)
* [Nasazení prostředků pomocí šablon ARM a Azure PowerShell](deploy-powershell.md)
* [Nasazení prostředků pomocí šablon ARM a Azure Resource Manager REST API](deploy-rest.md)
* [Použití tlačítka nasazení k nasazení šablon z úložiště GitHub](deploy-to-azure-button.md)
* [Nasazení šablon ARM z Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Umístění a název nasazení

Pro nasazení na úrovni předplatného musíte zadat umístění pro nasazení. Umístění nasazení je oddělené od umístění prostředků, které nasazujete. Umístění nasazení určuje, kam se mají ukládat data nasazení. [Skupina pro správu](deploy-to-management-group.md) a nasazení [klientů](deploy-to-tenant.md) také vyžadují umístění. Pro nasazení [skupin prostředků](deploy-to-resource-group.md) se umístění skupiny prostředků používá k ukládání dat nasazení.

Můžete zadat název nasazení nebo použít výchozí název nasazení. Výchozí název je název souboru šablony. Například nasazení šablony s názvem _azuredeploy.jsv_ vytvoří výchozí název nasazení **azuredeploy**.

Pro každý název nasazení je umístění neměnné. Nasazení nelze vytvořit v jednom umístění, pokud existuje existující nasazení se stejným názvem v jiném umístění. Pokud například vytvoříte nasazení předplatného s názvem **deployment1** v **centralus**, nemůžete později vytvořit další nasazení s názvem **deployment1** , ale umístěním **westus**. Pokud se zobrazí kód chyby `InvalidDeploymentLocation` , použijte jiný název nebo stejné umístění jako předchozí nasazení pro tento název.

## <a name="deployment-scopes"></a>Obory nasazení

Při nasazování do předplatného můžete prostředky nasadit do:

* cílové předplatné operace
* jakékoli předplatné v tenantovi
* skupiny prostředků v rámci předplatného nebo jiných předplatných
* tenant pro předplatné

[Prostředek rozšíření](scope-extension-resources.md) může být vymezen na cíl, který se liší od cíle nasazení.

Uživatel, který šablonu nasazuje, musí mít přístup k zadanému oboru.

V této části se dozvíte, jak zadat různé obory. Tyto různé obory můžete kombinovat v jediné šabloně.

### <a name="scope-to-target-subscription"></a>Obor do cílového předplatného

Chcete-li nasadit prostředky do cílového předplatného, přidejte tyto prostředky do části Resources (prostředky) v šabloně.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

Příklady nasazení do předplatného najdete v tématu [Vytvoření skupin prostředků](#create-resource-groups) a [přiřazení definice zásad](#assign-policy-definition).

### <a name="scope-to-other-subscription"></a>Rozsah pro jiné předplatné

Pokud chcete nasadit prostředky do předplatného, které se liší od předplatného této operace, přidejte vnořené nasazení. Nastavte `subscriptionId` vlastnost na ID předplatného, které chcete nasadit. Nastavte `location` vlastnost pro vnořené nasazení.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-sub.json" highlight="9,10,14":::

### <a name="scope-to-resource-group"></a>Rozsah do skupiny prostředků

Pokud chcete nasadit prostředky do skupiny prostředků v rámci předplatného, přidejte vnořené nasazení a zahrňte `resourceGroup` vlastnost. V následujícím příkladu je vnořené nasazení cíleno na skupinu prostředků s názvem `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

Příklad nasazení do skupiny prostředků najdete v tématu věnovaném [Vytvoření skupiny prostředků a prostředků](#create-resource-group-and-resources).

### <a name="scope-to-tenant"></a>Rozsah do tenanta

Chcete-li vytvořit prostředky v tenantovi, nastavte `scope` na `/` . Uživatel, který šablonu nasazuje, musí mít [požadovaný přístup k nasazení v tenantovi](deploy-to-tenant.md#required-access).

Chcete-li použít vnořené nasazení, nastavte `scope` a `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-to-tenant.json" highlight="9,10,14":::

Nebo můžete nastavit obor na `/` pro některé typy prostředků, jako jsou skupiny pro správu.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-create-mg.json" highlight="12,15":::

Další informace najdete v tématu [skupina pro správu](deploy-to-management-group.md#management-group).

## <a name="resource-groups"></a>Skupiny prostředků

### <a name="create-resource-groups"></a>Vytvoření skupin prostředků

Pokud chcete vytvořit skupinu prostředků v šabloně ARM, definujte prostředek [Microsoft. Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) s názvem a umístěním pro skupinu prostředků.

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
      "apiVersion": "2020-06-01",
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
      "apiVersion": "2020-06-01",
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

Informace o iteraci prostředků najdete [v tématu iterace prostředků v šablonách ARM](./copy-resources.md)a [kurz: vytvoření více instancí prostředků pomocí šablon ARM](./template-tutorial-create-multiple-instances.md).

### <a name="create-resource-group-and-resources"></a>Vytvoření skupiny prostředků a prostředků

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
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
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
        "policyDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
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

## <a name="azure-blueprints"></a>Azure Blueprints

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

## <a name="access-control"></a>Řízení přístupu

Další informace o přiřazování rolí najdete v tématu [Přidání přiřazení rolí Azure pomocí šablon Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).

Následující příklad vytvoří skupinu prostředků, použije na ni zámek a přiřadí roli objektu zabezpečení.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Další kroky

* Příklad nasazení nastavení pracovního prostoru pro Azure Security Center najdete v tématu [deployASCwithWorkspaceSettings.jsna](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Ukázkové šablony najdete na [GitHubu](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments).
* Šablony můžete nasadit také na úrovni [skupiny pro správu](deploy-to-management-group.md) a na [úrovni tenanta](deploy-to-tenant.md).
