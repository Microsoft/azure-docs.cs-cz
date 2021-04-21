---
title: Nasazení prostředků do skupiny pro správu
description: V této části najdete popis postupu nasazení prostředků v oboru skupiny pro správu v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 74e00921a1170a7750f4a2d239bb778150ac2cae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781932"
---
# <a name="management-group-deployments-with-arm-templates"></a>Nasazení skupin pro správu pomocí šablon ARM

V případě zralosti vaší organizace můžete nasadit šablonu Azure Resource Manager (šablonu ARM) a vytvořit prostředky na úrovni skupiny pro správu. Například může být potřeba definovat a přiřadit [zásady](../../governance/policy/overview.md) nebo [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) pro skupinu pro správu. Šablony na úrovni skupiny pro správu umožňují deklarativní použití zásad a přiřazování rolí na úrovni skupiny pro správu.

## <a name="supported-resources"></a>Podporované prostředky

Ne všechny typy prostředků lze nasadit do úrovně skupiny pro správu. V této části jsou uvedeny typy prostředků, které jsou podporovány.

Pro plány Azure použijte:

* [artefakty](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [podrobné plány](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [zachovávaných](/azure/templates/microsoft.blueprint/blueprints/versions)

Pro Azure Policy použijte:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [nápravy](/azure/templates/microsoft.policyinsights/remediations)

Pro řízení přístupu na základě role Azure (Azure RBAC) použijte:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

U vnořených šablon, které se nasazují do předplatných nebo skupin prostředků, použijte:

* [nasazení](/azure/templates/microsoft.resources/deployments)

Pro správu prostředků použijte:

* [značky](/azure/templates/microsoft.resources/tags)

Skupiny pro správu jsou prostředky na úrovni tenanta. Skupiny pro správu v nasazení skupiny pro správu však můžete vytvořit nastavením rozsahu nové skupiny pro správu na tenanta. Viz [skupina pro správu](#management-group).

## <a name="schema"></a>Schéma

Schéma, které používáte pro nasazení skupiny pro správu, se liší od schématu pro nasazení skupin prostředků.

Pro šablony použijte:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
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

K nasazení do skupiny pro správu použijte příkazy pro nasazení skupiny pro správu.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pro rozhraní příkazového řádku Azure CLI použijte [AZ Deployment g Create](/cli/azure/deployment/mg#az_deployment_mg_create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pro Azure PowerShell použijte [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
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

Pro nasazení na úrovni skupiny pro správu musíte zadat umístění pro nasazení. Umístění nasazení je oddělené od umístění prostředků, které nasazujete. Umístění nasazení určuje, kam se mají ukládat data nasazení. [Předplatné](deploy-to-subscription.md) a nasazení [klientů](deploy-to-tenant.md) také vyžadují umístění. Pro nasazení [skupin prostředků](deploy-to-resource-group.md) se umístění skupiny prostředků používá k ukládání dat nasazení.

Můžete zadat název nasazení nebo použít výchozí název nasazení. Výchozí název je název souboru šablony. Například nasazení šablony s názvem _azuredeploy.jsv_ vytvoří výchozí název nasazení **azuredeploy**.

Pro každý název nasazení je umístění neměnné. Nasazení nelze vytvořit v jednom umístění, pokud existuje existující nasazení se stejným názvem v jiném umístění. Pokud například vytvoříte nasazení skupiny pro správu s názvem **deployment1** v **centralus**, nemůžete později vytvořit další nasazení s názvem **deployment1** , ale umístěním **westus**. Pokud se zobrazí kód chyby `InvalidDeploymentLocation` , použijte jiný název nebo stejné umístění jako předchozí nasazení pro tento název.

## <a name="deployment-scopes"></a>Obory nasazení

Při nasazování do skupiny pro správu můžete prostředky nasadit do:

* Cílová skupina pro správu z operace
* jiná skupina pro správu v tenantovi
* předplatná ve skupině pro správu
* skupiny prostředků ve skupině pro správu
* tenant pro skupinu prostředků

[Prostředek rozšíření](scope-extension-resources.md) může být vymezen na cíl, který se liší od cíle nasazení.

Uživatel, který šablonu nasazuje, musí mít přístup k zadanému oboru.

V této části se dozvíte, jak zadat různé obory. Tyto různé obory můžete kombinovat v jediné šabloně.

### <a name="scope-to-target-management-group"></a>Obor pro cílovou skupinu pro správu

Prostředky definované v části Resources v šabloně jsou aplikovány na skupinu pro správu z příkazu nasazení.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>Rozsah do jiné skupiny pro správu

Chcete-li cílit na jinou skupinu pro správu, přidejte vnořené nasazení a zadejte `scope` vlastnost. Nastavte `scope` vlastnost na hodnotu ve formátu `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Rozsah pro předplatné

V rámci skupiny pro správu můžete také cílit na předplatná. Uživatel, který šablonu nasazuje, musí mít přístup k zadanému oboru.

Chcete-li cílit na předplatné v rámci skupiny pro správu, použijte vnořené nasazení a `subscriptionId` vlastnost.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Rozsah do skupiny prostředků

V rámci skupiny pro správu můžete také cílit na skupiny prostředků. Uživatel, který šablonu nasazuje, musí mít přístup k zadanému oboru.

Chcete-li cílit na skupinu prostředků v rámci skupiny pro správu, použijte vnořené nasazení. Nastavte `subscriptionId` vlastnosti a `resourceGroup` . Nenastavte umístění pro vnořené nasazení, protože je nasazené v umístění skupiny prostředků.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

Pokud chcete použít nasazení skupiny pro správu pro vytvoření skupiny prostředků v rámci předplatného a nasazení účtu úložiště do této skupiny prostředků, přečtěte si téma [nasazení do předplatného a skupiny prostředků](#deploy-to-subscription-and-resource-group).

### <a name="scope-to-tenant"></a>Rozsah do tenanta

Chcete-li vytvořit prostředky v tenantovi, nastavte `scope` na `/` . Uživatel, který šablonu nasazuje, musí mít [požadovaný přístup k nasazení v tenantovi](deploy-to-tenant.md#required-access).

Chcete-li použít vnořené nasazení, nastavte `scope` a `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

Nebo můžete nastavit obor na `/` pro některé typy prostředků, jako jsou skupiny pro správu. Vytvoření nové skupiny pro správu je popsáno v následující části.

## <a name="management-group"></a>Skupina pro správu

Chcete-li vytvořit skupinu pro správu v nasazení skupiny pro správu, je nutné nastavit obor `/` pro skupinu pro správu.

Následující příklad vytvoří novou skupinu pro správu v kořenové skupině pro správu.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

Následující příklad vytvoří novou skupinu pro správu ve skupině pro správu, která je zadána jako nadřazená. Všimněte si, že obor je nastaven na hodnotu `/` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string",
            "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
        },
        "parentMG": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('mgName')]",
            "type": "Microsoft.Management/managementGroups",
            "apiVersion": "2020-05-01",
            "scope": "/",
            "location": "eastus",
            "properties": {
                "details": {
                    "parent": {
                        "id": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('parentMG'))]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "output": {
            "type": "string",
            "value": "[parameters('mgName')]"
        }
    }
}
```

## <a name="subscriptions"></a>Předplatná

Chcete-li použít šablonu ARM k vytvoření nového předplatného Azure ve skupině pro správu, přečtěte si téma:

* [Programové vytváření předplatných Azure smlouva Enterprise](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [Programové vytvoření předplatných Azure pro zákaznickou smlouvu Microsoftu](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [Programové vytvoření předplatných Azure pro partnerských smluv Microsoftu](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

Pokud chcete nasadit šablonu, která přesouvá stávající předplatné Azure do nové skupiny pro správu, přečtěte si téma [Přesun předplatných v ŠABLONĚ ARM](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template) .

## <a name="azure-policy"></a>Azure Policy

Vlastní definice zásad, které jsou nasazené ve skupině pro správu, jsou rozšířeními skupiny pro správu. K získání ID vlastní definice zásady použijte funkci [extensionResourceId ()](template-functions-resource.md#extensionresourceid) . Předdefinované definice zásad jsou prostředky na úrovni tenanta. K získání ID předdefinované definice zásady použijte funkci [tenantResourceId ()](template-functions-resource.md#tenantresourceid) .

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
            "apiVersion": "2020-10-01",
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
                            "apiVersion": "2020-10-01",
                            "name": "[parameters('nestedRG')]",
                            "location": "[parameters('nestedLocation')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedRG",
            "subscriptionId": "[parameters('nestedSubId')]",
            "resourceGroup": "[parameters('nestedRG')]",
            "dependsOn": [
                "nestedSub"
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
                            "kind": "StorageV2",
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
```

## <a name="next-steps"></a>Další kroky

* Další informace o přiřazování rolí najdete v tématu [Přidání přiřazení rolí Azure pomocí šablon Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Příklad nasazení nastavení pracovního prostoru pro Azure Security Center najdete v tématu [deployASCwithWorkspaceSettings.jsna](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Šablony můžete také nasadit na úrovni [předplatného](deploy-to-subscription.md) a na [úrovni tenanta](deploy-to-tenant.md).
