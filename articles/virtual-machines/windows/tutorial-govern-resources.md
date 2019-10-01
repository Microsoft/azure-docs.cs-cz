---
title: Kurz – Správa virtuálních počítačů Azure pomocí Azure PowerShell | Microsoft Docs
description: V tomto kurzu se naučíte používat Azure PowerShell ke správě virtuálních počítačů Azure pomocí nastavení RBAC, policies, zámků a značek.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: gwallace
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: cb0050f79f2f6a38b9cd58ee87aa88899f0f5a15
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71674995"
---
# <a name="tutorial-learn-about-windows-virtual-machine-management-with-azure-powershell"></a>Kurz: informace o správě virtuálních počítačů s Windows pomocí Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

## <a name="launch-azure-cloud-shell"></a>Spustit Azure Cloud Shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Má společné nástroje Azure, které jsou předinstalované a nakonfigurované pro použití s vaším účtem. 

Chcete-li otevřít Cloud Shell, stačí vybrat příkaz **vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Vyberte **Kopírovat** pro zkopírování bloků kódu, vložení do Cloud Shell a stisknutím klávesy ENTER ji spusťte.

## <a name="understand-scope"></a>Pochopení oboru

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

V tomto kurzu použijete všechna nastavení správy na skupinu prostředků, abyste mohli tato nastavení po dokončení snadno odebrat.

Pojďme tuto skupinu prostředků vytvořit.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

V současné době je skupina prostředků prázdná.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Chcete zajistit, aby uživatelé ve vaší organizaci měli správnou úroveň přístupu k těmto prostředkům. Nechcete uživatelům udělit neomezený přístup, ale musíte také zajistit, aby mohli provádět své práce. [Řízení přístupu na základě rolí](../../role-based-access-control/overview.md) umožňuje spravovat uživatele, kteří mají oprávnění k provádění konkrétních akcí v oboru.

Aby uživatelé mohli vytvořit a odebrat přiřazení rolí, musí mít přístup `Microsoft.Authorization/roleAssignments/*`. Tento přístup je udělen prostřednictvím rolí vlastník nebo správce přístupu uživatelů.

Pro správu řešení virtuálních počítačů jsou k dispozici tři role specifické pro prostředky, které poskytují běžně potřebný přístup:

