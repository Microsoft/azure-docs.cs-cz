---
title: Postup konfigurace MSI na VMSS Azure pomocí prostředí PowerShell
description: Podrobné pokyny ke konfiguraci systému a uživatele přiřadit identit na VMSS Azure pomocí prostředí PowerShell.
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
ms.openlocfilehash: 2b3651eaf702cfe2f73320fcaf2ab469dd7c478a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Konfigurace VMSS spravované služby Identity (MSI) pomocí prostředí PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak provádět následující operace identita spravované služby v Azure Virtual Machine škálování nastavit (VMSS), pomocí prostředí PowerShell:
- Povolení a zákaz systému přiřazené identita na VMSS Azure
- Přidání a odebrání uživatele přiřazené identita na VMSS Azure

## <a name="prerequisites"></a>Požadavky

- Pokud jste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systémem přiřazený a uživatel s přiřazenou identity](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.
- Nainstalujte [nejnovější verzi prostředí Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) Pokud jste tak ještě neučinili. 

## <a name="system-assigned-managed-identity"></a>Systém přiřazené spravované identity

V této části zjistěte, jak povolit a odeberte identitu systému přiřazené pomocí Azure PowerShell.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Povolit systém přiřazené identity během vytváření služby Azure VMSS

Vytvoření VMSS s identitou systému přiřazené povoleno:

1. Odkazovat na *Příklad 1* v [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) rutiny článku o vytvoření VMSS s identitou systému, které jsou přiřazeny.  Přidejte parametr `-IdentityType SystemAssigned` k `New-AzureRmVmssConfig` rutiny:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Volitelné) Přidat pomocí rozšíření MSI VMSS `-Name` a `-Type` parametr na [přidat AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametr. `-Settings` Parametr určuje port je používán koncový bod tokenu OAuth pro získání tokenu:

    > [!NOTE]
    > Tento krok je nepovinný, protože identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k načtení také tokeny.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>Povolit systém přiřazené identita na existující VMSS Azure

Pokud potřebujete povolit identitu systému přiřazené na existující VMSS Azure:

1. Přihlaste se k Azure pomocí `Login-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Login-AzureRmAccount
   ```

2. Nejdřív načtěte pomocí vlastnosti VMSS [ `Get-AzureRmVmss` ](/powershell/module/azurerm.compute/get-azurermvmss) rutiny. Povolit systému přiřazené identity, použije `-IdentityType` přepínač na [aktualizace-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny:

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. Přidat pomocí rozšíření MSI VMSS `-Name` a `-Type` parametr na [přidat AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametr. `-Settings` Parametr určuje port je používán koncový bod tokenu OAuth pro získání tokenu:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>Zakázat systému přiřazenou identit ze služby Azure VMSS

> [!NOTE]
> Zakázání spravované identita služby z sadu škálování virtuálního počítače není aktuálně podporováno. Do té doby můžete přepnout mezi použitím přiřazené systému a přiřadit identity uživatelů.

Pokud máte sadu škálování virtuálního počítače, který už nepotřebuje systému přiřazené identity, ale stále potřebuje uživatel s přiřazenou identit, použijte následující rutinu:

1. Přihlaste se k Azure pomocí `Login-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů":

2. Spusťte následující rutinu:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>Uživatel s přiřazenou identity

V této části se dozvíte, jak přidávat a odebírat uživatele přiřazené identity z VMSS, pomocí Azure PowerShell.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>Přiřazení uživatele přiřazené identity během vytváření služby Azure VMSS

Vytvoření nové VMSS se uživatel přiřazený identity není aktuálně podporován pomocí prostředí PowerShell. O tom, jak přidat identitu uživatele přiřazeno k existující VMSS najdete v další části. Vraťte se zpět pro aktualizace.

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>Identita uživatele přiřadit existující VMSS Azure

Přiřazení uživatele existující VMSS Azure přiřazen identity:

1. Přihlaste se k Azure pomocí `Connect-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Nejdřív načíst vlastnosti virtuálního počítače pomocí `Get-AzureRmVM` rutiny. Chcete-li přiřadit identitu uživatele přiřazené k Azure VMSS, použijte `-IdentityType` a `-IdentityID` přepínač na [aktualizace-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny. Nahraďte `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` vlastními hodnotami.

   > [!IMPORTANT]
   > Vytvoření uživatele přiřazené identity s speciální znaky (tj. podtržítko) v názvu není aktuálně podporován. Použijte alfanumerické znaky. Vraťte se zpět pro aktualizace.  Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Odebrání uživatele přiřazené identity z VMSS Azure

> [!NOTE]
> Odebrání všech identit uživatelů přiřazené z sadu škálování virtuálního počítače není aktuálně podporováno, pokud používáte systém přiřazené identity. Vraťte se zpět pro aktualizace.

Pokud vaše VMSS má více identit přiřazeného uživatele, můžete odebrat všechny uzly s výjimkou naposledy pomocí následujících příkazů. Nezapomeňte nahradit `<RESOURCE GROUP>` a `<VMSS NAME>` hodnoty parametrů s vlastními hodnotami. `<MSI NAME>` Je identita uživatele přiřazené název vlastnosti, která by měla zůstat na VMSS. Tyto informace můžete nalezen v části Identita VMSS použití `az vmss show`:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

Pokud vaše VMSS systému přiřadit i přiřazeného identity uživatele, můžete odebrat všechny uživatelské přiřadila přepínání identity používat pouze systém přiřazen. Použijte následující příkaz:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Související obsah

- [Přehled spravované identita služby](overview.md)
- Úplné vytvoření virtuálního počítače Azure Quickstarts najdete v části:
  
  - [Vytvoření virtuálního počítače s Windows pomocí prostředí PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Vytvoření virtuální počítač s Linuxem pomocí prostředí PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















