---
title: Nasazení prostředků do skupiny pro správu
description: V této části najdete popis postupu nasazení prostředků v oboru skupiny pro správu v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 0419f3daca6845c6809c9f66e870fdf884a7193f
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117035"
---
# <a name="create-resources-at-the-management-group-level"></a>Vytváření prostředků na úrovni skupiny pro správu

Prostředky Azure se obvykle nasazují do skupiny prostředků ve vašem předplatném Azure. Můžete ale také vytvořit prostředky na úrovni skupiny pro správu. Nasazení na úrovni skupiny pro správu můžete použít k provádění akcí, které na této úrovni mají smysl, například přiřazení [řízení přístupu na základě role](../../role-based-access-control/overview.md) nebo použití [zásad](../../governance/policy/overview.md).

V současné době nasazování šablon na úrovni skupiny pro správu je nutné použít REST API.

## <a name="supported-resources"></a>Podporované prostředky

Na úrovni skupiny pro správu můžete nasadit následující typy prostředků:

* [nasazení](/azure/templates/microsoft.resources/deployments)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schéma

Schéma, které používáte pro nasazení skupiny pro správu, se liší od schématu pro nasazení skupin prostředků.

Pro šablony použijte:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Pro soubory parametrů použijte:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Příkazy nasazení

Příkaz pro nasazení skupiny pro správu se liší od příkazu pro nasazení skupin prostředků.

V případě REST API použijte [nasazení – vytvořte v oboru skupiny pro správu](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Umístění a název nasazení

Pro nasazení na úrovni skupiny pro správu musíte zadat umístění pro nasazení. Umístění nasazení je oddělené od umístění prostředků, které nasazujete. Umístění nasazení určuje, kam se mají ukládat data nasazení.

Můžete zadat název nasazení nebo použít výchozí název nasazení. Výchozí název je název souboru šablony. Například nasazení šablony s názvem **azuredeploy. JSON** vytvoří výchozí název nasazení **azuredeploy**.

Pro každý název nasazení je umístění neměnné. Nasazení nelze vytvořit v jednom umístění, pokud existuje existující nasazení se stejným názvem v jiném umístění. Pokud se zobrazí kód chyby `InvalidDeploymentLocation`, buď použijte jiný název nebo stejné umístění jako předchozí nasazení pro tento název.

## <a name="use-template-functions"></a>Použití funkcí šablon

V případě nasazení skupin pro správu existují při používání funkcí šablon důležité důležité informace:

* Funkce [Resource ()](template-functions-resource.md#resourcegroup) **není podporována.**
* Funkce [Subscription ()](template-functions-resource.md#subscription) **není podporována.**
* Funkce [Reference ()](template-functions-resource.md#reference) a [list ()](template-functions-resource.md#list) jsou podporovány.
* Funkce [ResourceID ()](template-functions-resource.md#resourceid) je podporována. Použijte ho k získání ID prostředku pro prostředky, které se používají v nasazeních na úrovni skupiny pro správu. Nezadávejte hodnotu parametru skupiny prostředků.

  Pokud například chcete získat ID prostředku pro definici zásady, použijte:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  ID vráceného prostředku má následující formát:
  
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

### <a name="assign-policy"></a>Přiřadit zásady

Následující příklad přiřadí existující definici zásady ke skupině pro správu. Pokud zásady přebírají parametry, poskytněte je jako objekt. Pokud zásada nepřijímá parametry, použijte výchozí prázdný objekt.

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

* Vytvořte skupinu prostředků, zásadu a přiřazení zásad.  Podívejte se [sem](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Další kroky

* Další informace o přiřazování rolí najdete v tématu [Správa přístupu k prostředkům Azure pomocí šablon RBAC a Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Příklad nasazení nastavení pracovního prostoru pro Azure Security Center najdete v tématu [deployASCwithWorkspaceSettings. JSON](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Další informace o vytváření šablon Azure Resource Manager najdete v tématu [vytváření šablon](template-syntax.md).
* Seznam dostupných funkcí v šabloně najdete v tématu [funkce šablon](template-functions.md).