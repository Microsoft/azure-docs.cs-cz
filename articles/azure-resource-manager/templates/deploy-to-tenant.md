---
title: Nasazení prostředků do tenanta
description: Popisuje postup nasazení prostředků v oboru tenanta v šabloně Azure Resource Manager.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: fd5a9ae60c578a3be7f70d82baae0a15e406b9db
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491482"
---
# <a name="tenant-deployments-with-arm-templates"></a>Nasazení klientů pomocí šablon ARM

V případě, že vaše organizace bude vyspělá, možná budete muset v tenantovi Azure AD definovat a přiřazovat [zásady](../../governance/policy/overview.md) nebo [řízení přístupu na základě role Azure (RBAC)](../../role-based-access-control/overview.md) . Pomocí šablon na úrovni tenanta můžete deklarativně uplatňovat zásady a přiřazovat role na globální úrovni.

## <a name="supported-resources"></a>Podporované prostředky

Ne všechny typy prostředků se dají nasadit na úrovni tenanta. V této části jsou uvedeny typy prostředků, které jsou podporovány.

Pro zásady Azure použijte:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Pro řízení přístupu na základě role Azure (Azure RBAC) použijte:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

U vnořených šablon, které se nasazují do skupin pro správu, předplatných nebo skupin prostředků, použijte:

* [nasazení](/azure/templates/microsoft.resources/deployments)

Pro vytváření skupin pro správu použijte:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Pro vytváření předplatných použijte:

* [hromad](/azure/templates/microsoft.subscription/aliases)

Pro správu nákladů použijte:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [pokynů](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

Pro konfiguraci portálu použijte:

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

## <a name="schema"></a>Schéma

Schéma, které používáte pro nasazení klientů, se liší od schématu pro nasazení skupin prostředků.

Pro šablony použijte:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
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

## <a name="deployment-commands"></a>Příkazy nasazení

Příkazy pro nasazení klientů se liší od příkazů pro nasazení skupin prostředků.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pro rozhraní příkazového řádku Azure CLI použijte [AZ Deployment tenant Create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pro Azure PowerShell použijte [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
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

Pro nasazení na úrovni tenanta musíte zadat umístění pro nasazení. Umístění nasazení je oddělené od umístění prostředků, které nasazujete. Umístění nasazení určuje, kam se mají ukládat data nasazení. Nasazení a [skupiny pro správu](deploy-to-management-group.md) [předplatného](deploy-to-subscription.md) také vyžadují umístění. Pro nasazení [skupin prostředků](deploy-to-resource-group.md) se umístění skupiny prostředků používá k ukládání dat nasazení.

Můžete zadat název nasazení nebo použít výchozí název nasazení. Výchozí název je název souboru šablony. Například nasazení šablony s názvem _azuredeploy.jsv_ vytvoří výchozí název nasazení **azuredeploy**.

Pro každý název nasazení je umístění neměnné. Nasazení nelze vytvořit v jednom umístění, pokud existuje existující nasazení se stejným názvem v jiném umístění. Pokud například vytvoříte nasazení tenanta s názvem **deployment1** v **centralus**, nemůžete později vytvořit další nasazení s názvem **deployment1** , ale umístěním **westus**. Pokud se zobrazí kód chyby `InvalidDeploymentLocation` , použijte jiný název nebo stejné umístění jako předchozí nasazení pro tento název.

## <a name="deployment-scopes"></a>Obory nasazení

Při nasazování do tenanta můžete prostředky nasadit do:

* tenant
* skupiny pro správu v rámci tenanta
* odběru
* skupinám prostředků

[Prostředek rozšíření](scope-extension-resources.md) může být vymezen na cíl, který se liší od cíle nasazení.

Uživatel, který šablonu nasazuje, musí mít přístup k zadanému oboru.

V této části se dozvíte, jak zadat různé obory. Tyto různé obory můžete kombinovat v jediné šabloně.

### <a name="scope-to-tenant"></a>Rozsah do tenanta

Prostředky definované v části Resources v šabloně se aplikují na tenanta.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Rozsah pro skupinu pro správu

Chcete-li cílit na skupinu pro správu v rámci tenanta, přidejte vnořené nasazení a zadejte `scope` vlastnost.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Rozsah pro předplatné

Můžete také cílit na odběry v rámci tenanta. Uživatel, který šablonu nasazuje, musí mít přístup k zadanému oboru.

Chcete-li cílit na předplatné v rámci tenanta, použijte vnořené nasazení a `subscriptionId` vlastnost.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Rozsah do skupiny prostředků

V rámci tenanta můžete také cílit na skupiny prostředků. Uživatel, který šablonu nasazuje, musí mít přístup k zadanému oboru.

Pokud chcete cílit na skupinu prostředků v rámci tenanta, použijte vnořené nasazení. Nastavte `subscriptionId` vlastnosti a `resourceGroup` . Nenastavte umístění pro vnořené nasazení, protože je nasazené v umístění skupiny prostředků.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-rg.json" highlight="9,10,18":::

## <a name="create-management-group"></a>Vytvoření skupiny pro správu

Následující šablona vytvoří skupinu pro správu.

:::code language="json" source="~/quickstart-templates/tenant-deployments/new-mg/azuredeploy.json":::

Pokud váš účet nemá oprávnění k nasazení do tenanta, můžete přesto vytvořit skupiny pro správu nasazením do jiného oboru. Další informace najdete v tématu [skupina pro správu](deploy-to-management-group.md#management-group).

## <a name="assign-role"></a>Přiřazení role

Následující šablona přiřadí roli v oboru tenanta.

:::code language="json" source="~/quickstart-templates/tenant-deployments/tenant-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Další kroky

* Další informace o přiřazování rolí najdete v tématu [Přidání přiřazení rolí Azure pomocí šablon Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Šablony můžete nasadit i na úrovni [předplatného](deploy-to-subscription.md) nebo [skupiny pro správu](deploy-to-management-group.md).
