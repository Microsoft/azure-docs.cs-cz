---
title: Konfigurace spravovaných identit ve službě Virtual Machine Scale Sets pomocí prostředí PowerShell – Azure AD
description: Podrobné pokyny pro konfiguraci spravovaných identit systémových a uživatelem v sadě škálování virtuálního počítače pomocí PowerShellu
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd7ec1f6cdfc8ecfe32de04f5d06b42f9492b88c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590898"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Konfigurace spravovaných identit pro prostředky Azure ve službě Virtual Machine Scale Sets pomocí PowerShellu

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se naučíte, jak provádět spravované identity prostředků Azure v sadě škálování virtuálního počítače pomocí PowerShellu:

- Povolí nebo zakáže spravovanou identitu přiřazenou systémem v sadě škálování virtuálního počítače.
- Přidání a odebrání spravované identity přiřazené uživatelem v sadě škálování virtuálního počítače

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi přiřazenou identitou systému a uživatelem spravované identity](overview.md#managed-identity-types)**.

- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

- K provedení operací správy v tomto článku potřebuje váš účet následující přiřazení řízení přístupu na základě role Azure:

    > [!NOTE]
    > Nevyžadují se žádné další přiřazení role adresáře Azure AD.

    - [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) , aby vytvořil sadu škálování virtuálního počítače a povolil a odebral spravovanou identitu přiřazenou systémem nebo uživatelem přiřazenou ze sady škálování virtuálního počítače.
    - Role [Přispěvatel spravovaných identit](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) k vytvoření spravované identity přiřazené uživatelem.
    - Role [operátora spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) k přiřazení a odebrání spravované identity přiřazené uživatelem z a do sady škálování virtuálního počítače.

- Chcete-li spustit ukázkové skripty, máte dvě možnosti:
    - Použijte [Azure Cloud Shell](../../cloud-shell/overview.md), který můžete otevřít pomocí tlačítka **vyzkoušet** v pravém horním rohu bloků kódu.
    - Spusťte skripty místně pomocí instalace nejnovější verze [Azure PowerShell](/powershell/azure/install-az-ps)a pak se přihlaste k Azure pomocí `Connect-AzAccount` . 

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a odebrat spravovanou identitu přiřazenou systémem pomocí Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Povolit spravovanou identitu přiřazenou systémem během vytváření sady škálování virtuálních počítačů Azure

Vytvoření sady škálování virtuálního počítače s povolenou spravovanou identitou přiřazenou systémem:

1. Pokud chcete vytvořit sadu škálování virtuálního počítače pomocí spravované identity přiřazené systémem, podívejte se na *Příklad 1* v referenčním článku k rutině [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) .  Přidejte parametr `-IdentityType SystemAssigned` do `New-AzVmssConfig` rutiny:

    ```azurepowershell-interactive
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Povolit spravovanou identitu přiřazenou systémem v existující sadě škálování virtuálních počítačů Azure

Pokud potřebujete povolit spravovanou identitu přiřazenou systémem v existující sadě škálování virtuálních počítačů Azure:

1. Ujistěte se, že účet Azure, který používáte, patří do role, která vám poskytne oprávnění k zápisu do sady škálování virtuálních počítačů, jako je například "Přispěvatel virtuálního počítače".
   
1. Pomocí rutiny načtěte vlastnosti sady škálování virtuálního počítače [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) . Pokud chcete povolit spravovanou identitu přiřazenou systémem, použijte `-IdentityType` přepínač v rutině [Update-AzVmss](/powershell/module/az.compute/update-azvmss) :

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Zakažte spravovanou identitu přiřazenou systémem ze sady škálování virtuálních počítačů Azure.

Pokud máte sadu škálování virtuálního počítače, která už nepotřebuje spravovanou identitu přiřazenou systémem, ale potřebuje spravované identity přiřazené uživatelem, použijte tuto rutinu:

1. Ujistěte se, že váš účet patří do role, která vám poskytne oprávnění k zápisu do sady škálování virtuálních počítačů, jako je například Přispěvatel virtuálního počítače.

1. Spusťte následující rutinu:

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

1. Pokud máte sadu škálování virtuálního počítače, která už nepotřebuje spravovanou identitu přiřazenou systémem a nemá žádné spravované identity přiřazené uživatelem, použijte následující příkaz:

    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
    ```
    
## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části se dozvíte, jak přidat a odebrat spravovanou identitu přiřazenou uživatelem ze sady škálování virtuálních počítačů pomocí Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživatelem během vytváření sady škálování virtuálních počítačů Azure

Vytvoření nové sady škálování virtuálního počítače s uživatelsky přiřazenou spravovanou identitou se v tuto chvíli nepodporuje přes PowerShell. V další části najdete informace o tom, jak přidat spravovanou identitu přiřazenou uživatelem do existující sady škálování virtuálního počítače. Vraťte se sem a přečtěte si nové informace.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživatelem do existující sady škálování virtuálních počítačů Azure

Přiřazení spravované identity přiřazené uživatelem do existující sady škálování virtuálních počítačů Azure:

1. Ujistěte se, že váš účet patří do role, která vám poskytne oprávnění k zápisu do sady škálování virtuálních počítačů, jako je například Přispěvatel virtuálního počítače.

1. Pomocí rutiny načtěte vlastnosti sady škálování virtuálního počítače `Get-AzVM` . Pak k přiřazení spravované identity přiřazené uživatelem do sady škálování virtuálního počítače použijte `-IdentityType` `-IdentityID` přepínač a v rutině [Update-AzVmss](/powershell/module/az.compute/update-azvmss) . Nahraďte,,, `<VM NAME>` `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` `<USER ASSIGNED ID1>` , `USER ASSIGNED ID2` vlastními hodnotami.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Odebrání spravované identity přiřazené uživatelem ze sady škálování virtuálních počítačů Azure

Pokud má vaše sada škálování virtuálních počítačů více uživatelsky přiřazených spravovaných identit, můžete všechny kromě poslední odebrat pomocí následujících příkazů. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VIRTUAL MACHINE SCALE SET NAME>` vlastními hodnotami. `<USER ASSIGNED IDENTITY NAME>`Je vlastnost název spravované identity přiřazené uživatelem, která by měla zůstat v sadě škálování virtuálního počítače. Tyto informace najdete v části Identita sady škálování virtuálních počítačů pomocí `az vmss show` :

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Pokud vaše sada škálování virtuálního počítače nemá spravovanou identitu přiřazenou systémem a chcete z ní odebrat všechny spravované identity přiřazené uživatelem, použijte následující příkaz:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Pokud vaše sada škálování virtuálního počítače obsahuje spravované identity přiřazené systémem i uživatelem, můžete odebrat všechny spravované identity přiřazené uživatelem, a to přepnutím na použití spravované identity přiřazené systémem.

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Další kroky

- [Přehled spravovaných identit pro prostředky Azure](overview.md)
- Kompletní rychlé starty pro vytváření virtuálních počítačů Azure najdete v těchto tématech:
  
  - [Vytvoření virtuálního počítače s Windows pomocí PowerShellu](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Vytvoření virtuálního počítače s Linuxem pomocí PowerShellu](../../virtual-machines/linux/quick-create-powershell.md)
