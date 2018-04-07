---
title: Řídí virtuální počítače Azure pomocí prostředí Azure PowerShell | Microsoft Docs
description: Kurz – spravovat virtuální počítače Azure s použitím RBAC, zásady, zámků a značky s prostředím Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 30f5fe83c46f2dbe1933e8347242be7fbb30a3e3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="virtual-machine-governance-with-azure-powershell"></a>Zásady správného řízení virtuální počítač s prostředím Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a místně pomocí prostředí PowerShell najdete [modul nainstalovat Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure. Pro místní instalace, musíte také [stáhnout modulu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/) k vytvoření nové skupiny Azure Active Directory.

## <a name="understand-scope"></a>Pochopení oboru

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

V tomto kurzu použít, můžete snadno odebrat tato nastavení po dokončení všech nastavení pro správu do skupiny prostředků.

Umožňuje vytvořit příslušné skupině prostředků.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

V současné době skupina prostředků je prázdný.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Chcete Ujistěte se, že uživatelé ve vaší organizaci mají správnou úroveň přístupu na tyto prostředky. Nechcete, aby uživatelům udělit neomezený přístup, ale musíte také zkontrolujte, zda že mohou provádět práci. [Řízení přístupu na základě role](../../active-directory/role-based-access-control-what-is.md) můžete spravovat uživatele, kteří mají oprávnění k dokončení určitých akcí u oboru.

Vytvoření a odeberte přiřazení rolí, uživatelé musí mít `Microsoft.Authorization/roleAssignments/*` přístup. Tento přístup je poskytována prostřednictvím role vlastníka nebo správce uživatelského přístupu.

Pro správu řešení virtuálního počítače, existují tři role konkrétní prostředky, které poskytují běžně potřebné přístup:

* [Přispěvatel virtuálních počítačů](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [Přispěvatel sítě](../../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [Přispěvatel účtu úložiště](../../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

Místo přiřazení rolí pro jednotlivé uživatele, je často jednodušší [vytvoření skupiny Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) pro uživatele, kteří potřebují k provádění podobných akcí. Potom přiřaďte této skupině do odpovídající role. Ke zjednodušení tohoto článku, vytvořit skupinu služby Azure Active Directory bez členů. Tato skupina stále můžete přiřadit do rolí pro obor. 

Následující příklad vytvoří skupinu Azure Active Directory s názvem *VMDemoContributors* s e-mailu Přezdívka *vmDemoGroup*. E-mailu Přezdívka slouží jako alias pro skupinu.

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
```

Jak dlouho trvá chvíli po příkazového řádku vrátí pro skupinu rozšíří v rámci Azure Active Directory. Po 20 nebo 30 sekund čekání, použijte [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) příkazu přiřaďte nové skupiny Azure Active Directory k roli Přispěvatel virtuálních počítačů pro skupinu prostředků.  Pokud předtím, než se rozšíří, spusťte následující příkaz, zobrazí se chybová zpráva, **hlavní <guid> neexistuje v adresáři**. Zkuste znovu spustit příkaz.

```azurepowershell-interactive
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Obvykle, opakujte tento postup pro *Přispěvatel sítě* a *Přispěvatel účet úložiště* a ujistěte se, uživatelé budou přiřazení ke správě nasazené prostředky. V tomto článku můžete přeskočit těchto kroků.

## <a name="azure-policies"></a>Azure zásady

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Pomocí zásad

Vaše předplatné už má několik definice zásady. Pokud chcete zobrazit definice dostupné zásady, použijte [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) příkaz:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Zobrazí existující definice zásady. Typ zásad je buď **BuiltIn** nebo **vlastní**. Projděte definice pro šablony, které popisují podmínku chcete přiřadit. V tomto článku můžete přiřadit zásady který:

* Omezení umístění pro všechny prostředky.
* Omezte SKU pro virtuální počítače.
* Audit virtuálních počítačů, které nepoužívají spravované disky.

V následujícím příkladu načíst tři definice zásady založené na zobrazovaný název. Můžete použít [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) příkazu přiřaďte tyto definice do skupiny prostředků. Pro některé zásady zadejte hodnoty parametrů k určení povolených hodnot.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzureRmResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
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

[Uzamčení prostředků](../../azure-resource-manager/resource-group-lock-resources.md) zabránit uživatelům ve vaší organizaci neúmyslnému odstranění nebo úprava důležitých prostředků. Na rozdíl od řízení přístupu na základě rolí uzamčení prostředků platí omezení ve všech uživatelů a rolí. Můžete nastavit zámek na úrovni *CanNotDelete* nebo *jen pro čtení*.

Pokud chcete zamknout virtuálního počítače a skupiny zabezpečení sítě, použijte [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock) příkaz:

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

K testování zámky, spusťte následující příkaz:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Zobrazí chyba s oznámením, že operaci odstranění nelze provést z důvodu zámek. Skupina prostředků lze odstranit pouze pokud odeberete konkrétně zámky. Tento krok se zobrazí v [vyčištění prostředků](#clean-up-resources).

## <a name="tag-resources"></a>Značka prostředky

Použijete [značky](../../azure-resource-manager/resource-group-using-tags.md) vaše prostředky Azure logicky uspořádat podle kategorií. Každá značka se skládá z názvu a hodnoty. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Pro použití značek k virtuálnímu počítači, použijte [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) příkaz:

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Najít prostředky podle značky

K vyhledání prostředků s názvem značky a hodnoty, použijte [najít AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) příkaz:

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

Pro úlohy správy, jako zastavení všechny virtuální počítače s hodnotou značky můžete použít vrácené hodnoty.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Zobrazení nákladů podle hodnoty značky

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Skupina zabezpečení sítě uzamčeném nejde odstranit, dokud se odebere ze zařízení zámek. Chcete-li odebrat zámek, použijte [odebrat AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock) příkaz:

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

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili vlastní image virtuálního počítače. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Přiřazení uživatele k roli
> * Použít zásady, které vynucují standardy
> * Chránit důležité prostředky s zámky.
> * Označit prostředky pro správu a fakturaci

Přechodu na další informace o tom, jak vysoce dostupné virtuální počítače v dalším kurzu.

> [!div class="nextstepaction"]
> [Monitorování virtuálních počítačů](tutorial-monitoring.md)

