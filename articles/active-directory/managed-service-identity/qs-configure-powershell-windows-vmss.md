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
ms.openlocfilehash: d5071a55c49a0749d91ec9617558ced76ebb007e
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188093"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Konfigurace VMSS Identity spravované služby (MSI) pomocí Powershellu

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak k provádění operací identita spravované služby v virtuálního počítače Škálovací nastavit (VMSS), pomocí prostředí PowerShell:
- Povolit nebo zakázat identitu na Azure VMSS přiřazenou systémem
- Přidávat a odebírat uživatele přiřazeny identitu na Azure VMSS

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi přiřazenou systémem a identity přiřazené uživateli](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení rolí:
    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) vytvořit škálovací sadu virtuálních počítačů a povolit a odebrat ze škálovací sady virtuálních počítačů spravovaných identitu přiřazenou systémem.
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role pro vytvoření identity přiřazené uživateli.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli přiřadit a odebrat identity přiřazené uživateli, ze kterých a do škálovací sady virtuálních počítačů.
- Nainstalujte [nejnovější verzi Azure Powershellu](https://www.powershellgallery.com/packages/AzureRM) Pokud jste tak již neučinili. 

## <a name="system-assigned-managed-identity"></a>Spravovanou identitu přiřazenou systémem

V této části se dozvíte, jak povolit a odebrat přiřazenou systémem identit pomocí Azure Powershellu.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Povolit během vytváření Azure VMSS identitu přiřazenou systémem

Chcete-li vytvořit VMSS s identitou přiřazenou systémem povoleno:

1. Odkazovat na *Příklad 1* v [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) rutiny článku o vytvoření VMSS s identitou přiřazenou systémem.  Přidat parametr `-IdentityType SystemAssigned` k `New-AzureRmVmssConfig` rutiny:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Volitelné) Přidat pomocí rozšíření MSI VMSS `-Name` a `-Type` parametru u [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu:

    > [!NOTE]
    > Tento krok je volitelný, i identitu koncového bodu Azure Instance Metadata služby (IMDS), můžete použít k získání tokenů také.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>Povolit na existujících VMSS Azure identitu přiřazenou systémem

Pokud je potřeba povolit identitu přiřazenou systémem na existující VMSS Azure:

1. Přihlaste se k Azure s využitím `Login-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Login-AzureRmAccount
   ```

2. Nejdřív načtěte pomocí vlastnosti VMSS [ `Get-AzureRmVmss` ](/powershell/module/azurerm.compute/get-azurermvmss) rutiny. Chcete-li povolit identitu přiřazenou systémem, použijte `-IdentityType` přepnout [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny:

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. Přidat pomocí rozšíření MSI VMSS `-Name` a `-Type` parametru u [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) rutiny. Můžete předat "ManagedIdentityExtensionForWindows" nebo "ManagedIdentityExtensionForLinux", v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>Zakázat systému identity přiřazené ze Azure VMSS

> [!NOTE]
> Zakáže identita spravované služby ze Škálovací sady virtuálních počítačů se aktuálně nepodporuje. Do té doby můžete přepínat mezi použitím systém přiřadil a přiřazené identity uživatele.

Pokud máte Škálovací sadu virtuálních počítačů, které už nepotřebuje identitu přiřazenou systémem, ale stále potřebuje identity přiřazené uživateli, použijte následující rutinu:

1. Přihlaste se k Azure s využitím `Login-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači, jako je například "Přispěvatel virtuálních počítačů":

2. Spusťte následující rutinu:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>Identity přiřazené uživateli

V této části se dozvíte, jak přidávat a odebírat uživatele identity přiřazené z VMSS pomocí Azure Powershellu.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>Přiřadit uživatele identity přiřazené během vytváření Azure VMSS

Vytváření nových VMSS identity přiřazené uživateli není aktuálně podporováno prostřednictvím prostředí PowerShell. Viz následující část o tom, jak přidat do existující VMSS identity přiřazené uživateli. Vraťte se sem a přečtěte si nové informace.

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>Identita uživatele přiřadit existující VMSS Azure

Přiřazení uživatele přiřadit k existující VMSS Azure identity:

1. Přihlaste se k Azure s využitím `Connect-AzureRmAccount`. Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači, jako je například "Přispěvatel virtuálních počítačů":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Nejdřív načtěte pomocí vlastnosti virtuálního počítače `Get-AzureRmVM` rutiny. Pak můžete přiřadit Azure VMSS identity přiřazené uživateli, pomocí `-IdentityType` a `-IdentityID` zapnout [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) rutiny. Nahraďte `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` vlastními hodnotami.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Odebrání uživatele ze Azure VMSS identity přiřazené

> [!NOTE]
> Odebrání všech uživatelsky přiřazených identit ze Škálovací sady virtuálních počítačů se aktuálně nepodporuje, pokud máte identitu přiřazenou systémem. Vraťte se sem a přečtěte si nové informace.

Pokud vaše VMSS má více uživatelsky přiřazených identit, můžete odebrat všechny kromě poslední z nich pomocí následujících příkazů. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VMSS NAME>` vlastními hodnotami. `<MSI NAME>` Je název vlastnosti identity přiřazené uživateli, která by měla zůstat na VMSS. Tyto informace můžete zobrazit tak v části Identita VMSS pomocí `az vmss show`:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

Pokud vaše VMSS má přiřazenou systémem a identity přiřazené uživateli, můžete odebrat všechny uživatele identit přiřazených přepnutím používat pouze přiřazenou systémem. Použijte následující příkaz:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Související obsah

- [Přehled spravované Identity služby](overview.md)
- Úplné vytvoření virtuálního počítače Azure rychlých startů naleznete v tématu:
  
  - [Vytvoření virtuálního počítače s Windows pomocí Powershellu](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Vytvoření virtuálního počítače s Linuxem pomocí Powershellu](../../virtual-machines/linux/quick-create-powershell.md) 

















