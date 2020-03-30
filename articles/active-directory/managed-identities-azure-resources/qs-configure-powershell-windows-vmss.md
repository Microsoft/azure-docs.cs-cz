---
title: Konfigurace spravovaných identit na škálovacích sadách virtuálních strojů pomocí PowerShellu – Azure AD
description: Krok za krokem pro konfiguraci systémových a uživatelem přiřazených spravovaných identit ve škálovací sadě virtuálních strojů pomocí prostředí PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 755aee312fd0492fd57a82cb7a437b04ebf72987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547272"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Konfigurace spravovaných identit pro prostředky Azure na škálovacích sadách virtuálních strojů pomocí PowerShellu

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, bez pověření ve vašem kódu. 

V tomto článku se pomocí PowerShellu dozvíte, jak provádět spravované identity pro operace prostředků Azure na škálovací sadě virtuálních strojů:
- Povolení a zakázání spravované identity přiřazené k systému ve škálovací sadě virtuálních strojů
- Přidání a odebrání spravované identity přiřazené uživateli ve škálovací sadě virtuálních strojů

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md). **Zkontrolujte rozdíl [mezi přiřazenou systémem a uživatelem spravovanou přiřazenou identitou](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- K provedení operací správy v tomto článku váš účet potřebuje následující přiřazení řízení přístupu azure na základě rolí:

    > [!NOTE]
    > Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

    - [Přispěvatel virtuálního počítače](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) pro vytvoření škálovací sady virtuálních strojů a povolení a odebrání spravované a/nebo uživatelem přiřazené spravované identity ze sady škálovací sady virtuálních strojů.
    - [Role spravovaného přispěvatele identity](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) k vytvoření spravované identity přiřazené uživateli.
    - Role [spravovaného operátora identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) pro přiřazení a odebrání spravované identity přiřazené uživateli z škálovací sady virtuálních strojů a do ní.
- Pokud jste to ještě neudělali, nainstalujte [nejnovější verzi Azure PowerShellu.](/powershell/azure/install-az-ps) 

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a odebrat systémem přiřazenou spravovanou identitu pomocí Azure PowerShellu.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Povolení spravované identity přiřazené systémem při vytváření škálovací sady virtuálních strojů Azure

Vytvoření škálovací sady virtuálních strojů s povolenou spravovanou identitou přiřazenou systémem:

1. Odkazovat na *příklad 1* v článku odkaz na rutinu [Rutina New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) chcete-li vytvořit škálovací sadu virtuálních strojů se systémem přiřazenou spravovanou identitou.  Přidejte `-IdentityType SystemAssigned` parametr `New-AzVmssConfig` do rutiny:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```



## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Povolení spravované identity přiřazené systémem na existující škálovací sadě virtuálních strojů Azure

Pokud potřebujete povolit systémem přiřazenou spravovanou identitu na existující škálovací sadě virtuálních strojů Azure:

1. Přihlaste se `Connect-AzAccount`k Azure pomocí . Použijte účet, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních strojů. Také se ujistěte, že váš účet patří do role, která vám dává oprávnění k zápisu na škálovací sadu virtuálních strojů, například "Přispěvatel virtuálního počítače":

   ```powershell
   Connect-AzAccount
   ```

2. Nejprve načtěte vlastnosti [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) škálovací sady virtuálního počítače pomocí rutiny. Chcete-li povolit spravovanou identitu `-IdentityType` přiřazenou systémem, použijte přepínač na rutině [Update-AzVmss:](/powershell/module/az.compute/update-azvmss)

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```



### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Zakázání spravované identity přiřazené systémem ze škálovací sady virtuálních strojů Azure

Pokud máte škálovací sadu virtuálních strojů, která už nepotřebuje spravovanou identitu přiřazenou systémem, ale stále potřebuje spravované identity přiřazené uživatelem, použijte následující rutinu:

1. Přihlaste se `Connect-AzAccount`k Azure pomocí . Použijte účet, který je přidružený k předplatnému Azure, který obsahuje virtuální počítač. Také se ujistěte, že váš účet patří do role, která vám dává oprávnění k zápisu na škálovací sadu virtuálních strojů, například "Přispěvatel virtuálního počítače":

2. Spusťte následující rutinu:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Pokud máte škálovací sadu virtuálních strojů, která už nepotřebuje spravovanou identitu přiřazenou systémem a nemá žádné spravované identity přiřazené uživateli, použijte následující příkazy:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části se dozvíte, jak přidat a odebrat uživatelem přiřazenou spravovanou identitu ze škálovací sady virtuálních strojů pomocí Azure PowerShellu.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživateli při vytváření škálovací sady virtuálních strojů Azure

Vytvoření nové škálovací sady virtuálních strojů s uživatelem přiřazenou spravovanou identitou není aktuálně podporováno prostřednictvím prostředí PowerShell. Podívejte se na další část o tom, jak přidat uživatelem přiřazenou spravovanou identitu do existující škálovací sady virtuálních strojů. Vraťte se sem a přečtěte si nové informace.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživateli k existující škálovací sadě virtuálních strojů Azure

Přiřazení spravované identity přiřazené uživateli k existující škálovací sadě virtuálních strojů Azure:

1. Přihlaste se `Connect-AzAccount`k Azure pomocí . Použijte účet, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních strojů. Také se ujistěte, že váš účet patří do role, která vám dává oprávnění k zápisu na škálovací sadu virtuálních strojů, například "Přispěvatel virtuálního počítače":

   ```powershell
   Connect-AzAccount
   ```

2. Nejprve načtěte vlastnosti `Get-AzVM` škálovací sady virtuálního počítače pomocí rutiny. Chcete-li pak přiřadit uživatelem přiřazenou spravovanou identitu k škálovací sadě virtuálních strojů, použijte rutinu `-IdentityType` a `-IdentityID` zapněte rutinu Aktualizace [AzVmss.](/powershell/module/az.compute/update-azvmss) `<VM NAME>`Nahraďte `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, , vlastními hodnotami.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Odebrání spravované identity přiřazené uživateli ze škálovací sady virtuálních strojů Azure

Pokud má škálovací sada virtuálních strojů více spravovaných identit přiřazených uživatelem, můžete odebrat všechny kromě poslední pomocí následujících příkazů. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VIRTUAL MACHINE SCALE SET NAME>` vlastními hodnotami. Jedná `<USER ASSIGNED IDENTITY NAME>` se o vlastnost názvu spravované identity přiřazené uživateli, která by měla zůstat ve škálovací sadě virtuálních strojů. Tyto informace najdete v části identity škálovací `az vmss show`sady virtuálních strojů pomocí :

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Pokud škálovací sada virtuálních strojů nemá spravovanou identitu přiřazenou systémem a chcete z ní odebrat všechny spravované identity přiřazené uživatelem, použijte následující příkaz:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Pokud má škálovací sada virtuálního počítače spravované identity přiřazené systémem i uživatelem, můžete odebrat všechny spravované identity přiřazené uživatelem tak, že přepnete na použití pouze spravované identity přiřazené k systému.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Další kroky

- [Přehled spravovaných identit pro prostředky Azure](overview.md)
- Pro úplné vytvoření virtuálního počítače Azure Rychlé starty, najdete v tématu:
  
  - [Vytvoření virtuálního počítače s Windows pomocí PowerShellu](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Vytvoření virtuálního počítače s Linuxem pomocí PowerShellu](../../virtual-machines/linux/quick-create-powershell.md) 

















