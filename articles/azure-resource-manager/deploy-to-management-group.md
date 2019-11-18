---
title: Nasazení prostředků do skupiny pro správu
description: V této části najdete popis postupu nasazení prostředků v oboru skupiny pro správu v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: c8ed8ae20192599ff7cda691429230d8c2a104fc
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149920"
---
# <a name="create-resources-at-the-management-group-level"></a>Vytváření prostředků na úrovni skupiny pro správu

Prostředky Azure se obvykle nasazují do skupiny prostředků ve vašem předplatném Azure. Můžete ale také vytvořit prostředky na úrovni skupiny pro správu. Nasazení na úrovni skupiny pro správu můžete použít k provádění akcí, které na této úrovni mají smysl, například přiřazení [řízení přístupu na základě role](../role-based-access-control/overview.md) nebo použití [zásad](../governance/policy/overview.md).

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

* Funkce [Resource ()](resource-group-template-functions-resource.md#resourcegroup) **není podporována.**
* Funkce [Subscription ()](resource-group-template-functions-resource.md#subscription) **není podporována.**
* Funkce [ResourceID ()](resource-group-template-functions-resource.md#resourceid) je podporována. Použijte ho k získání ID prostředku pro prostředky, které se používají v nasazeních na úrovni skupiny pro správu. Můžete například získat ID prostředku pro definici zásady s `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`. Vrátí ID prostředku ve formátu `/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}`.
* Funkce [Reference ()](resource-group-template-functions-resource.md#reference) a [list ()](resource-group-template-functions-resource.md#list) jsou podporovány.

## <a name="create-policies"></a>Vytvoření zásad

### <a name="define-policy"></a>Definovat zásady

Následující příklad ukazuje, jak [definovat](../governance/policy/concepts/definition-structure.md) zásadu na úrovni skupiny pro správu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```



## <a name="next-steps"></a>Další kroky

* Další informace o přiřazování rolí najdete v tématu [Správa přístupu k prostředkům Azure pomocí šablon RBAC a Azure Resource Manager](../role-based-access-control/role-assignments-template.md).
* Příklad nasazení nastavení pracovního prostoru pro Azure Security Center najdete v tématu [deployASCwithWorkspaceSettings. JSON](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Další informace o vytváření šablon Azure Resource Manager najdete v tématu [vytváření šablon](resource-group-authoring-templates.md). 
* Seznam dostupných funkcí v šabloně najdete v tématu [funkce šablon](resource-group-template-functions.md).