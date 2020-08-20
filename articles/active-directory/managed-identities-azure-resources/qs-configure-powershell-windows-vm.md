---
title: Konfigurace spravovaných identit na virtuálním počítači Azure pomocí PowerShellu – Azure AD
description: Podrobné pokyny pro konfiguraci spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí PowerShellu
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/19/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474e6c96be810192d3c4e1ada1ab2e0391a5d4f9
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606485"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí PowerShellu

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu.

V tomto článku se naučíte, jak provádět následující spravované identity pro operace prostředků Azure na virtuálním počítači Azure pomocí následujících spravovaných identit.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#managed-identity-types)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Pokud jste to ještě neudělali, nainstalujte [nejnovější verzi Azure PowerShell](/powershell/azure/install-az-ps) .

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a zakázat spravovanou identitu přiřazenou systémem pomocí Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Povolit spravovanou identitu přiřazenou systémem během vytváření virtuálního počítače Azure

Aby bylo možné vytvořit virtuální počítač Azure s povolenou spravovanou identitou přiřazenou systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Přečtěte si jedno z následujících rychlých startů virtuálních počítačů Azure a vyplněním pouze nezbytných oddílů ("přihlášení do Azure", "vytvořit skupinu prostředků", "vytvořit síťovou skupinu", "Vytvoření virtuálního počítače").

    Až se dostanete do části Vytvoření virtuálního počítače, udělejte nepatrnou úpravu syntaxe rutiny [New-AzVMConfig](/powershell/module/az.compute/new-azvm) . Nezapomeňte přidat `-IdentityType SystemAssigned` parametr pro zřízení virtuálního počítače s povolenou identitou přiřazenou systémem, například:

    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -IdentityType SystemAssigned ...
    ```

   - [Virtuální počítač s Windows s využitím PowerShellu](../../virtual-machines/windows/quick-create-powershell.md)
   - [Vytvoření virtuálního počítače se systémem Linux pomocí prostředí PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Povolení spravované identity přiřazené systémem na stávajícím virtuálním počítači Azure

Aby se povolila spravovaná identita přiřazená systémem na virtuálním počítači, který se původně zřídil bez něho, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Přihlaste se k Azure pomocí `Connect-AzAccount` . Použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

   ```powershell
   Connect-AzAccount
   ```

2. Nejdřív načtěte vlastnosti virtuálního počítače pomocí `Get-AzVM` rutiny. Pokud chcete povolit spravovanou identitu přiřazenou systémem, použijte `-IdentityType` přepínač v rutině [Update-AzVM](/powershell/module/az.compute/update-azvm) :

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Přidání identity přiřazené k systému virtuálních počítačů do skupiny

Po povolení identity přiřazené systémem na virtuálním počítači ji můžete přidat do skupiny.  Následující postup přidá identitu přiřazenou systému virtuálního počítače do skupiny.

1. Přihlaste se k Azure pomocí `Connect-AzAccount` . Použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

   ```powershell
   Connect-AzAccount
   ```

2. Načte a poznamenejte si `ObjectID` (jak je uvedeno v `Id` poli vrácených hodnot) objektu služby virtuálního počítače:

   ```powershell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Načte a poznamenejte si `ObjectID` (jak je uvedeno v `Id` poli vrácených hodnot) skupiny:

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Přidejte instanční objekt virtuálního počítače do skupiny:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Zakázat spravovanou identitu přiřazenou systémem z virtuálního počítače Azure

Aby se na virtuálním počítači zakázala spravovaná identita přiřazená systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

Pokud máte virtuální počítač, který už nepotřebuje spravovanou identitu přiřazenou systémem, ale potřebuje spravované identity přiřazené uživatelem, použijte tuto rutinu:

1. Přihlaste se k Azure pomocí `Connect-AzAccount` . Použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

   ```powershell
   Connect-AzAccount
   ```

2. Pomocí rutiny načtěte vlastnosti virtuálního počítače `Get-AzVM` a nastavte `-IdentityType` parametr na `UserAssigned` :

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Pokud máte virtuální počítač, který už nepotřebuje spravovanou identitu přiřazenou systémem a nemá žádné spravované identity přiřazené uživatelem, použijte následující příkazy:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části se dozvíte, jak přidat a odebrat spravovanou identitu přiřazenou uživatelem z virtuálního počítače pomocí Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Přiřazení spravované identity přiřazené uživateli k virtuálnímu počítači během vytváření

