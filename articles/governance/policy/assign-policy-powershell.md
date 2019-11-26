---
title: 'Quickstart: New policy assignment with PowerShell'
description: In this quickstart, you use Azure PowerShell to create an Azure Policy assignment to identify non-compliant resources.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 3ce823a7abfe16e4433128dcdfe073dfcfaeba50
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482388"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Quickstart: Create a policy assignment to identify non-compliant resources using Azure PowerShell

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků. In this quickstart, you create a policy assignment to identify virtual machines that aren't using managed disks. When complete, you'll identify virtual machines that are _non-compliant_.

The Azure PowerShell module is used to manage Azure resources from the command line or in scripts.
This guide explains how to use Az module to create a policy assignment.

## <a name="prerequisites"></a>Předpoklady

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

- Before you start, make sure that the latest version of Azure PowerShell is installed. See [Install Azure PowerShell module](/powershell/azure/install-az-ps) for detailed information.

- Register the Azure Policy Insights resource provider using Azure PowerShell. Registrace poskytovatele prostředků zajistí, že s ním vaše předplatné bude fungovat. To register a resource provider, you must have permission to the register resource provider operation. Tato operace je součástí rolí Přispěvatel a Vlastník. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Další informace o registraci a zobrazení poskytovatelů prostředků najdete v tématu [Poskytovatelé a typy prostředků](../../azure-resource-manager/resource-manager-supported-services.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

In this quickstart, you create a policy assignment for the _Audit VMs without managed disks_ definition. This policy definition identifies virtual machines not using managed disks.

Spuštěním následujících příkazů vytvořte nové přiřazení zásady:

```azurepowershell-interactive
# Get a reference to the resource group that will be the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition that will be assigned
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Předchozí příkazy používají následující informace:

- **Name** – skutečný název přiřazení. V tomto příkladu je použitý název _audit-vm-manageddisks_.
- **DisplayName** – zobrazovaný název přiřazení zásady. In this case, you're using _Audit VMs without managed disks Assignment_.
- **Definition** – Definice zásady, kterou používáte k vytvoření tohoto přiřazení. In this case, it's the ID of policy definition _Audit VMs that do not use managed disks_.
- **Scope** – Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Může sahat od předplatného až po skupinu prostředků. Nezapomeňte nahradit &lt;scope&gt; názvem vaší skupiny prostředků.

You're now ready to identify non-compliant resources to understand the compliance state of your environment.

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Pomocí následujících informací identifikujte prostředky, které nedodržují předpisy přiřazení zásady, kterou jste vytvořili. Spusťte následující příkazy:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

For more information about getting policy state, see [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

Vaše výsledky budou vypadat přibližně jako v následujícím příkladu:

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

The results match what you see in the **Resource compliance** tab of a policy assignment in the Azure portal view.

## <a name="clean-up-resources"></a>Vyčištění prostředků

To remove the assignment created, use the following command:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

To learn more about assigning policies to validate that new resources are compliant, continue to the tutorial for:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)