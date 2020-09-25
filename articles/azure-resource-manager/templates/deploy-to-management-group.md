---
title: Nasazení prostředků do skupiny pro správu
description: V této části najdete popis postupu nasazení prostředků v oboru skupiny pro správu v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 0c5ed8d2427a9e0329db6ebd7f0aa48aa4912a48
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91284818"
---
# <a name="create-resources-at-the-management-group-level"></a>Vytváření prostředků na úrovni skupiny pro správu

V případě zralosti vaší organizace můžete nasadit šablonu Azure Resource Manager (šablonu ARM) a vytvořit prostředky na úrovni skupiny pro správu. Například může být potřeba definovat a přiřadit [zásady](../../governance/policy/overview.md) nebo [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) pro skupinu pro správu. Šablony na úrovni skupiny pro správu umožňují deklarativní použití zásad a přiřazování rolí na úrovni skupiny pro správu.

## <a name="supported-resources"></a>Podporované prostředky

Ne všechny typy prostředků lze nasadit do úrovně skupiny pro správu. V této části jsou uvedeny typy prostředků, které jsou podporovány.

Pro plány Azure použijte:

* [artefakty](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [podrobné plány](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [zachovávaných](/azure/templates/microsoft.blueprint/blueprints/versions)

Pro zásady Azure použijte:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [nápravy](/azure/templates/microsoft.policyinsights/remediations)

Pro řízení přístupu na základě role použijte:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

U vnořených šablon, které se nasazují do předplatných nebo skupin prostředků, použijte:

* [nasazení](/azure/templates/microsoft.resources/deployments)

Pro správu prostředků použijte:

* [značky](/azure/templates/microsoft.resources/tags)

## <a name="schema"></a>Schéma

Schéma, které používáte pro nasazení skupiny pro správu, se liší od schématu pro nasazení skupin prostředků.

Pro šablony použijte:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Schéma pro soubor parametrů je pro všechny obory nasazení stejné. Pro soubory parametrů použijte:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-scopes"></a>Obory nasazení

Při nasazování do skupiny pro správu můžete cílit na skupinu pro správu zadanou v příkazu nasazení nebo můžete vybrat jinou skupinu pro správu v tenantovi.

Prostředky definované v části Resources v šabloně jsou aplikovány na skupinu pro správu z příkazu nasazení.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

Chcete-li cílit na jinou skupinu pro správu, přidejte vnořené nasazení a zadejte `scope` vlastnost. Nastavte `scope` vlastnost na hodnotu ve formátu `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,22":::

V rámci skupiny pro správu můžete také cílit na předplatná nebo skupiny prostředků. Uživatel, který šablonu nasazuje, musí mít přístup k zadanému oboru.

Chcete-li cílit na předplatné v rámci skupiny pro správu, použijte vnořené nasazení a `subscriptionId` vlastnost.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="10,18":::

Pokud chcete cílit na skupinu prostředků v rámci tohoto předplatného, přidejte další vnořené nasazení a `resourceGroup` vlastnost.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="10,21,25":::

Pokud chcete použít nasazení skupiny pro správu pro vytvoření skupiny prostředků v rámci předplatného a nasazení účtu úložiště do této skupiny prostředků, přečtěte si téma [nasazení do předplatného a skupiny prostředků](#deploy-to-subscription-and-resource-group).

## <a name="deployment-commands"></a>Příkazy nasazení

Příkazy pro nasazení skupiny pro správu se liší od příkazů pro nasazení skupin prostředků.

Pro rozhraní příkazového řádku Azure CLI použijte [AZ Deployment g Create](/cli/azure/deployment/mg#az-deployment-mg-create):

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

V případě REST API použijte [nasazení – vytvořte v oboru skupiny pro správu](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Umístění a název nasazení

Pro nasazení na úrovni skupiny pro správu musíte zadat umístění pro nasazení. Umístění nasazení je oddělené od umístění prostředků, které nasazujete. Umístění nasazení určuje, kam se mají ukládat data nasazení.

Můžete zadat název nasazení nebo použít výchozí název nasazení. Výchozí název je název souboru šablony. Například nasazení šablony s názvem **azuredeploy.jsv** vytvoří výchozí název nasazení **azuredeploy**.

Pro každý název nasazení je umístění neměnné. Nasazení nelze vytvořit v jednom umístění, pokud existuje existující nasazení se stejným názvem v jiném umístění. Pokud se zobrazí kód chyby `InvalidDeploymentLocation` , použijte jiný název nebo stejné umístění jako předchozí nasazení pro tento název.

## <a name="use-template-functions"></a>Použití funkcí šablon

V případě nasazení skupin pro správu existují při používání funkcí šablon důležité důležité informace:

* Funkce [Resource ()](template-functions-resource.md#resourcegroup) **není podporována.**
* Funkce [Subscription ()](template-functions-resource.md#subscription) **není podporována.**
* Funkce [Reference ()](template-functions-resource.md#reference) a [list ()](template-functions-resource.md#list) jsou podporovány.
* Nepoužívejte funkci [ResourceID ()](template-functions-resource.md#resourceid) pro prostředky nasazené do skupiny pro správu.

  Místo toho použijte funkci [extensionResourceId ()](template-functions-resource.md#extensionresourceid) pro prostředky, které jsou implementovány jako rozšíření skupiny pro správu. Vlastní definice zásad, které jsou nasazené ve skupině pro správu, jsou rozšířeními skupiny pro správu.

  Pokud chcete získat ID prostředku pro definici vlastní zásady na úrovni skupiny pro správu, použijte:
  
  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

  Pro prostředky tenanta, které jsou k dispozici v rámci skupiny pro správu, použijte funkci [tenantResourceId](template-functions-resource.md#tenantresourceid) . Předdefinované definice zásad jsou prostředky na úrovni tenanta.

  Pokud chcete získat ID prostředku pro předdefinovanou definici zásady, použijte:
  
  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="azure-policy"></a>Azure Policy

Následující příklad ukazuje, jak [definovat](../../governance/policy/concepts/definition-structure.md) zásadu na úrovni skupiny pro správu a přiřadit ji.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
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
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Nasazení do předplatného a skupiny prostředků

Z nasazení na úrovni skupiny pro správu můžete cílit na předplatné v rámci skupiny pro správu. Následující příklad vytvoří skupinu prostředků v rámci předplatného a nasadí účet úložiště do této skupiny prostředků.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2020-06-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]",
            },
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedSubRG",
              "resourceGroup": "[parameters('nestedRG')]",
              "dependsOn": [
                "[parameters('nestedRG')]"
              ],
              "properties": {
                "mode": "Incremental",
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[parameters('storageAccountName')]",
                      "location": "[parameters('nestedLocation')]",
                      "sku": {
                        "name": "Standard_LRS"
                      }
                    }
                  ]
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

* Další informace o přiřazování rolí najdete v tématu [Přidání přiřazení rolí Azure pomocí šablon Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Příklad nasazení nastavení pracovního prostoru pro Azure Security Center najdete v tématu [deployASCwithWorkspaceSettings.jsna](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Šablony můžete také nasadit na úrovni [předplatného](deploy-to-subscription.md) a na [úrovni tenanta](deploy-to-tenant.md).
