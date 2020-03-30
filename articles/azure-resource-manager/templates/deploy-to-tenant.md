---
title: Nasazení prostředků do klienta
description: Popisuje, jak nasadit prostředky v oboru klienta v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: fcdfc5b1c4333a0d7eeec80a09ad85579a1f8b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460258"
---
# <a name="create-resources-at-the-tenant-level"></a>Vytvoření prostředků na úrovni klienta

Jak vaše organizace dozrává, možná budete muset definovat a přiřadit [zásady](../../governance/policy/overview.md) nebo [ovládací prvky přístupu založené na rolích](../../role-based-access-control/overview.md) v rámci vašeho klienta Azure AD. Pomocí šablon na úrovni tenanta můžete deklarativně použít zásady a přiřadit role na globální úrovni.

## <a name="supported-resources"></a>Podporované prostředky

Na úrovni klienta můžete nasadit následující typy prostředků:

* [nasazení](/azure/templates/microsoft.resources/deployments) – pro vnořené šablony, které se nasazují do skupin pro správu nebo předplatných.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleÚkoly](/azure/templates/microsoft.authorization/roleassignments)
* [definice rolí](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schéma

Schéma, které používáte pro nasazení klienta, se liší od schématu pro nasazení skupiny prostředků.

Pro šablony použijte:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Schéma pro soubor parametrů je stejné pro všechny obory nasazení. Pro soubory parametrů použijte:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Požadovaný přístup

Hlavní nasazení šablony musí mít oprávnění k vytváření prostředků v oboru klienta. Objekt zabezpečení musí mít oprávnění k`Microsoft.Resources/deployments/*`provádění akcí nasazení ( ) a k vytvoření prostředků definovaných v šabloně. Chcete-li například vytvořit skupinu pro správu, musí mít objekt zabezpečení oprávnění přispěvatele v oboru klienta. Chcete-li vytvořit přiřazení rolí, musí mít objekt zabezpečení oprávnění Vlastník.

Globální správce služby Azure Active Directory nemá automaticky oprávnění k přiřazování rolí. Chcete-li povolit nasazení šablon v oboru klienta, globální správce musí provést následující kroky:

1. Zvyšte přístup k účtu, aby globální správce mohl přiřadit role. Další informace najdete [v tématu Zvýšení přístupu ke správě všech předplatných Azure a skupin pro správu](../../role-based-access-control/elevate-access-global-admin.md).

1. Přiřaďte vlastníka nebo přispěvatele k objektu zabezpečení, který potřebuje k nasazení šablon.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

Objekt zabezpečení má nyní požadovaná oprávnění k nasazení šablony.

## <a name="deployment-commands"></a>Příkazy nasazení

Příkazy pro nasazení klienta se liší od příkazů pro nasazení skupiny prostředků.

Pro Azure CLI použijte [vytvoření klienta nasazení az](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Pro Azure PowerShell použijte [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Pro rozhraní REST API použijte [nasazení – vytvořit nebo aktualizovat v oboru klienta](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Umístění a název nasazení

Pro nasazení na úrovni klienta je nutné zadat umístění pro nasazení. Umístění nasazení je oddělené od umístění prostředků, které nasazujete. Umístění nasazení určuje, kam se mají ukládat data nasazení.

Můžete zadat název pro nasazení nebo použít výchozí název nasazení. Výchozí název je název souboru šablony. Například nasazení šablony s názvem **azuredeploy.json** vytvoří výchozí název nasazení **azuredeploy**.

Pro každý název nasazení umístění je neměnné. Nasazení nelze vytvořit na jednom místě, pokud existuje existující nasazení se stejným názvem v jiném umístění. Pokud se zobrazí `InvalidDeploymentLocation`kód chyby , použijte jiný název nebo stejné umístění jako předchozí nasazení pro tento název.

## <a name="use-template-functions"></a>Použití funkcí šablony

Pro nasazení klienta existují některé důležité důležité aspekty při použití funkce šablony:

* Funkce [resourceGroup()](template-functions-resource.md#resourcegroup) **není** podporována.
* Funkce [subscription()](template-functions-resource.md#subscription) **není** podporována.
* [Funkce reference()](template-functions-resource.md#reference) a [list()](template-functions-resource.md#list) jsou podporovány.
* Pomocí funkce [tenantResourceId()](template-functions-resource.md#tenantresourceid) získat ID prostředku pro prostředky, které jsou nasazeny na úrovni klienta.

  Chcete-li například získat ID prostředku pro definici zásad, použijte:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Vrácené ID prostředku má následující formát:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Vytvoření skupiny pro správu

[Následující šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) vytvoří skupinu pro správu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>Přiřadit roli

[Následující šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) přiřadí roli v oboru klienta.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

* Další informace o přiřazování rolí najdete [v tématu Správa přístupu k prostředkům Azure pomocí šablon RBAC a Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Šablony můžete také nasadit na [úrovni předplatného](deploy-to-subscription.md) nebo [na úrovni skupiny pro správu](deploy-to-management-group.md).
