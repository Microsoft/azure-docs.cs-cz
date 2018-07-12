---
title: Konfigurace MSI ve Virtuálním počítači Azure pomocí Powershellu
description: Projděte pokyny ke konfiguraci Identity spravované služby (MSI) na virtuálním počítači Azure pomocí Powershellu.
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
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9a466a5c695277a7b5833f997e2ad7281c962f3f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610995"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurace virtuálním počítači Identity spravované služby (MSI) pomocí Powershellu

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a odebrat MSI pro virtuální počítač Azure, pomocí Powershellu.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Také nainstalujte [nejnovější verzi Azure Powershellu](https://www.powershellgallery.com/packages/AzureRM) (verze 4.3.1 nebo novější) Pokud jste tak již neučinili.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Povolení MSI během vytváření virtuálního počítače Azure

Vytvoření virtuálního počítače s podporou MSI:

1. Odkazovat na jeden z následujících startu virtuálního počítače Azure, dokončení jenom nezbytné oddíly ("Přihlášení k Azure", "Vytvoření skupiny prostředků", "Vytvoření sítě skupiny", "Vytvoření virtuálního počítače"). 

   > [!IMPORTANT] 
   > Když dostanete do části "Vytvoření virtuálního počítače", ujistěte se, drobné změny [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) Syntaxe rutin. Nezapomeňte si přidat `-IdentityType "SystemAssigned"` parametr ke zřízení virtuálního počítače pomocí MSI, například:
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [Vytvoření virtuálního počítače s Windows pomocí Powershellu](~/articles/virtual-machines/windows/quick-create-powershell.md)
   - [Vytvoření virtuálního počítače s Linuxem pomocí Powershellu](~/articles/virtual-machines/linux/quick-create-powershell.md)



2. Přidat direktivu using MSI virtuálního počítače rozšíření `-Type` parametru u [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Povolení MSI na existujícím virtuálním počítači Azure

Pokud je potřeba povolit MSI na existující virtuální počítač:

1. Přihlaste se k Azure s využitím `Connect-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Nejdřív načtěte pomocí vlastnosti virtuálního počítače `Get-AzureRmVM` rutiny. Chcete-li povolit MSI, použijte `-IdentityType` přepnout [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Přidat direktivu using MSI virtuálního počítače rozšíření `-Type` parametru u [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu. Nezapomeňte zadat správné `-Location` parametr odpovídající umístění existujícího virtuálního počítače:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Odebrání MSI virtuálního počítače Azure

Pokud máte virtuální počítač, který už je Instalační služba MSI, můžete použít `RemoveAzureRmVMExtension` rutina pro odebrání MSI z virtuálního počítače:

1. Přihlaste se k Azure s využitím `Connect-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Použití `-Name` přepněte se [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) rutiny, určení stejným názvem, který při přidání rozšíření:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Související obsah

- [Přehled spravované Identity služby](msi-overview.md)
- Úplné vytvoření virtuálního počítače Azure rychlých startů naleznete v tématu:
  
  - [Vytvoření virtuálního počítače s Windows pomocí Powershellu](~/articles/virtual-machines/windows/quick-create-powershell.md) 
  - [Vytvoření virtuálního počítače s Linuxem pomocí Powershellu](~/articles/virtual-machines/linux/quick-create-powershell.md) 

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.
















