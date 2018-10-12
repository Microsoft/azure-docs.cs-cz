---
title: Postup konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí Powershellu
description: Podrobné pokyny ke konfiguraci spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí Powershellu.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 3cd0a88747379edb15385014fcc93287d95295e0
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114035"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí Powershellu

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku pomocí Powershellu, se dozvíte, jak k provádění následujících spravovaných identit pro prostředky Azure operace na Virtuálním počítači Azure:

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení řízení přístupu na základě rolí Azure:

    > [!NOTE]
    > Žádné další Azure vyžaduje přiřazení rolí adresáře AD.

    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) k vytvoření virtuálního počítače a povolit a odeberte systém a/nebo uživatelsky přiřazené identity spravované z virtuálního počítače Azure.
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role k vytvoření uživatel přiřazenou se identita spravované.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli přiřadit a odebrat uživatel přiřazenou se identita spravované od a do virtuálního počítače.
- Nainstalujte [nejnovější verzi Azure Powershellu](https://www.powershellgallery.com/packages/AzureRM) Pokud jste tak již neučinili.

## <a name="system-assigned-managed-identity"></a>Systém přiřadil spravované identity

V této části se dozvíte, jak povolit a zakázat systém přiřadil spravovanou identitu pomocí Azure Powershellu.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Povolit systém přiřadil spravovanou identitu při vytváření virtuálního počítače Azure

Vytvoření virtuálního počítače Azure s systém přiřadil spravovaná identita povolen:

1. Odkazovat na jeden z následujících startu virtuálního počítače Azure, dokončení jenom nezbytné oddíly ("Přihlášení k Azure", "Vytvoření skupiny prostředků", "Vytvoření sítě skupiny", "Vytvoření virtuálního počítače").
    
    Když dostanete do části "Vytvoření virtuálního počítače", ujistěte se, drobné změny [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) Syntaxe rutin. Nezapomeňte si přidat `-AssignIdentity:$SystemAssigned` parametr ke zřízení virtuálního počítače s identitou systém přiřadil povolené, například:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Vytvoření virtuálního počítače s Windows pomocí Powershellu](../../virtual-machines/windows/quick-create-powershell.md)
   - [Vytvoření virtuálního počítače s Linuxem pomocí Powershellu](../../virtual-machines/linux/quick-create-powershell.md)

2. (Volitelné) Přidání spravovaných identit pro prostředky Azure, virtuálního počítače pomocí rozšíření (plánovaná k převedení na zastaralého v lednu 2019) `-Type` parametru u [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Tento krok je volitelný, i identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také. Spravované identity pro prostředky Azure, které vyřazení v lednu 2019 plánujeme přidat rozšíření virtuálního počítače. 

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Povolit systém přiřadil spravovaná identita na existujícím virtuálním počítači Azure

Pokud je potřeba povolit systém přiřadil spravovanou identitu na existující virtuální počítač:

1. Přihlaste se k Azure s využitím `Login-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač.

   ```powershell
   Login-AzureRmAccount
   ```

2. Nejdřív načtěte pomocí vlastnosti virtuálního počítače `Get-AzureRmVM` rutiny. Chcete-li povolit systém přiřadil spravovanou identitu, použijte `-AssignIdentity` zapnout [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

3. (Volitelné) Přidání spravovaných identit pro prostředky Azure, virtuálního počítače pomocí rozšíření (plánovaná k převedení na zastaralého v lednu 2019) `-Type` parametru u [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu. Nezapomeňte zadat správné `-Location` parametr odpovídající umístění existujícího virtuálního počítače:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Tento krok je volitelný, i identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také.

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Zakázat spravovanou identitu systém přiřadil z virtuálního počítače Azure

Pokud máte virtuální počítač se už nepotřebuje systém přiřadil spravovanou identitu, ale stále potřebuje uživatelsky přiřazené identity spravované, použijte následující rutinu:

1. Přihlaste se k Azure s využitím `Login-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač.

   ```powershell
   Login-AzureRmAccount
   ```

2. Načíst vlastnosti virtuálního počítače pomocí `Get-AzureRmVM` rutiny a nastavit `-IdentityType` parametr `UserAssigned`:

   ```powershell   
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM    
   Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Pokud máte virtuální počítač, který už je systém přiřadil spravovanou identitu a nemá žádné uživatelsky přiřazené spravované identity, použijte následující příkazy:

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

Odebrat spravovaných identit pro prostředky Azure rozšíření virtuálního počítače, uživatele – název přepínače s [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) rutiny, určení stejným názvem, který při přidání rozšíření:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-managed-identity"></a>Uživatel přiřazenou spravované identity

V této části se dozvíte, jak přidávat a odebírat spravované identity přiřazené uživateli z virtuálního počítače pomocí Azure Powershellu.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Během vytváření virtuálnímu počítači přiřadit uživateli přiřazena spravované identity

Přiřadit uživateli přiřazena spravovanou identitu virtuálního počítače Azure při vytváření virtuálního počítače:

1. Odkazovat na jeden z následujících startu virtuálního počítače Azure, dokončení jenom nezbytné oddíly ("Přihlášení k Azure", "Vytvoření skupiny prostředků", "Vytvoření sítě skupiny", "Vytvoření virtuálního počítače"). 
  
    Když dostanete do části "Vytvoření virtuálního počítače", ujistěte se, drobné změny [ `New-AzureRmVMConfig` ](/powershell/module/azurerm.compute/new-azurermvm) Syntaxe rutin. Přidat `-IdentityType UserAssigned` a `-IdentityID ` parametry pro zřízení virtuálního počítače s identitou uživatele přiřazeny.  Nahraďte `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami.  Příklad:
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Vytvoření virtuálního počítače s Windows pomocí Powershellu](../../virtual-machines/windows/quick-create-powershell.md)
    - [Vytvoření virtuálního počítače s Linuxem pomocí Powershellu](../../virtual-machines/linux/quick-create-powershell.md)