K přiřazení uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači potřebuje váš účet [přispěvatele virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a přiřazení rolí [spravovaného operátoru identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Přečtěte si jedno z následujících rychlých startů virtuálních počítačů Azure a vyplněním pouze nezbytných oddílů ("přihlášení do Azure", "vytvořit skupinu prostředků", "vytvořit síťovou skupinu", "Vytvoření virtuálního počítače").

    Až se dostanete do části Vytvoření virtuálního počítače, udělejte mírně změnu [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) syntaxe rutiny. Přidáním `-IdentityType UserAssigned` parametrů a `-IdentityID` ZŘÍDÍTE virtuální počítač s uživatelem přiřazenou identitou.  Nahraďte `<VM NAME>` ,, `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami.  Příklad:

    ```powershell
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```

    - [Virtuální počítač s Windows s využitím PowerShellu](../../virtual-machines/windows/quick-create-powershell.md)
    - [Vytvoření virtuálního počítače se systémem Linux pomocí prostředí PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Přiřazení spravované identity přiřazené uživateli k existujícímu virtuálnímu počítači Azure

K přiřazení uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači potřebuje váš účet [přispěvatele virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a přiřazení rolí [spravovaného operátoru identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Přihlaste se k Azure pomocí `Connect-AzAccount` . Použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

   ```powershell
   Connect-AzAccount
   ```

2. Pomocí rutiny [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) vytvořte spravovanou identitu přiřazenou uživatelem.  Všimněte si `Id` výstupů, protože ho budete potřebovat v dalším kroku.

   > [!IMPORTANT]
   > Vytváření uživatelem definovaných spravovaných identit podporuje pouze alfanumerické znaky, podtržítka a spojovníky (0-9 nebo a-z nebo A-Z nebo \_ -) znaků. Kromě toho by měl být název omezen na 3 až 128 znaků, aby přiřazení k virtuálnímu počítači/VMSS správně fungovalo. Další informace najdete v [nejčastějších dotazech a známých problémech](known-issues.md) .

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Pomocí rutiny načtěte vlastnosti virtuálního počítače `Get-AzVM` . Pak k virtuálnímu počítači Azure přiřadíte spravovanou identitu přiřazenou uživatelem pomocí `-IdentityType` přepínače a `-IdentityID` v rutině [Update-AzVM](/powershell/module/az.compute/update-azvm) .  Hodnota `-IdentityId` parametru je, `Id` kterou jste si poznamenali v předchozím kroku.  Nahraďte `<VM NAME>` ,, `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami.

   > [!WARNING]
   > Pokud chcete zachovat všechny dříve přiřazené spravované identity přiřazené k virtuálnímu počítači, proveďte dotaz na `Identity` vlastnost objektu virtuálního počítače (například `$vm.Identity` ).  Pokud se vrátí nějaké spravované identity přiřazené uživatelem, zahrňte je do následujícího příkazu společně s novou spravovanou identitou přiřazenou uživatelem, kterou chcete přiřadit k virtuálnímu počítači.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Odebrání spravované identity přiřazené uživatelem z virtuálního počítače Azure

K odebrání uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači vyžaduje váš účet přiřazení role [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .

Pokud má váš virtuální počítač více spravovaných identit přiřazených uživatelem, můžete odebrat všechny kromě poslední, a to pomocí následujících příkazů. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VM NAME>` vlastními hodnotami. `<USER ASSIGNED IDENTITY NAME>`Je vlastnost název spravované identity přiřazené uživatelem, která by měla zůstat na virtuálním počítači. Tyto informace se dají najít dotazem na `Identity` vlastnost objektu virtuálního počítače.  Například `$vm.Identity` :

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Pokud váš virtuální počítač nemá spravovanou identitu přiřazenou systémem a chcete z něj odebrat všechny spravované identity přiřazené uživatelem, použijte následující příkaz:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Pokud má váš virtuální počítač spravované identity přiřazené systémem i uživatelem, můžete odebrat všechny spravované identity přiřazené uživatelem, a to přepnutím na použití pouze spravovaných identit přiřazených systémem.

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Další kroky

- [Přehled spravovaných identit pro prostředky Azure](overview.md)
- Kompletní rychlé starty pro vytváření virtuálních počítačů Azure najdete v těchto tématech:

  - [Vytvoření virtuálního počítače s Windows pomocí PowerShellu](../../virtual-machines/windows/quick-create-powershell.md)
  - [Vytvoření virtuálního počítače s Linuxem pomocí PowerShellu](../../virtual-machines/linux/quick-create-powershell.md)
