---
title: Konfigurace MSI ve VMSS Azure pomocí Powershellu
description: Podrobné pokyny ke konfiguraci systémových a uživatelských identit přiřazených na VMSS Azure pomocí Powershellu.
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
ms.openlocfilehash: b82785d0f4b6a5952334e891e7adec570c624f2d
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238127"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Konfigurace VMSS Identity spravované služby (MSI) pomocí Powershellu

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak k provádění operací identita spravované služby ve škálovací sadě virtuálních počítačů Azure pomocí Powershellu:
- Povolit a zakázat identitu přiřazenou systémem ve škálovací sadě virtuálních počítačů
- Přidání a odebrání identity přiřazené uživateli na škálovací sadu virtuálních počítačů

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi přiřazenou systémem a identity přiřazené uživateli](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení rolí:
    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) chcete vytvořit škálovací sadu virtuálních počítačů a povolit a odebrat přiřazenou systémem spravované a/nebo nastavit identitu uživatele z škálovací sadu virtuálních počítačů.
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role pro vytvoření identity přiřazené uživateli.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli přiřadit a odebrat identity přiřazené uživateli, ze kterých a do škálovací sady virtuálních počítačů.
- Nainstalujte [nejnovější verzi Azure Powershellu](https://www.powershellgallery.com/packages/AzureRM) Pokud jste tak již neučinili. 

## <a name="system-assigned-managed-identity"></a>Spravovanou identitu přiřazenou systémem

V této části se dozvíte, jak povolit a odebrat přiřazenou systémem identit pomocí Azure Powershellu.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Povolit identitu přiřazenou systémem při vytváření škálovací sady virtuálních počítačů Azure

Chcete-li vytvořit VMSS s identitou přiřazenou systémem povoleno:

1. Odkazovat na *Příklad 1* v [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) rutiny článku o vytvoření VMSS s identitou přiřazenou systémem.  Přidat parametr `-IdentityType SystemAssigned` k `New-AzureRmVmssConfig` rutiny:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Volitelné) Přidat pomocí rozšíření MSI VMSS `-Name` a `-Type` parametru u [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) rutiny. Můžete předat buď "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu sady škálování virtuálního počítače nastavte a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu:

    > [!NOTE]
    > Tento krok je volitelný, i identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Povolit identitu přiřazenou systémem v existující škálovací sady virtuálních počítačů Azure

Pokud je potřeba povolit identitu přiřazenou systémem v existující škálovací sady virtuálních počítačů Azure:

1. Přihlaste se k Azure s využitím `Login-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na škálovací sadu virtuálních počítačů, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Login-AzureRmAccount
   ```

2. Prvním načtení škálovací sady virtuálních počítačů pomocí vlastnosti [ `Get-AzureRmVmss` ](/powershell/module/azurerm.compute/get-azurermvmss) rutiny. Chcete-li povolit identitu přiřazenou systémem, použijte `-IdentityType` přepnout [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) rutiny:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

3. Přidat pomocí rozšíření MSI VMSS `-Name` a `-Type` parametru u [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) rutiny. Můžete předat buď "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu sady škálování virtuálního počítače nastavte a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Zakázat identitu přiřazenou systémem ze škálovací sady virtuálních počítačů Azure

Pokud máte škálovací sadu virtuálních počítačů, který už nepotřebuje identitu přiřazenou systémem, ale stále potřebuje uživatel identit přiřazených, použijte následující rutinu:

1. Přihlaste se k Azure s využitím `Login-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na škálovací sadu virtuálních počítačů, jako je například "Přispěvatel virtuálních počítačů":

2. Spusťte následující rutinu:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Pokud máte škálovací sadu virtuálních počítačů, které už nepotřebuje identitu přiřazenou systémem a nemá žádný uživatel identit přiřazených, použijte následující příkazy:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-identity"></a>Identity přiřazené uživateli

V této části se dozvíte, jak přidávat a odebírat uživatele identity přiřazené z virtuálního počítače škálovací sady pomocí Azure Powershellu.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Přiřadit identity přiřazené uživateli při vytváření škálovací sady virtuálních počítačů Azure

Vytváření nového virtuálního počítače škálovací sadu s identity přiřazené uživateli není aktuálně podporováno prostřednictvím prostředí PowerShell. Viz následující část o tom, jak přidat identitu uživatele přiřadit do existující škálovací sady virtuálních počítačů. Vraťte se sem a přečtěte si nové informace.

### <a name="assign-a-user-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Identita uživatele přiřadit existující škálovací sadě virtuálních počítačů Azure

Při přiřazování identity přiřazené uživateli do existující škálovací sady virtuálních počítačů Azure:

1. Přihlaste se k Azure s využitím `Connect-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na škálovací sadu virtuálních počítačů, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Prvním načtení škálovací sady virtuálních počítačů pomocí vlastnosti `Get-AzureRmVM` rutiny. Pak můžete přiřadit identity přiřazené uživateli škálovací sadu virtuálních počítačů, pomocí `-IdentityType` a `-IdentityID` zapnout [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) rutiny. Nahraďte `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` vlastními hodnotami.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Odebrat uživatele, přiřazenou spravovanou identitu ze škálovací sady virtuálních počítačů Azure

Pokud vaše škálovací sada virtuálních počítačů má více uživatelsky přiřazených identit, můžete odebrat všechny kromě poslední z nich pomocí následujících příkazů. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VMSS NAME>` vlastními hodnotami. `<MSI NAME>` Je název vlastnosti identity přiřazené uživateli, která by měla zůstat na škálovací sadu virtuálních počítačů. Tyto informace najdete v části Identita škálovací sady virtuálních počítačů `az vmss show`:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<MSI NAME>"
```
Pokud chcete odebrat všechny uživatele identit přiřazených z něj škálovací sadu virtuálních počítačů nemá identitu přiřazenou systémem, použijte následující příkaz:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Pokud vaše škálovací sada virtuálních počítačů má přiřazenou systémem a identity přiřazené uživateli, můžete odebrat všechny uživatele identit přiřazených přepnutím používat pouze přiřazenou systémem.

```powershell 
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Související obsah

- [Přehled spravované Identity služby](overview.md)
- Úplné vytvoření virtuálního počítače Azure rychlých startů naleznete v tématu:
  
  - [Vytvoření virtuálního počítače s Windows pomocí Powershellu](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Vytvoření virtuálního počítače s Linuxem pomocí Powershellu](../../virtual-machines/linux/quick-create-powershell.md) 

















