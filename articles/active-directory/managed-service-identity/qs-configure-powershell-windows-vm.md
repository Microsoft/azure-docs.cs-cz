---
title: Konfigurace MSI ve Virtuálním počítači Azure pomocí Powershellu
description: Projděte pokyny ke konfiguraci Identity spravované služby (MSI) na virtuálním počítači Azure pomocí Powershellu.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 20bf16eeb6aff952423af6754812f9532e55cd5f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444448"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurace virtuálním počítači Identity spravované služby (MSI) pomocí Powershellu

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>V tomto článku se dozvíte, jak provádět následující operace Identity spravované služby na virtuálním počítači Azure pomocí Powershellu:
- 

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi přiřazenou systémem a identity přiřazené uživateli](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.
- Nainstalujte [nejnovější verzi Azure Powershellu](https://www.powershellgallery.com/packages/AzureRM) Pokud jste tak již neučinili.

## <a name="system-assigned-identity"></a>Identitu přiřazenou systémem

V této části se dozvíte, jak povolit a zakázat identitu přiřazenou systémem pomocí Azure Powershellu.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Povolit systém identity přiřazené během vytváření virtuálního počítače Azure

Vytvoření virtuálního počítače Azure s přiřazenou identity povoleno systémem:

1. Odkazovat na jeden z následujících startu virtuálního počítače Azure, dokončení jenom nezbytné oddíly ("Přihlášení k Azure", "Vytvoření skupiny prostředků", "Vytvoření sítě skupiny", "Vytvoření virtuálního počítače").
    
    Když dostanete do části "Vytvoření virtuálního počítače", ujistěte se, drobné změny [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) Syntaxe rutin. Nezapomeňte si přidat `-AssignIdentity:$SystemAssigned` parametr ke zřízení virtuálního počítače s identitou přiřazenou systémem povolené, například:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Vytvoření virtuálního počítače s Windows pomocí Powershellu](../../virtual-machines/windows/quick-create-powershell.md)
   - [Vytvoření virtuálního počítače s Linuxem pomocí Powershellu](../../virtual-machines/linux/quick-create-powershell.md)

2. (Volitelné) Přidat direktivu using MSI virtuálního počítače rozšíření `-Type` parametru u [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Tento krok je volitelný, i identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také.

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Povolit v existujícím virtuálním počítači Azure identitu přiřazenou systémem

Pokud je potřeba povolit identitu přiřazenou systémem na existující virtuální počítač:

1. Přihlaste se k Azure s využitím `Login-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Login-AzureRmAccount
   ```

2. Nejdřív načtěte pomocí vlastnosti virtuálního počítače `Get-AzureRmVM` rutiny. Chcete-li povolit identitu přiřazenou systémem, použijte `-AssignIdentity` přepnout [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

3. (Volitelné) Přidat direktivu using MSI virtuálního počítače rozšíření `-Type` parametru u [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu. Nezapomeňte zadat správné `-Location` parametr odpovídající umístění existujícího virtuálního počítače:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Tento krok je volitelný, i identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také.

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Zakázat systému identity přiřazené z virtuálního počítače Azure

> [!NOTE]
>  Zakáže identita spravované služby z virtuálního počítače není aktuálně podporováno. Do té doby můžete přepínat mezi použitím systém přiřadil a přiřazené identity uživatele.

Pokud máte virtuální počítač se už nepotřebuje identitu přiřazenou systémem, ale stále potřebuje identity přiřazené uživateli, použijte následující rutinu:

1. Přihlaste se k Azure s využitím `Login-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Login-AzureRmAccount
   ```

2. Spusťte následující rutinu: 
    ```powershell       
    Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -IdentityType "UserAssigned"
    ```
Odebrat rozšíření MSI virtuálního počítače, uživatele – název přepínače s [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) rutiny, určení stejným názvem, který při přidání rozšíření:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>Identity přiřazené uživateli

V této části se dozvíte, jak přidávat a odebírat uživatele identity přiřazené z virtuálního počítače pomocí Azure Powershellu.

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>Přiřazování identity přiřazené k virtuálnímu počítači při vytváření uživateli

Postup přiřazení identity přiřazené uživateli na Virtuálním počítači Azure při vytváření virtuálního počítače:

1. Odkazovat na jeden z následujících startu virtuálního počítače Azure, dokončení jenom nezbytné oddíly ("Přihlášení k Azure", "Vytvoření skupiny prostředků", "Vytvoření sítě skupiny", "Vytvoření virtuálního počítače"). 
  
    Když dostanete do části "Vytvoření virtuálního počítače", ujistěte se, drobné změny [ `New-AzureRmVMConfig` ](/powershell/module/azurerm.compute/new-azurermvm) Syntaxe rutin. Přidat `-IdentityType UserAssigned` a `-IdentityID ` parametry pro zřízení virtuálního počítače s využitím identity přiřazené uživateli.  Nahraďte `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, a `<MSI NAME>` vlastními hodnotami.  Příklad:
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [Vytvoření virtuálního počítače s Windows pomocí Powershellu](../../virtual-machines/windows/quick-create-powershell.md)
    - [Vytvoření virtuálního počítače s Linuxem pomocí Powershellu](../../virtual-machines/linux/quick-create-powershell.md)

2. (Volitelné) Přidat direktivu using MSI virtuálního počítače rozšíření `-Type` parametru u [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu. Nezapomeňte zadat správné `-Location` parametr odpovídající umístění existujícího virtuálního počítače:
      > [!NOTE]
    > Tento krok je volitelný, i identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>Přiřaďte identitu uživatele do existujícího virtuálního počítače Azure

K přiřazování identity přiřazené do stávajícího virtuálního počítače Azure uživateli:

1. Přihlaste se k Azure s využitím `Connect-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Vytvoření uživatele přiřadit pomocí identity [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) rutiny.  Poznámka: `Id` ve výstupu vzhledem k tomu, že ho budete potřebovat v dalším kroku.

    > [!IMPORTANT]
    > Vytvoření uživatelsky přiřazených identit podporuje pouze alfanumerické znaky a spojovník (0-9 nebo a-z nebo A-Z nebo -) znaků. Kromě toho název by měl být omezený na 24 znaků pro přiřazení k VM/VMSS správně fungovat. Sledujte novinky. Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)


  ```powershell
  New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
  ```
3. Načíst vlastnosti virtuálního počítače pomocí `Get-AzureRmVM` rutiny. Identity přiřazené uživateli přiřadit virtuálnímu počítači Azure, použijte `-IdentityType` a `-IdentityID` zapnout [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny.  Hodnota`-IdentityId` parametr je `Id` jste si poznamenali v předchozím kroku.  Nahraďte `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Přidat direktivu using MSI virtuálního počítače rozšíření `-Type` parametru u [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu. Zadejte správný `-Location` parametr odpovídající umístění existujícího virtuálního počítače.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Odebrání uživatele přiřazeny spravovaná identita z virtuálního počítače Azure

> [!NOTE]
>  Odebrání všech uživatelsky přiřazených identit z virtuálního počítače se aktuálně nepodporuje, pokud máte identitu přiřazenou systémem. Sledujte novinky.

Pokud váš virtuální počítač má více uživatelsky přiřazených identit, můžete odebrat všechny kromě poslední z nich pomocí následujících příkazů. Nezapomeňte nahradit `<RESOURCE GROUP>` a `<VM NAME>` parametr hodnoty vlastními hodnotami. `<MSI NAME>` Je název vlastnosti identity přiřazené uživateli, která by měla zůstat na virtuálním počítači. Tyto informace můžete zobrazit tak v části Identita virtuálního počítače pomocí `az vm show`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm
```

Pokud váš virtuální počítač má přiřazenou systémem a identity přiřazené uživateli, můžete odebrat všechny uživatele identit přiřazených přepnutím používat pouze přiřazenou systémem. Použijte následující příkaz:

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = $null
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Související obsah

- [Přehled spravované Identity služby](overview.md)
- Úplné vytvoření virtuálního počítače Azure rychlých startů naleznete v tématu:
  
  - [Vytvoření virtuálního počítače s Windows pomocí Powershellu](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Vytvoření virtuálního počítače s Linuxem pomocí Powershellu](../../virtual-machines/linux/quick-create-powershell.md) 
