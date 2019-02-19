---
title: Kurz – Správa virtuálních počítačů Azure pomocí Azure Powershellu | Dokumentace Microsoftu
description: V tomto kurzu zjistíte, jak pomocí Azure PowerShellu spravovat virtuální počítače Azure s využitím RBAC, zásad, zámků a značek.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: baa8be0a7da2b8f56c334f14fcdb15da0a406e15
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341823"
---
# <a name="tutorial-learn-about-windows-virtual-machine-management-with-azure-powershell"></a>Kurz: Další informace o správě virtuálního počítače Windows pomocí Azure Powershellu

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="understand-scope"></a>Orientace v oborech

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

V tomto kurzu se nastavení pro správu aplikují na skupinu prostředků, takže je po skončení můžete snadno odebrat.

Vytvoříme skupinu prostředků.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

V tuto chvíli je skupina prostředků prázdná.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Potřebujete zajistit, aby uživatelé ve vaší organizaci měli správnou úroveň přístupu k těmto prostředkům. Nechcete uživatelům dát neomezený přístup, ale zároveň jim potřebujete umožnit dělat svou práci. Pomocí [řízení přístupu na základě rolí](../../role-based-access-control/overview.md) můžete spravovat oprávnění uživatelů k provádění určitých akcí v daném oboru.

K vytváření a odebírání přiřazení rolí musí mít uživatelé přístup `Microsoft.Authorization/roleAssignments/*`. Tento přístup se poskytuje prostřednictvím role vlastníka nebo správce uživatelských přístupů.

Pro správu řešení virtuálních počítačů existují v závislosti na prostředcích tři role, které poskytují běžný přístup:

* [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Přispěvatel sítě](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Přispěvatel účtů úložiště](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Místo přiřazování rolí jednotlivým uživatelům je často jednodušší použít skupinu Azure Active Directory obsahující uživatele, kteří potřebují provádět podobné akce. Potom této skupině přiřaďte odpovídající role. Pro účely tohoto článku použijte buď existující skupinu pro správu virtuálního počítače, nebo pomocí portálu [vytvořte skupinu Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Po vytvoření nové skupiny nebo vyhledání existujícího jeden, použijte [New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment) příkazu přiřaďte skupiny Azure Active Directory k roli Přispěvatel virtuálních počítačů pro skupinu prostředků.  

```azurepowershell-interactive
$adgroup = Get-AzADGroup -DisplayName <your-group-name>

New-AzRoleAssignment -ObjectId $adgroup.id `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Pokud se zobrazí chyba s oznámením, že **objekt zabezpečení <guid> v adresáři neexistuje**, znamená to, že se nová skupina ještě nerozšířila napříč Azure Active Directory. Zkuste příkaz znovu spustit.

Obvykle tento postup zopakujete pro role *Přispěvatel sítě* a *Přispěvatel účtů úložiště*, abyste zajistili přiřazení uživatelů ke správě nasazených prostředků. V tomto článku můžete tyto kroky vynechat.

## <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../governance/policy/overview.md) pomáhá zajistit, aby všechny prostředky v předplatném splňovaly firemní standardy. Vaše předplatné už obsahuje několik definic zásad. Pokud chcete zobrazit dostupné definice zásad, použijte [Get-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/Get-AzPolicyDefinition) příkaz:

```azurepowershell-interactive
(Get-AzPolicyDefinition).Properties | Format-Table displayName, policyType
```

Zobrazí se definice existujících zásad. Typ zásad je buď **Předdefinované** nebo **Vlastní**. Najděte definice zásad popisující podmínku, kterou chcete přiřadit. V tomto článku můžete přiřadit zásady s těmito funkcemi:

* Omezení umístění pro všechny prostředky
* Omezení SKU pro virtuální počítače
* Audit virtuálních počítačů, které nepoužívají spravované disky

V následujícím příkladu načtete definice tří zásad na základě zobrazovaného názvu. Můžete použít [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment) příkazu přiřaďte tyto definice do skupiny prostředků. U některých zásad určíte povolené hodnoty zadáním hodnot parametrů.

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

Přiřadili jste role a zásady, takže jste připravení nasadit řešení. Výchozí velikost je Standard_DS1_v2, což je jedno z povolených SKU. Při spuštění tohoto kroku se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač.

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

Po dokončení nasazování můžete použít další nastavení pro správu řešení.

## <a name="lock-resources"></a>Uzamčení prostředků

[Zámky prostředků](../../azure-resource-manager/resource-group-lock-resources.md) zabraňují tomu, aby uživatelé ve vaší organizaci neúmyslně odstranili nebo změnili důležité prostředky. Na rozdíl od řízení přístupu na základě role používají zámky prostředků omezení pro všechny uživatele a role. Zámek můžete nastavit na úroveň *CanNotDelete* nebo *ReadOnly*.

Zamknout virtuálního počítače a skupiny zabezpečení sítě, použijte [New-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock) příkaz:

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

Zámky otestujete pomocí následujícího příkazu:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup
```

Zobrazí se chyba s oznámením, že operaci odstranění nelze kvůli zámku dokončit. Skupinu prostředků odstraníte, pouze pokud skutečně odeberete zámky. Tento krok najdete v části o [vyčištění prostředků](#clean-up-resources).

## <a name="tag-resources"></a>Označení prostředků

K logickému uspořádání prostředků Azure podle kategorií slouží [značky](../../azure-resource-manager/resource-group-using-tags.md). Každá značka se skládá z názvu a hodnoty. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Chcete-li použít značky na virtuální počítač, použijte [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) příkaz:

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Hledání prostředků podle značky

K vyhledání prostředků pomocí značek název a hodnotu, použijte [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) příkaz:

```azurepowershell-interactive
(Get-AzResource -Tag @{ Environment="Test"}).Name
```

Vrácené hodnoty můžete použít pro úlohy správy, jako je zastavení všech virtuálních počítačů s určitou hodnotou značky.

```azurepowershell-interactive
Get-AzResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzVM
```

### <a name="view-costs-by-tag-values"></a>Zobrazení nákladů podle hodnoty značky

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Zamčená skupina zabezpečení sítě nejde odstranit, dokud neodstraníte zámek. Chcete-li odebrat zámek proti, použijte [odebrat AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcelock) příkaz:

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

Pokud už je nepotřebujete, můžete použít [odebrat AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) příkazu k odebrání skupiny prostředků, virtuálního počítače, a všechny související prostředky.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili vlastní image virtuálního počítače. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Přiřazení role uživateli
> * Použití zásad, které vynucují standardy
> * Ochrana důležitých prostředků pomocí zámků
> * Označení prostředků pro fakturaci a správu

Přejděte k dalšímu kurzu, kde se seznámíte s virtuálními počítači s vysokou dostupností.

> [!div class="nextstepaction"]
> [Monitorování virtuálních počítačů](tutorial-monitoring.md)

