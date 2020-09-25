---
title: Nasazení prostředků do tenanta
description: Popisuje postup nasazení prostředků v oboru tenanta v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: af75e4f0e51ac685986e57b3b92a23dd37174460
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91284755"
---
# <a name="create-resources-at-the-tenant-level"></a>Vytváření prostředků na úrovni tenanta

V případě, že vaše organizace bude vyspělá, možná budete muset v tenantovi Azure AD definovat a přiřazovat [zásady](../../governance/policy/overview.md) nebo [řízení přístupu na základě role Azure (RBAC)](../../role-based-access-control/overview.md) . Pomocí šablon na úrovni tenanta můžete deklarativně uplatňovat zásady a přiřazovat role na globální úrovni.

## <a name="supported-resources"></a>Podporované prostředky

Ne všechny typy prostředků se dají nasadit na úrovni tenanta. V této části jsou uvedeny typy prostředků, které jsou podporovány.

Pro zásady Azure použijte:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Pro řízení přístupu na základě role použijte:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

U vnořených šablon, které se nasazují do skupin pro správu, předplatných nebo skupin prostředků, použijte:

* [nasazení](/azure/templates/microsoft.resources/deployments)

Pro vytváření skupin pro správu použijte:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Pro správu nákladů použijte:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [pokynů](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

## <a name="schema"></a>Schéma

Schéma, které používáte pro nasazení klientů, se liší od schématu pro nasazení skupin prostředků.

Pro šablony použijte:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Schéma pro soubor parametrů je pro všechny obory nasazení stejné. Pro soubory parametrů použijte:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Požadovaný přístup

Objekt zabezpečení, který šablonu nasazuje, musí mít oprávnění k vytváření prostředků v oboru tenanta. Objekt zabezpečení musí mít oprávnění ke spuštění akcí nasazení ( `Microsoft.Resources/deployments/*` ) a k vytvoření prostředků definovaných v šabloně. Chcete-li například vytvořit skupinu pro správu, musí mít objekt zabezpečení oprávnění přispěvatele v oboru tenanta. Aby bylo možné vytvořit přiřazení rolí, musí mít objekt zabezpečení oprávnění vlastníka.

Globální správce pro Azure Active Directory nemá automaticky oprávnění k přiřazování rolí. Chcete-li povolit nasazení šablon v oboru klienta, globální správce musí provést následující kroky:

1. Zvyšte přístup k účtu, aby globální správce mohl přiřadit role. Další informace najdete v tématu [zvýšení úrovně přístupu ke správě všech předplatných Azure a skupin pro správu](../../role-based-access-control/elevate-access-global-admin.md).

1. Přiřaďte vlastníka nebo přispěvateli objektu zabezpečení, který potřebuje k nasazení šablon.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

Objekt zabezpečení má teď požadovaná oprávnění k nasazení šablony.

## <a name="deployment-scopes"></a>Obory nasazení

Při nasazování do tenanta můžete cílit na tenanta nebo skupiny pro správu, odběry a skupiny prostředků v tenantovi. Uživatel, který šablonu nasazuje, musí mít přístup k zadanému oboru.

Prostředky definované v části Resources v šabloně se aplikují na tenanta.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

Chcete-li cílit na skupinu pro správu v rámci tenanta, přidejte vnořené nasazení a zadejte `scope` vlastnost.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,22":::

## <a name="deployment-commands"></a>Příkazy nasazení

Příkazy pro nasazení klientů se liší od příkazů pro nasazení skupin prostředků.

Pro rozhraní příkazového řádku Azure CLI použijte [AZ Deployment tenant Create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

Pro Azure PowerShell použijte [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

V případě REST API použijte [nasazení – vytvořit nebo aktualizovat v oboru tenanta](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Umístění a název nasazení

Pro nasazení na úrovni tenanta musíte zadat umístění pro nasazení. Umístění nasazení je oddělené od umístění prostředků, které nasazujete. Umístění nasazení určuje, kam se mají ukládat data nasazení.

Můžete zadat název nasazení nebo použít výchozí název nasazení. Výchozí název je název souboru šablony. Například nasazení šablony s názvem **azuredeploy.jsv** vytvoří výchozí název nasazení **azuredeploy**.

Pro každý název nasazení je umístění neměnné. Nasazení nelze vytvořit v jednom umístění, pokud existuje existující nasazení se stejným názvem v jiném umístění. Pokud se zobrazí kód chyby `InvalidDeploymentLocation` , použijte jiný název nebo stejné umístění jako předchozí nasazení pro tento název.

## <a name="use-template-functions"></a>Použití funkcí šablon

Pro nasazení klientů existují při použití funkcí šablon důležité důležité informace:

* Funkce [Resource ()](template-functions-resource.md#resourcegroup) **není podporována.**
* Funkce [Subscription ()](template-functions-resource.md#subscription) **není podporována.**
* Funkce [Reference ()](template-functions-resource.md#reference) a [list ()](template-functions-resource.md#list) jsou podporovány.
* Nepoužívejte [ResourceID ()](template-functions-resource.md#resourceid) k získání ID prostředku pro prostředky, které jsou nasazeny na úrovni tenanta.

  Místo toho použijte funkci [tenantResourceId ()](template-functions-resource.md#tenantresourceid) .

  Pokud například chcete získat ID prostředku pro předdefinovanou definici zásady, použijte:

  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```

  ID vráceného prostředku má následující formát:

  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Vytvoření skupiny pro správu

[Následující šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/new-mg) vytvoří skupinu pro správu.

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

## <a name="assign-role"></a>Přiřazení role

[Následující šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/tenant-role-assignment) přiřadí roli v oboru tenanta.

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

* Další informace o přiřazování rolí najdete v tématu [Přidání přiřazení rolí Azure pomocí šablon Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Šablony můžete nasadit i na úrovni [předplatného](deploy-to-subscription.md) nebo [skupiny pro správu](deploy-to-management-group.md).
