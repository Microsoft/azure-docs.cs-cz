---
title: Spravovat řešení Azure pomocí Powershellu | Dokumentace Microsoftu
description: Ke správě prostředků pomocí Azure Powershellu a Resource Manageru.
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
ms.openlocfilehash: 5f7c569eabcf6e4b743f1b6616161787764e8f84
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723858"
---
# <a name="manage-resources-with-azure-powershell"></a>Správa prostředků pomocí Azure Powershellu

[!INCLUDE [Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, přečtěte si článek o [instalaci Azure PowerShellu](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="understand-scope"></a>Orientace v oborech

[!INCLUDE [Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

V tomto článku použít všechna nastavení správy do skupiny prostředků, můžete snadno odebrat tato nastavení po dokončení.

Pojďme vytvořit skupinu prostředků.

```azurepowershell-interactive
Set-AzureRmContext -Subscription <subscription-name>
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

V tuto chvíli je skupina prostředků prázdná.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>Přiřazení role

V tomto článku nasadíte virtuální počítač a jeho odpovídající virtuální síť. Pro správu řešení virtuálních počítačů existují v závislosti na prostředcích tři role, které poskytují běžný přístup:

* [Přispěvatel virtuálních počítačů](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Přispěvatel sítě](../role-based-access-control/built-in-roles.md#network-contributor)
* [Přispěvatel účtů úložiště](../role-based-access-control/built-in-roles.md#storage-account-contributor)

Místo přiřazení rolí jednotlivým uživatelům je často jednodušší [vytvořit skupinu Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) pro uživatele, kteří potřebují provádět podobné akce. Potom této skupině přiřaďte odpovídající role. Pro názornost tohoto článku stačí vytvořit skupinu Azure Active Directory bez členů. Této skupině můžete přiřadit role pro některý obor. 

Následující příklad vytvoří skupinu a přiřadí ji k roli Přispěvatel virtuálních počítačů pro skupinu prostředků. Ke spuštění `New-AzureAdGroup` příkaz, je nutné použít [Azure Cloud Shell](/azure/cloud-shell/overview) nebo [stažení modulu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Obvykle tento postup zopakujete pro role **Přispěvatel sítě** a **Přispěvatel účtů úložiště**, abyste zajistili přiřazení uživatelů ke správě nasazených prostředků. V tomto článku můžete tyto kroky vynechat.

## <a name="azure-policies"></a>Zásady Azure

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Použití zásad

Vaše předplatné už obsahuje několik definic zásad. Pokud chcete zobrazit dostupné definice zásad, použijte:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Zobrazí se definice existujících zásad. Typ zásad je buď **Předdefinované** nebo **Vlastní**. Najděte definice zásad popisující podmínku, kterou chcete přiřadit. V tomto článku můžete přiřadit zásady s těmito funkcemi:

* omezení umístění pro všechny prostředky
* omezit SKU virtuálních počítačů
* Auditovat virtuální počítače, které nepoužívají spravované disky

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

## <a name="deploy-the-virtual-machine"></a>Nasazení virtuálního počítače

Přiřadili jste role a zásady, takže jste připravení nasadit řešení. Výchozí velikost je Standard_DS1_v2, což je jedno z povolených SKU. Při spuštění tohoto kroku se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač.

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

Po dokončení nasazování můžete použít další nastavení pro správu řešení.

## <a name="lock-resources"></a>Uzamčení prostředků

[!INCLUDE [Resource Manager governance locks](../../includes/resource-manager-governance-locks.md)]

### <a name="lock-a-resource"></a>Uzamknout prostředek

Zamknout virtuálního počítače a skupiny zabezpečení sítě, použijte:

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

Virtuální počítač můžete odstranit pouze, pokud výslovně odebrat zámek proti. Tento krok najdete v části o [vyčištění prostředků](#clean-up-resources).

## <a name="tag-resources"></a>Označení prostředků

[!INCLUDE [Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>Označení prostředků

[!INCLUDE [Resource Manager governance tags Powershell](../../includes/resource-manager-governance-tags-powershell.md)]

Použití značek k virtuálnímu počítači, použijte:

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Hledání prostředků podle značky

K vyhledání prostředků pomocí značek název a hodnotu, použijte:

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

Vrácené hodnoty můžete použít pro úlohy správy, jako je zastavení všech virtuálních počítačů s určitou hodnotou značky.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Zobrazení nákladů podle hodnoty značky

Po použití značek k prostředkům, můžete zobrazit náklady na prostředky s těmito značkami. To chvíli trvat, než analýzu nákladů zobrazíte nejnovější využití, tak náklady na nemusíte vidět ještě. Když náklady jsou k dispozici, můžete zobrazit náklady na prostředky napříč skupinami prostředků ve vašem předplatném. Uživatelé musí mít [úrovně přístup k předplatnému na fakturační informace](../billing/billing-manage-access.md) zobrazíte náklady.

Chcete-li zobrazit náklady podle klíčových slov na portálu, vyberte své předplatné a vyberte **Cost Analysis**.

![Analýza nákladů](./media/powershell-azure-resource-manager/select-cost-analysis.png)

Potom vyfiltrovat hodnotu značky a vyberte **použít**.

![Zobrazení nákladů podle značky](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

Můžete také použít [API pro fakturaci Azure](../billing/billing-usage-rate-card-overview.md) programově zobrazit náklady.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Zamčená skupina zabezpečení sítě nejde odstranit, dokud neodstraníte zámek. Odebrat zámek, použijte:

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
* Další informace o monitorování virtuálních počítačů najdete v tématu [monitorovat a aktualizovat virtuální počítač Windows s Azure Powershellem](../virtual-machines/windows/tutorial-monitoring.md).
* Další informace o použití Azure Security Center k implementaci doporučené bezpečnostní postupy [monitorování zabezpečení virtuálních počítačů pomocí Azure Security Center](../virtual-machines/windows/tutorial-azure-security.md).
* Přesunutí stávajících prostředků do nové skupiny prostředků. Příklady najdete v tématu [přesun prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).
