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
ms.openlocfilehash: a29af17c97b106860c3b4adbad5e8002d2a91651
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí prostředí PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku zjistěte, jak povolit a odebrat MSI pro virtuální počítač Azure, pomocí prostředí PowerShell.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Také nainstalovat [nejnovější verzi prostředí Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) (verze 4.3.1 nebo novější) Pokud jste tak ještě neučinili.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Povolit MSI při vytváření virtuálního počítače Azure

Pro vytvoření virtuálního počítače povoleným MSI:

1. Podívejte se na jeden z následujících Quickstarts virtuálních počítačů Azure, dokončení jenom nezbytné oddíly ("Přihlášení k Azure", "Vytvořte skupinu prostředků", "Vytvoření sítě skupiny", "Vytvoření virtuálního počítače"). 

   > [!IMPORTANT] 
   > Když získáte do části "Vytvoření virtuálního počítače", provedete mírné změny [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) Syntaxe rutin. Nezapomeňte přidat `-AssignIdentity "SystemAssigned"` parametr pro zřízení virtuálního počítače s MSI, například:
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity "SystemAssigned" ...`

   - [Vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Vytvořit virtuální počítač s Linuxem pomocí prostředí PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



2. Přidat pomocí rozšíření virtuálního počítače MSI `-Type` parametr na [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametr. `-Settings` Parametr určuje port je používán koncový bod tokenu OAuth pro získání tokenu:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Povolit MSI na existující virtuální počítač Azure

Pokud potřebujete povolit MSI na existující virtuální počítač:

1. Přihlaste se k Azure pomocí `Connect-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Nejdřív načíst vlastnosti virtuálního počítače pomocí `Get-AzureRmVM` rutiny. Chcete-li povolit MSI, použijte `-AssignIdentity` přepínač na [aktualizace-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity "SystemAssigned"
   ```

3. Přidat pomocí rozšíření virtuálního počítače MSI `-Type` parametr na [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametr. `-Settings` Parametr určuje port je používán koncový bod tokenu OAuth pro získání tokenu. Nezapomeňte zadat správné `-Location` parametr odpovídající umístění existující virtuální počítač:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Odebrání virtuálního počítače Azure MSI

Pokud máte virtuální počítač, který už nepotřebuje MSI, můžete použít `RemoveAzureRmVMExtension` rutiny odebrat MSI z virtuálního počítače:

1. Přihlaste se k Azure pomocí `Connect-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Použití `-Name` přepínač s [odebrat AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) rutiny zadat stejný název, který jste použili při přidání rozšíření:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Související obsah

- [Přehled spravované identita služby](overview.md)
- Úplné vytvoření virtuálního počítače Azure Quickstarts najdete v části:
  
  - [Vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Vytvoření virtuální počítač s Linuxem pomocí prostředí PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.
















