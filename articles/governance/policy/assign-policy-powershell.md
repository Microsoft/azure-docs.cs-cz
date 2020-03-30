---
title: 'Úvodní příručka: Nové přiřazení zásad pomocí PowerShellu'
description: V tomto rychlém startu použijete Azure PowerShell k vytvoření přiřazení zásad Azure k identifikaci nekompatibilních prostředků.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 3fd6060d1f38c523ccf22e80807f6220bfdf3cbc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75978309"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Úvodní příručka: Vytvoření přiřazení zásad k identifikaci nekompatibilních prostředků pomocí Azure PowerShellu

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků. V tomto rychlém startu vytvoříte přiřazení zásad k identifikaci virtuálních počítačů, které nepoužívají spravované disky. Po dokončení identifikujete virtuální počítače, které _nejsou kompatibilní_.

Modul Azure PowerShell se používá ke správě prostředků Azure z příkazového řádku nebo ve skriptech.
Tato příručka vysvětluje, jak pomocí modulu Az vytvořit přiřazení zásad.

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

- Než začnete, ujistěte se, že je nainstalovaná nejnovější verze Prostředí Azure PowerShell. Podrobné informace [najdete v tématu Instalace modulu Azure PowerShell.](/powershell/azure/install-az-ps)

- Zaregistrujte poskytovatele prostředků Azure Policy Insights pomocí Azure PowerShellu. Registrace poskytovatele prostředků zajistí, že s ním vaše předplatné bude fungovat. Chcete-li zaregistrovat zprostředkovatele prostředků, musíte mít oprávnění k operaci poskytovatele prostředků registru. Tato operace je součástí rolí Přispěvatel a Vlastník. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Další informace o registraci a zobrazení zprostředkovatelů prostředků naleznete v tématu [Zprostředkovatelé a typy prostředků](../../azure-resource-manager/management/resource-providers-and-types.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásad pro auditovat virtuální počítače bez definice _spravovaných disků._ Tato definice zásad identifikuje virtuální počítače, které nepoužívají spravované disky.

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
- **DisplayName** – zobrazovaný název přiřazení zásady. V takovém případě používáte _auditovat virtuální počítače bez přiřazení spravovaných disků_.
- **Definition** – Definice zásady, kterou používáte k vytvoření tohoto přiřazení. V tomto případě je to ID definice zásad _auditní virtuální počítače, které nepoužívají spravované disky_.
- **Scope** – Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Může sahat od předplatného až po skupinu prostředků. Nezapomeňte nahradit &lt;scope&gt; názvem vaší skupiny prostředků.

Nyní jste připraveni identifikovat nekompatibilní prostředky, abyste pochopili stav dodržování předpisů vašeho prostředí.

## <a name="identify-non-compliant-resources"></a>Identifikace prostředků, které nedodržují předpisy

Pomocí následujících informací identifikujte prostředky, které nedodržují předpisy přiřazení zásady, kterou jste vytvořili. Spusťte následující příkazy:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Další informace o získání stavu zásad naleznete v [tématu Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

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

Výsledky odpovídají tomu, co vidíte na kartě **Dodržování předpisů prostředků** přiřazení zásad v zobrazení portálu Azure.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat vytvořenou úlohu, použijte následující příkaz:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

Další informace o přiřazení zásad k ověření, že nové prostředky jsou kompatibilní, pokračujte v kurzu pro:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)