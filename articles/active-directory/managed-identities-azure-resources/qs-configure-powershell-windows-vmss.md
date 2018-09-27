---
title: Nastavení konfigurace spravovaných identit pro prostředky Azure na škálovací sadu virtuálních počítačů pomocí Powershellu
description: Podrobné pokyny ke konfiguraci systému a uživatelsky přiřazené identity spravované na škálovací sadu virtuálních počítačů sada s použitím Powershellu.
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
ms.openlocfilehash: e426da9e48c24a83c0612d233923227cf057e0f8
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47106783"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Konfigurace spravovaných identit pro prostředky Azure na škálovací sady virtuálních počítačů pomocí Powershellu

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku pomocí Powershellu, se dozvíte, jak k provádění spravovaných identit pro operace se prostředky Azure na škálovací sadu virtuálních počítačů:
- Povolení a zákaz systém přiřadil spravovaná identita na škálovací sadu virtuálních počítačů
- Přidání a odebrání uživatelsky přiřazené spravovanou identitu na škálovací sadu virtuálních počítačů

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a identity přiřazené uživateli spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení řízení přístupu na základě rolí Azure:

    > [!NOTE]
    > Žádné další Azure vyžaduje přiřazení rolí adresáře AD.

    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) vytvoření virtuálního počítače škálovací sady a povolení a odeberte systém přiřadil spravované a/nebo uživatelsky přiřazené identity spravované ze škálovací sady virtuálních počítačů.
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role k vytvoření uživatel přiřazenou se identita spravované.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli přiřadit a odebrat uživatel přiřazenou se identita spravované od a do škálovací sady virtuálních počítačů.
- Nainstalujte [nejnovější verzi Azure Powershellu](https://www.powershellgallery.com/packages/AzureRM) Pokud jste tak již neučinili. 

## <a name="system-assigned-managed-identity"></a>Systém přiřadil spravované identity

V této části se dozvíte, jak povolit a odeberte systém přiřadil spravovanou identitu pomocí Azure Powershellu.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Povolit systém přiřadil spravovanou identitu při vytváření škálovací sady virtuálních počítačů Azure

Vytvoření VMSS se systém přiřadil spravovaná identita povolen:

1. Odkazovat na *Příklad 1* v [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) rutiny článku o vytvoření VMSS se systém přiřadil spravovanou identitu.  Přidat parametr `-IdentityType SystemAssigned` k `New-AzureRmVmssConfig` rutiny:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Volitelné) Přidat spravované identity pro prostředky Azure škálovací sady virtuálních počítačů pomocí rozšíření `-Name` a `-Type` parametru u [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) rutiny. Můžete předat buď "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu sady škálování virtuálního počítače nastavte a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu:

    > [!NOTE]
    > Tento krok je volitelný, i identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Povolit systém přiřadil spravované identity v existující škálovací sady virtuálních počítačů Azure

Pokud je potřeba povolit systém přiřadil spravovanou identitu v existující škálovací sady virtuálních počítačů Azure:

1. Přihlaste se k Azure s využitím `Login-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na škálovací sadu virtuálních počítačů, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Login-AzureRmAccount
   ```

2. Prvním načtení škálovací sady virtuálních počítačů pomocí vlastnosti [ `Get-AzureRmVmss` ](/powershell/module/azurerm.compute/get-azurermvmss) rutiny. Chcete-li povolit systém přiřadil spravovanou identitu, použijte `-IdentityType` zapnout [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) rutiny:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

3. Přidání spravovaných identit pro použití rozšíření prostředků Azure VMSS `-Name` a `-Type` parametru u [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) rutiny. Můžete předat buď "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu sady škálování virtuálního počítače nastavte a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Zakázat systém přiřadil spravovanou identitu ze škálovací sady virtuálních počítačů Azure

Pokud máte škálovací sadu virtuálních počítačů, které už nepotřebuje systém přiřadil spravovanou identitu, ale stále potřebuje uživatelsky přiřazené identity spravované, použijte následující rutinu:

1. Přihlaste se k Azure s využitím `Login-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na škálovací sadu virtuálních počítačů, jako je například "Přispěvatel virtuálních počítačů":

2. Spusťte následující rutinu:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Pokud máte škálovací sady virtuálního počítače, který už je systém přiřadil spravovanou identitu a nemá žádné uživatelsky přiřazené spravované identity, použijte následující příkazy:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Uživatel přiřazenou spravované identity

V této části se dozvíte, jak přidávat a odebírat spravované identity přiřazené uživateli z virtuálního počítače škálovací sady pomocí Azure Powershellu.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Přiřadit uživateli přiřazena spravovanou identitu při vytváření škálovací sady virtuálních počítačů Azure

Vytváření nového virtuálního počítače škálovací sadu s uživatelsky přiřazené spravovanou identitu není aktuálně podporováno prostřednictvím prostředí PowerShell. Viz následující část o tom, jak přidat spravovanou identitu uživatele přiřadit do existující škálovací sady virtuálních počítačů. Vraťte se sem a přečtěte si nové informace.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Přiřadit uživateli přiřazena spravovanou identitu do existující škálovací sady virtuálních počítačů Azure

Přiřadit uživateli přiřazena spravovanou identitu do existující škálovací sady virtuálních počítačů Azure:

1. Přihlaste se k Azure s využitím `Connect-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na škálovací sadu virtuálních počítačů, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Prvním načtení škálovací sady virtuálních počítačů pomocí vlastnosti `Get-AzureRmVM` rutiny. Pak můžete přiřadit spravovanou identitu uživatele přiřadit do škálovací sady virtuálních počítačů, pomocí `-IdentityType` a `-IdentityID` zapnout [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) rutiny. Nahraďte `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` vlastními hodnotami.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Odeberte uživatelsky přiřazené spravovanou identitu ze škálovací sady virtuálních počítačů Azure

Pokud vaše škálovací sada virtuálních počítačů má několik spravovaných uživatelsky přiřazené identity, můžete odebrat všechny kromě poslední z nich pomocí následujících příkazů. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VMSS NAME>` vlastními hodnotami. `<USER ASSIGNED IDENTITY NAME>` Je vlastnost název přiřazený uživatelem spravované identity, která by měla zůstat na škálovací sadu virtuálních počítačů. Tyto informace najdete v části Identita škálovací sady virtuálních počítačů `az vmss show`:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Pokud vaše škálovací sada virtuálních počítačů nemá systém přiřadil spravovat identity a chcete z něj odebrat všechny přiřazené uživatele spravované identity, použijte následující příkaz:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Pokud vaše škálovací sada virtuálních počítačů jsou obě systém přiřadil a uživatelsky přiřazené identity spravované, můžete odebrat všechny přiřazené uživatele spravované identity přepnutím používat jen systém přiřadil spravovanou identitu.

```powershell 
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Další postup

- [Spravované identity pro prostředky Azure – přehled](overview.md)
- Úplné vytvoření virtuálního počítače Azure rychlých startů naleznete v tématu:
  
  - [Vytvoření virtuálního počítače s Windows pomocí Powershellu](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Vytvoření virtuálního počítače s Linuxem pomocí Powershellu](../../virtual-machines/linux/quick-create-powershell.md) 

















