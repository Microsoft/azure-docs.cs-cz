---
title: Nasazení prostředků do skupiny pro správu
description: Popisuje, jak nasadit prostředky v oboru skupiny pro správu v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 863d1330412fa238b820eb0f1f05351fc723de6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460309"
---
# <a name="create-resources-at-the-management-group-level"></a>Vytvoření zdrojů na úrovni skupiny pro správu

Jak vaše organizace dozrává, možná budete muset definovat a přiřadit [zásady](../../governance/policy/overview.md) nebo [ovládací prvky přístupu založené na rolích](../../role-based-access-control/overview.md) pro skupinu pro správu. Pomocí šablon na úrovni skupiny pro správu můžete deklarativně použít zásady a přiřadit role na úrovni skupiny pro správu.

## <a name="supported-resources"></a>Podporované prostředky

Na úrovni skupiny pro správu můžete nasadit následující typy prostředků:

* [nasazení](/azure/templates/microsoft.resources/deployments) – pro vnořené šablony, které se nasazují do předplatných nebo skupin prostředků.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleÚkoly](/azure/templates/microsoft.authorization/roleassignments)
* [definice rolí](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schéma

Schéma, které používáte pro nasazení skupiny pro správu, se liší od schématu pro nasazení skupiny prostředků.

Pro šablony použijte:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Schéma pro soubor parametrů je stejné pro všechny obory nasazení. Pro soubory parametrů použijte:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Příkazy nasazení

Příkazy pro nasazení skupiny pro správu se liší od příkazů pro nasazení skupiny prostředků.

Pro azure cli použijte [nasazení az mg create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Pro Azure PowerShell použijte [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Pro rozhraní REST API použijte [nasazení – vytvořit v oboru skupiny pro správu](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Umístění a název nasazení

Pro nasazení na úrovni skupiny pro správu je nutné zadat umístění pro nasazení. Umístění nasazení je oddělené od umístění prostředků, které nasazujete. Umístění nasazení určuje, kam se mají ukládat data nasazení.

Můžete zadat název pro nasazení nebo použít výchozí název nasazení. Výchozí název je název souboru šablony. Například nasazení šablony s názvem **azuredeploy.json** vytvoří výchozí název nasazení **azuredeploy**.

Pro každý název nasazení umístění je neměnné. Nasazení nelze vytvořit na jednom místě, pokud existuje existující nasazení se stejným názvem v jiném umístění. Pokud se zobrazí `InvalidDeploymentLocation`kód chyby , použijte jiný název nebo stejné umístění jako předchozí nasazení pro tento název.

## <a name="use-template-functions"></a>Použití funkcí šablony

Pro nasazení skupiny pro správu existují některé důležité důležité aspekty při použití funkcí šablony:

* Funkce [resourceGroup()](template-functions-resource.md#resourcegroup) **není** podporována.
* Funkce [subscription()](template-functions-resource.md#subscription) **není** podporována.
* [Funkce reference()](template-functions-resource.md#reference) a [list()](template-functions-resource.md#list) jsou podporovány.
* Funkce [resourceId()](template-functions-resource.md#resourceid) je podporována. Použijte ji k získání ID prostředku pro prostředky, které se používají na úrovni nasazení skupiny pro správu. Nezadejte hodnotu parametru skupiny prostředků.

  Chcete-li například získat ID prostředku pro definici zásad, použijte:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Vrácené ID prostředku má následující formát:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>Vytvoření zásad

### <a name="define-policy"></a>Definovat zásady

Následující příklad ukazuje, jak [definovat](../../governance/policy/concepts/definition-structure.md) zásadu na úrovni skupiny pro správu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
    }
  ]
}
```

### <a name="assign-policy"></a>Přiřadit zásadu

Následující příklad přiřadí existující definici zásady skupině pro správu. Pokud zásada přebírá parametry, zadejte je jako objekt. Pokud zásada nepřevezme parametry, použijte výchozí prázdný objekt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

## <a name="template-sample"></a>Ukázka šablony

* [Vytvořte skupinu zdrojů, zásadu a přiřazení zásad](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Další kroky

* Další informace o přiřazování rolí najdete [v tématu Správa přístupu k prostředkům Azure pomocí šablon RBAC a Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Příklad nasazení nastavení pracovního prostoru pro Azure Security Center najdete v [tématu nasazení ASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Můžete také nasadit šablony na [úrovni předplatného](deploy-to-subscription.md) a na [úrovni klienta](deploy-to-tenant.md).
