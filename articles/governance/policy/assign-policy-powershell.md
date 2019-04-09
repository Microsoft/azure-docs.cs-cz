---
title: Vytvoření zásad pro nekompatibilní prostředky pomocí Azure Powershellu
description: Použití Azure Powershellu k vytvoření přiřazení Azure Policy pro identifikaci prostředků, které nedodržují předpisy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: f07a760153c4ec15cec5c5bc1348e23152c59cb8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258330"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Vytvoření přiřazení zásady pro identifikaci neodpovídajících prostředků pomocí Azure Powershellu

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků. V tomto rychlém startu vytvoříte přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky. Jakmile budete hotovi, budete identifikovat virtuální počítače, které jsou *neodpovídajících*.

Modul Azure PowerShell slouží ke správě prostředků Azure z příkazového řádku nebo ve skriptech.
Tato příručka vysvětluje, jak modul Az slouží k vytvoření přiřazení zásady.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Než začnete, ujistěte se, že je nainstalovaná nejnovější verze prostředí Azure PowerShell. Zobrazit [instalace modulu Azure PowerShell](/powershell/azure/install-az-ps) podrobné informace.
- Pomocí Azure PowerShellu zaregistrujte poskytovatele prostředků Policy Insights. Registrace poskytovatele prostředků zajistí, že s ním vaše předplatné bude fungovat. Zaregistrovat poskytovatele prostředků, musíte mít oprávnění pro operace registrace poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Další informace o registraci a zobrazení poskytovatelů prostředků najdete v tématu [Poskytovatelé a typy prostředků](../../azure-resource-manager/resource-manager-supported-services.md).

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady pro *Audit virtuálních počítačů bez spravovaných disků* definice. Tato definice zásady identifikuje virtuální počítače bez použití spravovaných disků.

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

- **Name** – skutečný název přiřazení. V tomto příkladu je použitý název *audit-vm-manageddisks*.
- **DisplayName** – zobrazovaný název přiřazení zásady. V tomto případě používáte *Audit virtuálních počítačů bez spravovaných disků přiřazení*.
- **Definition** – Definice zásady, kterou používáte k vytvoření tohoto přiřazení. V takovém případě je ID definice zásady *Audit virtuálních počítačů, které nepoužívají spravované disky*.
- **Scope** – Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Může sahat od předplatného až po skupinu prostředků. Nezapomeňte nahradit &lt;scope&gt; názvem vaší skupiny prostředků.

Nyní jste připraveni identifikovat prostředky, které nedodržují předpisy, abyste porozuměli stavu dodržování předpisů ve vašem prostředí.

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Pomocí následujících informací identifikujte prostředky, které nedodržují předpisy přiřazení zásady, kterou jste vytvořili. Spusťte následující příkazy:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Další informace o tom, jak zásady stavu, najdete v části [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

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

Výsledky splňují, co se zobrazí v **dodržování předpisů prostředkem** kartě přiřazení zásady v zobrazení Azure portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odebrat přiřazení vytvořené, použijte následující příkaz:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

Další informace o přiřazování zásad pro ověření, že nové prostředky jsou kompatibilní, i nadále najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a Správa zásad](./tutorials/create-and-manage.md)