* [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Přispěvatel sítě](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Přispěvatel účtu úložiště](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Místo přiřazování rolí jednotlivým uživatelům je často snazší použít Azure Active Directory skupinu s uživateli, kteří potřebují provést podobné akce. Pak tuto skupinu přiřaďte k příslušné roli. V tomto článku buď použijte existující skupinu pro správu virtuálního počítače, nebo [vytvořte skupinu Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)pomocí portálu.

Po vytvoření nové skupiny nebo vyhledání existující skupiny použijte příkaz [New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment) a přiřaďte skupinu Azure Active Directory k roli Přispěvatel virtuálních počítačů pro skupinu prostředků.  

```azurepowershell-interactive
$adgroup = Get-AzADGroup -DisplayName <your-group-name>

New-AzRoleAssignment -ObjectId $adgroup.id `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Pokud se zobrazí chyba oznamující, že **objekt zabezpečení \<guid > v adresáři neexistuje**, nová skupina se v celém Azure Active Directory nerozšíří. Zkuste znovu spustit příkaz.

Obvykle tento proces zopakujete pro přispěvatele *sítě* a *přispěvatele účtu úložiště* , abyste se ujistili, že uživatelé mají přiřazenou správu nasazených prostředků. V tomto článku můžete tyto kroky přeskočit.

## <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../governance/policy/overview.md) vám pomůže zajistit, že všechny prostředky v předplatném splňují podnikové standardy. Vaše předplatné už má několik definic zásad. Dostupné definice zásad zobrazíte pomocí příkazu [Get-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/Get-AzPolicyDefinition) :

```azurepowershell-interactive
(Get-AzPolicyDefinition).Properties | Format-Table displayName, policyType
```

Zobrazí se existující definice zásad. Typ zásady je buď **Builtin** , nebo **vlastní**. Prohlédněte si definice pro ty, které popisují podmínku, kterou chcete přiřadit. V tomto článku přiřadíte zásady, které:

* Omezte umístění pro všechny prostředky.
* Omezte počet SKU pro virtuální počítače.
* Auditujte virtuální počítače, které nepoužívají spravované disky.

V následujícím příkladu načtete tři definice zásad na základě zobrazovaného názvu. K přiřazení těchto definic do skupiny prostředků použijte příkaz [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment) . U některých zásad poskytujete hodnoty parametrů pro určení povolených hodnot.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Nasazení virtuálního počítače

Máte přiřazené role a zásady, takže jste připravení nasadit své řešení. Výchozí velikost je Standard_DS1_v2, což je jedna z vašich povolených SKU. Při spuštění tohoto kroku budete vyzváni k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač.

```azurepowershell-interactive
New-AzVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

Po dokončení nasazení můžete v řešení použít další nastavení správy.

## <a name="lock-resources"></a>Uzamčení prostředků

[Zámky prostředků](../../azure-resource-manager/resource-group-lock-resources.md) zabraňují uživatelům ve vaší organizaci v neúmyslném odstranění nebo úpravě důležitých prostředků. Na rozdíl od řízení přístupu na základě rolí používají zámky prostředků omezení napříč všemi uživateli a rolemi. Úroveň zámku můžete nastavit na *CanNotDelete* nebo *jen pro čtení*.

Chcete-li zamknout virtuální počítač a skupinu zabezpečení sítě, použijte příkaz [New-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock) :

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Chcete-li otestovat zámky, zkuste spustit následující příkaz:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup
```

Zobrazí se chyba oznamující, že operaci odstranění nelze dokončit z důvodu zámku. Skupinu prostředků lze odstranit pouze v případě, že zámky odstraníte výslovně. Tento krok se zobrazuje v [vyčištění prostředků](#clean-up-resources).

## <a name="tag-resources"></a>Označení prostředků

[Značky](../../azure-resource-manager/resource-group-using-tags.md) můžete použít pro prostředky Azure, abyste je logicky uspořádali podle kategorií. Každá značka se skládá z názvu a hodnoty. Můžete například použít název "prostředí" a hodnotu "produkce" na všechny prostředky v produkčním prostředí.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Pokud chcete použít značky na virtuálním počítači, použijte příkaz [set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) :

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Najít prostředky podle značky

Chcete-li najít prostředky s názvem a hodnotou značky, použijte příkaz [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) :

```azurepowershell-interactive
(Get-AzResource -Tag @{ Environment="Test"}).Name
```

Můžete použít vrácené hodnoty pro úlohy správy, jako je například zastavení všech virtuálních počítačů s hodnotou značky.

```azurepowershell-interactive
Get-AzResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzVM
```

### <a name="view-costs-by-tag-values"></a>Zobrazit náklady podle hodnot značek

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Uzamčenou skupinu zabezpečení sítě nelze odstranit, dokud se zámek neodebere. Chcete-li odebrat zámek, použijte příkaz [Remove-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcelock) :

```azurepowershell-interactive
Remove-AzResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili vlastní image virtuálního počítače. Zjistili jste, jak:

> [!div class="checklist"]
> * Přiřazení uživatelů k roli
> * Použití zásad, které vynutily standardy
> * Ochrana kritických prostředků pomocí zámků
> * Označení prostředků pro fakturaci a správu

Přejděte k dalšímu kurzu, kde se dozvíte, jak identifikovat změny a spravovat aktualizace balíčků na virtuálním počítači se systémem Linux.

> [!div class="nextstepaction"]
> [Správa virtuálních počítačů](tutorial-config-management.md)

