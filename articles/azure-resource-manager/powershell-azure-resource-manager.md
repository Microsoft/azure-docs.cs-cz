---
title: Spravovat řešení Azure pomocí prostředí PowerShell | Microsoft Docs
description: Ke správě prostředků pomocí Azure PowerShell a správce prostředků.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: tomfitz
ms.openlocfilehash: 13e5836aea0e307cdce5bcdcd5cf3c50969dfbf8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="manage-resources-with-azure-powershell"></a>Správa prostředků pomocí Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a místně pomocí prostředí PowerShell najdete [modul nainstalovat Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="understand-scope"></a>Pochopení oboru

[!INCLUDE [Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

V tomto článku použít, můžete snadno odebrat tato nastavení po dokončení všech nastavení pro správu do skupiny prostředků.

Umožňuje vytvořit skupinu prostředků.

```azurepowershell-interactive
Set-AzureRmContext -Subscription <subscription-name>
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

V současné době skupina prostředků je prázdný.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>Přiřazení role

V tomto článku nasaďte virtuální počítač a jeho odpovídající virtuální síť. Pro správu řešení virtuálního počítače, existují tři role konkrétní prostředky, které poskytují běžně potřebné přístup:

* [Přispěvatel virtuálních počítačů](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Přispěvatel sítě](../role-based-access-control/built-in-roles.md#network-contributor)
* [Přispěvatel účtu úložiště](../role-based-access-control/built-in-roles.md#storage-account-contributor)

Místo přiřazení rolí pro jednotlivé uživatele, je často jednodušší [vytvoření skupiny Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md) pro uživatele, kteří potřebují k provádění podobných akcí. Potom přiřaďte této skupině do odpovídající role. Ke zjednodušení tohoto článku, vytvořit skupinu služby Azure Active Directory bez členů. Tato skupina stále můžete přiřadit do rolí pro obor. 

Následující příklad vytvoří skupinu a přiřadí ji k roli Přispěvatel virtuálních počítačů pro skupinu prostředků. Ke spuštění `New-AzureAdGroup` příkaz, musíte buď použijte [prostředí cloudu Azure](/azure/cloud-shell/overview) nebo [stáhnout modulu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Obvykle, opakujte tento postup pro **Přispěvatel sítě** a **Přispěvatel účet úložiště** a ujistěte se, uživatelé budou přiřazení ke správě nasazené prostředky. V tomto článku můžete přeskočit těchto kroků.

## <a name="azure-policies"></a>Azure zásady

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Pomocí zásad

Vaše předplatné už má několik definice zásady. Pokud chcete zobrazit definice dostupné zásady, použijte:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Zobrazí existující definice zásady. Typ zásad je buď **BuiltIn** nebo **vlastní**. Projděte definice pro šablony, které popisují podmínku chcete přiřadit. V tomto článku můžete přiřadit zásady který:

* omezení umístění pro všechny prostředky
* omezit SKU pro virtuální počítače
* audit virtuálních počítačů, které nepoužívají spravované disky

```azurepowershell-interactive
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

$rg = Get-AzureRmResourceGroup -Name myResourceGroup

$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Nasaďte virtuální počítač

Přiřadili jste role a zásady, takže jste připravení nasadit řešení. Výchozí velikost je Standard_DS1_v2, což je jedno z vaší povolená SKU. Při spuštění tohoto kroku se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač.

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

Po dokončení nasazení můžete použít další nastavení správy k řešení.

## <a name="lock-resources"></a>Uzamčení prostředků

[!INCLUDE [Resource Manager governance locks](../../includes/resource-manager-governance-locks.md)]

### <a name="lock-a-resource"></a>Zamknout prostředku

Chcete-li uzamčení virtuálního počítače a skupiny zabezpečení sítě, použijte:

```azurepowershell-interactive
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Virtuální počítač se dá odstranit jenom Pokud konkrétně odebrat zámek. Tento krok se zobrazí v [vyčištění prostředků](#clean-up-resources).

## <a name="tag-resources"></a>Značka prostředky

[!INCLUDE [Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>Značka prostředky

[!INCLUDE [Resource Manager governance tags Powershell](../../includes/resource-manager-governance-tags-powershell.md)]

Použití značek k virtuálnímu počítači, použijte:

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Najít prostředky podle značky

K vyhledání prostředků s názvem značky a hodnoty, použijte:

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

Pro úlohy správy, jako zastavení všechny virtuální počítače s hodnotou značky můžete použít vrácené hodnoty.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Zobrazení nákladů podle hodnoty značky

Po použití značek k prostředkům, můžete zobrazit náklady na prostředky s těmito značkami. Pro analýzu náklady zobrazíte nejnovější využití, se nemusí zobrazovat náklady na ještě chvíli trvá. Když náklady jsou k dispozici, můžete zobrazit náklady pro prostředky ve skupinách prostředků ve vašem předplatném. Uživatelé musí mít [přístupu na úrovni předplatného na fakturační informace](../billing/billing-manage-access.md) zobrazení nákladů.

Chcete-li zobrazit náklady podle značky na portálu, vyberte své předplatné a vyberte **analýza nákladů**.

![Analýza nákladů](./media/powershell-azure-resource-manager/select-cost-analysis.png)

Potom filtrovat podle hodnoty značky a vyberte **použít**.

![Náklady na zobrazení značky](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

Můžete také [rozhraní API Správce Azure fakturace](../billing/billing-usage-rate-card-overview.md) prostřednictvím kódu programu zobrazíte náklady.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Skupina zabezpečení sítě uzamčeném nejde odstranit, dokud se odebere ze zařízení zámek. Chcete-li odebrat zámek, použijte:

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další postup
* Další informace o monitorování virtuálních počítačů najdete v tématu [monitorovat a aktualizovat virtuální počítač Windows v prostředí Azure PowerShell](../virtual-machines/windows/tutorial-monitoring.md).
* Další informace o použití Azure Security Center k implementaci zabezpečení doporučené postupy [monitorovat zabezpečení virtuálního počítače pomocí Azure Security Center](../virtual-machines/windows/tutorial-azure-security.md).
* Existující prostředky můžete přesunout do nové skupiny prostředků. Příklady najdete v tématu [přesunutí prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](resource-manager-subscription-governance.md).