2. (Volitelné) Přidat spravovanou identitu pro prostředky Azure, virtuálního počítače pomocí rozšíření `-Type` parametru u [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu. Nezapomeňte zadat správné `-Location` parametr odpovídající umístění existujícího virtuálního počítače:
      > [!NOTE]
    > Tento krok je volitelný, i identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také. Spravované identity pro prostředky Azure, které vyřazení v lednu 2019 plánujeme přidat rozšíření virtuálního počítače.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Spravované identity přiřazené uživateli přiřadit existující virtuální počítač Azure

Spravované identity přiřazené uživateli přiřadit existujícího virtuálního počítače Azure:

1. Přihlaste se k Azure s využitím `Connect-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač.

   ```powershell
   Connect-AzureRmAccount
   ```

2. Vytvořte s využitím uživatelsky přiřazené identity spravované [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) rutiny.  Poznámka: `Id` ve výstupu vzhledem k tomu, že ho budete potřebovat v dalším kroku.

   > [!IMPORTANT]
   > Vytvoření uživatelsky přiřazené identity spravované podporuje pouze alfanumerické znaky a spojovník (0-9 nebo a-z nebo A-Z nebo -) znaků. Kromě toho název by měl být omezený na 24 znaků pro přiřazení k VM/VMSS správně fungovat. Vraťte se sem a přečtěte si nové informace. Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

   ```powershell
   New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Načíst vlastnosti virtuálního počítače pomocí `Get-AzureRmVM` rutiny. Pak můžete přiřadit uživateli přiřazena spravovanou identitu virtuálního počítače Azure, pomocí `-IdentityType` a `-IdentityID` zapnout [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny.  Hodnota`-IdentityId` parametr je `Id` jste si poznamenali v předchozím kroku.  Nahraďte `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami.

   > [!WARNING]
   > Pokud chcete zachovat všechny dříve přiřazená uživatelem spravované identity přiřazené k virtuálnímu počítači, dotazování `Identity` vlastnost v objektu VM (například `$vm.Identity`).  Pokud žádný uživatel přiřazený spravovaných identit jsou vráceny, zahrnout je do následujícího příkazu společně s nové spravované identity přiřazené uživateli, že které chcete přiřadit k virtuálnímu počítači.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Přidat spravovanou identitu pro prostředky Azure, virtuálního počítače pomocí rozšíření (plánovaná k převedení na zastaralého v lednu 2019) `-Type` parametru u [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu. Zadejte správný `-Location` parametr odpovídající umístění existujícího virtuálního počítače.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Odebrání virtuálního počítače Azure spravované identity přiřazené uživateli

Pokud váš virtuální počítač má několik spravovaných uživatelsky přiřazené identity, můžete odebrat všechny kromě poslední z nich pomocí následujících příkazů. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VM NAME>` vlastními hodnotami. `<USER ASSIGNED IDENTITY NAME>` Je vlastnost název přiřazený uživatelem spravované identity, která by měla zůstat na virtuálním počítači. Tyto informace můžete najít pomocí dotazu `Identity` vlastnost v objektu VM.  Například `$vm.Identity`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Pokud váš virtuální počítač nemá systém přiřadil spravovat identity a chcete z něj odebrat všechny přiřazené uživatele spravované identity, použijte následující příkaz:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Pokud váš virtuální počítač má systém přiřadil i uživatelsky přiřazené identity spravované, můžete odebrat všechny přiřazené uživatele spravované identity přepnutím používat jen systém přiřadil spravované identity.

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Další postup

- [Spravované identity pro prostředky Azure – přehled](overview.md)
- Úplné vytvoření virtuálního počítače Azure rychlých startů naleznete v tématu:
  
  - [Vytvoření virtuálního počítače s Windows pomocí Powershellu](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Vytvoření virtuálního počítače s Linuxem pomocí Powershellu](../../virtual-machines/linux/quick-create-powershell.md) 
