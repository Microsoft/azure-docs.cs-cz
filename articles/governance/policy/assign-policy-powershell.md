---
title: 'Rychlý Start: nové přiřazení zásad pomocí PowerShellu'
description: V tomto rychlém startu použijete Azure PowerShell k vytvoření přiřazení Azure Policy k identifikaci prostředků, které nedodržují předpisy.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 3fd6060d1f38c523ccf22e80807f6220bfdf3cbc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978309"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí Azure PowerShell

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků. V tomto rychlém startu vytvoříte přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky. Po dokončení budete identifikovat virtuální počítače, které _nedodržují předpisy_.

Modul Azure PowerShell se používá ke správě prostředků Azure z příkazového řádku nebo ve skriptech.
V této příručce se dozvíte, jak pomocí funkce AZ Module vytvořit přiřazení zásady.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

- Než začnete, ujistěte se, že je nainstalovaná nejnovější verze Azure PowerShell. Podrobné informace najdete v tématu [instalace Azure PowerShell modulu](/powershell/azure/install-az-ps) .

- Zaregistrujte poskytovatele prostředků Azure Policy Insights pomocí Azure PowerShell. Registrace poskytovatele prostředků zajistí, že s ním vaše předplatné bude fungovat. Zaregistrovat poskytovatele prostředků, musíte mít oprávnění pro operace registrace poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Další informace o registraci a zobrazení poskytovatelů prostředků najdete v tématu [Poskytovatelé a typy prostředků](../../azure-resource-manager/management/resource-providers-and-types.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady pro definici _audit virtuálních počítačů bez spravovaných disků_ . Tato definice zásady identifikuje virtuální počítače, které nepoužívají spravované disky.

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
- **DisplayName** – zobrazovaný název přiřazení zásady. V tomto případě používáte _Audit virtuálních počítačů bez spravovaných disků přiřazení_.
- **Definition** – Definice zásady, kterou používáte k vytvoření tohoto přiřazení. V takovém případě je ID definice zásady _Audit virtuálních počítačů, které nepoužívají spravované disky_.
- **Scope** – Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Může sahat od předplatného až po skupinu prostředků. Nezapomeňte nahradit &lt;scope&gt; názvem vaší skupiny prostředků.

Nyní můžete identifikovat nekompatibilní prostředky, abyste pochopili stav dodržování předpisů vašeho prostředí.

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Pomocí následujících informací identifikujte prostředky, které nedodržují předpisy přiřazení zásady, kterou jste vytvořili. Spusťte následující příkazy:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Další informace o získání stavu zásad najdete v tématu [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

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

Výsledky se shodují s tím, co vidíte na kartě **Kompatibilita prostředků** v přiřazení zásady v zobrazení Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odebrat přiřazení vytvořené, použijte následující příkaz:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

Další informace o přiřazování zásad pro ověření, že nové prostředky jsou kompatibilní, i nadále najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)