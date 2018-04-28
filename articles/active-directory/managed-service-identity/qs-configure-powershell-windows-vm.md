---
title: Postup konfigurace MSI ve virtuálním počítači Azure pomocí prostředí PowerShell
description: Krok podle podrobné pokyny pro konfiguraci a spravovaná služba Identity (MSI) ve virtuálním počítači Azure, pomocí prostředí PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 0c316a2c73b451e4d8f67ace7b41c38dcfbc52f0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí prostředí PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>V tomto článku se dozvíte, jak provádět následující operace identita spravované služby ve virtuálním počítači Azure, pomocí prostředí PowerShell:
- 

## <a name="prerequisites"></a>Požadavky

- Pokud jste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systémem přiřazený a uživatel s přiřazenou identity](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.
- Nainstalujte [nejnovější verzi prostředí Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) Pokud jste tak ještě neučinili.

## <a name="system-assigned-identity"></a>Systém přiřazené identity

V této části se dozvíte, jak povolit a zakázat systému přiřazené identitu pomocí Azure PowerShell.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Povolit systém přiřazené identity během vytváření virtuální počítač Azure

Chcete-li vytvořit virtuální počítač Azure se systémem přiřadit identity povoleno:

1. Podívejte se na jeden z následujících Quickstarts virtuálních počítačů Azure, dokončení jenom nezbytné oddíly ("Přihlášení k Azure", "Vytvořte skupinu prostředků", "Vytvoření sítě skupiny", "Vytvoření virtuálního počítače").
    
    Když získáte do části "Vytvoření virtuálního počítače", provedete mírné změny [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) Syntaxe rutin. Nezapomeňte přidat `-AssignIdentity "SystemAssigned"` parametr zřídit virtuální počítač s identitou systému přiřazené povoleno, například:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity "SystemAssigned" ...
    ```

   - [Vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Vytvořit virtuální počítač s Linuxem pomocí prostředí PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Volitelné) Přidat pomocí rozšíření virtuálního počítače MSI `-Type` parametr na [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametr. `-Settings` Parametr určuje port je používán koncový bod tokenu OAuth pro získání tokenu:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Tento krok je nepovinný, protože identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k načtení také tokeny.

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Povolit systém přiřazené identita na existující virtuální počítač Azure

Pokud potřebujete povolit identitu systému přiřazené na existující virtuální počítač:

1. Přihlaste se k Azure pomocí `Login-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Login-AzureRmAccount
   ```

2. Nejdřív načíst vlastnosti virtuálního počítače pomocí `Get-AzureRmVM` rutiny. Povolit systému přiřazené identity, použije `-AssignIdentity` přepínač na [aktualizace-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity "SystemAssigned"
   ```

3. (Volitelné) Přidat pomocí rozšíření virtuálního počítače MSI `-Type` parametr na [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametr. `-Settings` Parametr určuje port je používán koncový bod tokenu OAuth pro získání tokenu. Nezapomeňte zadat správné `-Location` parametr odpovídající umístění existující virtuální počítač:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Tento krok je nepovinný, protože identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k načtení také tokeny.

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Zakázat systému přiřazené identity z virtuálního počítače Azure

> [!NOTE]
>  Zakázání spravované identita služby z virtuálního počítače není aktuálně podporováno. Do té doby můžete přepnout mezi použitím přiřazené systému a přiřadit identity uživatelů.

Pokud máte virtuální počítač, který už nepotřebuje systému přiřazené identity, ale stále potřebuje přiřazeného identity uživatele, použijte následující rutinu:

1. Přihlaste se k Azure pomocí `Login-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Login-AzureRmAccount
   ```

2. Spusťte následující rutinu: 
    ```powershell       
    Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -IdentityType "UserAssigned"
    ```
Odebrat rozšíření virtuálního počítače MSI, uživatel-název přepínače s [odebrat AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) rutiny zadat stejný název, který jste použili při přidání rozšíření:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>Uživatel s přiřazenou identity

V této části se dozvíte, jak přidávat a odebírat uživatele přiřazené identity z virtuálního počítače pomocí Azure PowerShell.

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>Přiřazení uživatele přiřazené identity virtuálního počítače během vytváření

Přiřazení uživatele přiřadit identity na virtuální počítač Azure při vytváření virtuálního počítače:

1. Podívejte se na jeden z následujících Quickstarts virtuálních počítačů Azure, dokončení jenom nezbytné oddíly ("Přihlášení k Azure", "Vytvořte skupinu prostředků", "Vytvoření sítě skupiny", "Vytvoření virtuálního počítače"). 
  
    Když získáte do části "Vytvoření virtuálního počítače", provedete mírné změny [ `New-AzureRmVMConfig` ](/powershell/module/azurerm.compute/new-azurermvm) Syntaxe rutin. Přidat `-IdentityType UserAssigned` a `-IdentityID ` parametry zřídit virtuální počítač s identitou přiřazeného uživatele.  Nahraďte `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, a `<MSI NAME>` vlastními hodnotami.  Příklad:
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [Vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Vytvořit virtuální počítač s Linuxem pomocí prostředí PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Volitelné) Přidat pomocí rozšíření virtuálního počítače MSI `-Type` parametr na [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametr. `-Settings` Parametr určuje port je používán koncový bod tokenu OAuth pro získání tokenu. Nezapomeňte zadat správné `-Location` parametr odpovídající umístění existující virtuální počítač:
      > [!NOTE]
    > Tento krok je nepovinný, protože identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k načtení také tokeny.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>Identita uživatele přiřadit existující virtuální počítač Azure

Přiřazení uživatele přiřadit k existující virtuální počítač Azure identity:

1. Přihlaste se k Azure pomocí `Connect-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Vytvoření uživatele přiřazená pomocí identity [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) rutiny.  Poznámka: `Id` ve výstupu vzhledem k tomu, že budete potřebovat v dalším kroku.

    > [!IMPORTANT]
    > Vytvoření uživatele přiřazené identity s speciální znaky (tj. podtržítko) v názvu není aktuálně podporován. Použijte alfanumerické znaky. Vraťte se zpět pro aktualizace.  Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

  ```powershell
  New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
  ```
3. Načíst vlastnosti virtuálního počítače pomocí `Get-AzureRmVM` rutiny. Chcete-li přiřadit identitu uživatele přiřazené k virtuálnímu počítači Azure, použijte `-IdentityType` a `-IdentityID` přepínač na [aktualizace-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny.  Hodnota`-IdentityId` parametr je `Id` jste si poznamenali v předchozím kroku.  Nahraďte `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Přidat pomocí rozšíření virtuálního počítače MSI `-Type` parametr na [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametr. `-Settings` Parametr určuje port je používán koncový bod tokenu OAuth pro získání tokenu. Zadejte správný `-Location` parametr odpovídající umístění existující virtuální počítač.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Odebrání uživatele přiřazené spravované identity z virtuálního počítače Azure

> [!NOTE]
>  Odebrání všech identit uživatelů přiřazené z virtuálního počítače není aktuálně podporováno, pokud používáte systém přiřazené identity. Vraťte se zpět pro aktualizace.

Pokud virtuální počítač má více identit přiřazeného uživatele, můžete odebrat všechny uzly s výjimkou naposledy pomocí následujících příkazů. Nezapomeňte nahradit `<RESOURCE GROUP>` a `<VM NAME>` hodnoty parametrů s vlastními hodnotami. `<MSI NAME>` Je identita uživatele přiřazené název vlastnosti, která by měla zůstat ve virtuálním počítači. Tyto informace můžete nalezen v části Identita virtuální počítač pomocí `az vm show`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm
```

Pokud virtuální počítač má systém přiřadit i přiřazeného identity uživatele, můžete odebrat všechny uživatelské přiřadila přepínání identity používat pouze systém přiřazen. Použijte následující příkaz:

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = $null
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Související obsah

- [Přehled spravované identita služby](overview.md)
- Úplné vytvoření virtuálního počítače Azure Quickstarts najdete v části:
  
  - [Vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Vytvoření virtuální počítač s Linuxem pomocí prostředí PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















