---
title: Konfigurace spravovaných identit na virtuálním počítači Azure pomocí PowerShellu – Azure AD
description: Krok za krokem pro konfiguraci spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí PowerShellu.
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
ms.openlocfilehash: f24c89477d71df3f497590b49841403576343bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547212"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí PowerShellu

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, bez pověření ve vašem kódu. 

V tomto článku se pomocí PowerShellu dozvíte, jak provádět následující spravované identity pro operace prostředků Azure na virtuálním počítači Azure.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md). **Zkontrolujte rozdíl [mezi systémem přiřazenou a uživatelem přiřazenou spravovanou identitou](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Pokud jste to ještě neudělali, nainstalujte [nejnovější verzi Azure PowerShellu.](/powershell/azure/install-az-ps)

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a zakázat spravované identity přiřazené systémem pomocí Azure PowerShellu.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Povolení spravované identity přiřazené systémem při vytváření virtuálního počítače Azure

K vytvoření virtuálního počítače Azure s povolenou spravovanou identitou přiřazenou systémem potřebuje váš účet přiřazení role [Přispěvatel virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Odkazovat na jeden z následujících rychlých startů virtuálních zařízení Azure, dokončení pouze potřebné části ("Přihlásit se k Azure", "Vytvořit skupinu prostředků", "Vytvořit síťovou skupinu", "Vytvořit virtuální počítač").
    
    Když se dostanete do části "Vytvořit virtuální ho dnajevo", proveďte drobné změny syntaxe rutiny [New-AzVMConfig.](/powershell/module/az.compute/new-azvm) Nezapomeňte přidat `-AssignIdentity:$SystemAssigned` parametr pro zřízení virtuálního virtuálního zařízení s povolenou identitou přiřazenou systémem, například:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Virtuální počítač s Windows s využitím PowerShellu](../../virtual-machines/windows/quick-create-powershell.md)
   - [Vytvoření virtuálního počítače s Linuxem pomocí PowerShellu](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Povolení spravované identity přiřazené systémem na existujícím virtuálním počítači Azure

Chcete-li povolit systémem přiřazenou spravovanou identitu na virtuálním počítači, který byl původně zřízen bez něj, váš účet potřebuje přiřazení role [přispěvatele virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Přihlaste se `Connect-AzAccount`k Azure pomocí . Použijte účet, který je přidružený k předplatnému Azure, který obsahuje virtuální počítač.

   ```powershell
   Connect-AzAccount
   ```

2. Nejprve načíst vlastnosti `Get-AzVM` virtuálního počítače pomocí rutiny. Chcete-li povolit spravovanou identitu `-AssignIdentity` přiřazenou systémem, použijte přepínač na rutině [Update-AzVM:](/powershell/module/az.compute/update-azvm)

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Přidání přiřazené identity systému virtuálních mandatispovenek do skupiny

Po povolení systému přiřazenou identitu na virtuálním počítači, můžete ji přidat do skupiny.  Následující postup přidá systém virtuálního aplikace přiřazenou identitu do skupiny.

1. Přihlaste se `Connect-AzAccount`k Azure pomocí . Použijte účet, který je přidružený k předplatnému Azure, který obsahuje virtuální počítač.

   ```powershell
   Connect-AzAccount
   ```

2. Načíst a `ObjectID` poznamenejte `Id` si (jak je uvedeno v poli vrácených hodnot) instančního objektu virtuálního soudu:

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Načíst a `ObjectID` poznamenat (jak je uvedeno v `Id` poli vrácených hodnot) skupiny:

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Přidejte instanční objekt virtuálního soudu do skupiny:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Zakázání spravované identity přiřazené systému z virtuálního počítače Azure

Chcete-li zakázat systémem přiřazenou spravovanou identitu na virtuálním počítači, váš účet potřebuje přiřazení role [Přispěvatel virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

Pokud máte virtuální počítač, který už nepotřebuje spravovanou identitu přiřazenou systémem, ale stále potřebuje spravované identity přiřazené uživatelem, použijte následující rutinu:

1. Přihlaste se `Connect-AzAccount`k Azure pomocí . Použijte účet, který je přidružený k předplatnému Azure, který obsahuje virtuální počítač.

   ```powershell
   Connect-AzAccount
   ```

2. Načtěte vlastnosti `Get-AzVM` virtuálního počítače pomocí `-IdentityType` rutiny a nastavte parametr na `UserAssigned`:

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Pokud máte virtuální počítač, který už nepotřebuje spravovanou identitu přiřazenou systémem a nemá žádné spravované identity přiřazené uživateli, použijte následující příkazy:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části se dozvíte, jak přidat a odebrat uživatelem přiřazenou spravovanou identitu z virtuálního počítače pomocí Azure PowerShellu.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Přiřazení spravované identity přiřazené uživateli k virtuálnímu virtuálnímu soudu během vytváření

Chcete-li přiřadit uživatelem přiřazenou identitu k virtuálnímu počítači, váš účet potřebuje přiřazení rolí [Přispěvatel virtuálního počítače](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a [Operátor spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Odkazovat na jeden z následujících rychlých startů virtuálních zařízení Azure, dokončení pouze potřebné části ("Přihlásit se k Azure", "Vytvořit skupinu prostředků", "Vytvořit síťovou skupinu", "Vytvořit virtuální počítač"). 
  
    Když se dostanete do části "Vytvořit virtuální ho d). [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) Přidejte `-IdentityType UserAssigned` `-IdentityID` parametry a zřdávejte virtuální mu s identitou přiřazenou uživatelem.  `<VM NAME>`Nahraďte `<RESROURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>` `<SUBSCRIPTION ID>`, , a s vlastními hodnotami.  Například:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Virtuální počítač s Windows s využitím PowerShellu](../../virtual-machines/windows/quick-create-powershell.md)
    - [Vytvoření virtuálního počítače s Linuxem pomocí PowerShellu](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Přiřazení spravované identity přiřazené uživateli k existujícímu virtuálnímu počítači Azure

Chcete-li přiřadit uživatelem přiřazenou identitu k virtuálnímu počítači, váš účet potřebuje přiřazení rolí [Přispěvatel virtuálního počítače](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a [Operátor spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Přihlaste se `Connect-AzAccount`k Azure pomocí . Použijte účet, který je přidružený k předplatnému Azure, který obsahuje virtuální počítač.

   ```powershell
   Connect-AzAccount
   ```

2. Vytvořte spravovanou identitu přiřazenou uživatelem pomocí [rutiny New-AzUserAssignedIdentity.](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity)  Poznámka: `Id` ve výstupu, protože budete potřebovat v dalším kroku.

   > [!IMPORTANT]
   > Vytváření spravovaných identit přiřazených uživatelem podporuje pouze alfanumerické znaky, podtržítka a pomlčka (0-9 nebo a-z nebo A-Z \_ nebo -). Kromě toho název by měl být omezen od 3 do 128 délka znaků pro přiřazení k VM/VMSS pracovat správně. Další informace naleznete [v častých dotazech a známých problémech](known-issues.md)

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Načíst vlastnosti virtuálního počítače pomocí rutiny. `Get-AzVM` Chcete-li pak přiřadit uživatelem přiřazenou spravovanou `-IdentityType` identitu virtuálnímu počítači Azure, použijte rutinu `-IdentityID` [Update-AzVM](/powershell/module/az.compute/update-azvm) a přepněte ji.  Hodnota parametru`-IdentityId` je `Id` uvedená v předchozím kroku.  `<VM NAME>`Nahraďte `<RESROURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>` `<SUBSCRIPTION ID>`, , a s vlastními hodnotami.

   > [!WARNING]
   > Chcete-li zachovat všechny dříve uživatelem přiřazené spravované `Identity` identity přiřazené virtuálnímu virtuálnímu hotelu, dotaz na vlastnost objektu virtuálního uživatele `$vm.Identity`(například).  Pokud jsou vráceny všechny uživatele přiřazené spravované identity, zahrnout je do následujícího příkazu spolu s novým uživatelem přiřazenslanou identitu, kterou chcete přiřadit k virtuálnímu jemu.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Odebrání spravované identity přiřazené uživateli z virtuálního počítače Azure

Chcete-li odebrat uživatelem přiřazenou identitu virtuálnímu počítači, váš účet potřebuje přiřazení role [Přispěvatel virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

Pokud váš virtuální počítač má více uživatelem přiřazené spravované identity, můžete odebrat všechny kromě poslední pomocí následujících příkazů. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VM NAME>` vlastními hodnotami. Je `<USER ASSIGNED IDENTITY NAME>` uživatelem přiřazená vlastnost názvu spravované identity, která by měla zůstat na virtuálním počítači. Tyto informace lze nalézt dotazem na `Identity` vlastnost objektu virtuálního zařízení.  Například: `$vm.Identity`

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Pokud váš virtuální počítač nemá spravanou identitu přiřazenou systémem a chcete z něj odebrat všechny spravované identity přiřazené uživatelem, použijte následující příkaz:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Pokud má váš virtuální počítač přiřazené i uživatelem přiřazené spravované identity, můžete odebrat všechny spravované identity přiřazené uživateli tak, že přepnete na použití pouze spravovaných identit přiřazených systémem.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Další kroky

- [Přehled spravovaných identit pro prostředky Azure](overview.md)
- Pro úplné vytvoření virtuálního počítače Azure Rychlé starty, najdete v tématu:
  
  - [Vytvoření virtuálního počítače s Windows pomocí PowerShellu](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Vytvoření virtuálního počítače s Linuxem pomocí PowerShellu](../../virtual-machines/linux/quick-create-powershell.md) 
