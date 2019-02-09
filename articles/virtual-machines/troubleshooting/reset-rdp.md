---
title: Resetování služby Vzdálená plocha nebo jeho heslo správce virtuálního počítače s Windows | Dokumentace Microsoftu
description: Zjistěte, jak obnovit heslo k účtu nebo služby Vzdálená plocha na virtuálním počítači s Windows pomocí webu Azure portal nebo Azure Powershellu.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 875f2d9dbbece4e9587462c6e8bdb2b2d8536c86
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979883"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Resetování služby Vzdálená plocha nebo jeho heslo správce virtuálního počítače s Windows
Pokud se nemůžete připojit k virtuálnímu počítači Windows (VM), můžete resetovat heslo místního správce nebo resetujte konfiguraci služby Vzdálená plocha (není podporováno ve Windows řadiče domény). Heslo můžete resetovat pomocí webu Azure Portal nebo rozšíření VMAccess v Azure PowerShellu. Po přihlášení k virtuálnímu počítači resetujte heslo příslušného místního správce.  
Pokud používáte PowerShell, ujistěte se, že máte [nejnovější modul Powershellu nainstalovanou a nakonfigurovanou](/powershell/azure/overview) a přihlášeni ke svému předplatnému Azure. Můžete také [provést tento postup určený pro virtuální počítače vytvořené pomocí modelu nasazení Classic](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

Vzdálená plocha a přihlašovací údaje můžete resetovat následujícími způsoby:

- [Resetovat pomocí webu Azure portal](#reset-by-using-the-azure-portal)

- [Resetovat pomocí rozšíření VMAccess a prostředí PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Resetovat pomocí webu Azure portal

Nejprve se přihlaste k [webu Azure portal](https://portal.azure.com) a pak vyberte **virtuálních počítačů** v nabídce vlevo. 

### <a name="reset-the-local-administrator-account-password"></a>**Resetování hesla účtu místního správce**

1. Vyberte svůj virtuální počítač Windows a pak vyberte **resetovat heslo** pod **podpora a řešení potíží**. **Resetovat heslo** se zobrazí okno.

1. Vyberte **resetovat heslo**, zadejte uživatelské jméno a heslo a pak vyberte **aktualizace**. 

1. Zkuste se znovu připojit k virtuálnímu počítači.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Resetovat konfiguraci služby Vzdálená plocha**

1. Vyberte svůj virtuální počítač Windows a pak vyberte **resetovat heslo** pod **podpora a řešení potíží**. **Resetovat heslo** se zobrazí okno. 

1. Vyberte **resetovat jenom konfiguraci** a pak vyberte **aktualizace**. 

1. Zkuste se znovu připojit k virtuálnímu počítači.


## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Resetovat pomocí rozšíření VMAccess a prostředí PowerShell

Nejprve zkontrolujte, že máte [nejnovější modul Powershellu nainstalovanou a nakonfigurovanou](/powershell/azure/overview) a přihlášeni ke svému předplatnému Azure pomocí [připojit AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) rutiny.

### <a name="reset-the-local-administrator-account-password"></a>**Resetování hesla účtu místního správce**

- Obnovení správce heslo nebo uživatelské jméno s [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) rutiny Powershellu. `typeHandlerVersion` Nastavení musí být 2.0 nebo novější, protože verze 1 je zastaralý. 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzAccount 
    Select-AzSubscription -SubscriptionId $SubID 
    Set-AzVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > Pokud zadáte jiný název než aktuální účet místního správce na svém virtuálním počítači, bude rozšíření VMAccess přidejte účet místního správce s tímto názvem a přiřadit zadané heslo k tomuto účtu. Pokud existuje účet místního správce na svém virtuálním počítači, bude rozšíření VMAccess resetovat heslo. Pokud účet je zakázaný, rozšíření VMAccess ji povolit.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Resetovat konfiguraci služby Vzdálená plocha**

1. Resetování vzdáleného přístupu k vašemu virtuálnímu počítači s [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) rutiny Powershellu. Následující příklad resetuje rozšíření přístupu s názvem `myVMAccess` na virtuálním počítači s názvem `myVM` v `myResourceGroup` skupina prostředků:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > V libovolném bodě virtuální počítač může mít pouze jednoho agenta virtuálního počítače přístup. Chcete-li nastavit vlastnosti agenta přístup k virtuálním počítačům, použijte `-ForceRerun` možnost. Při použití `-ForceRerun`, používají stejný název pro přístup k agenta virtuálního počítače, který jste možná použili ve všech předchozích příkazů.

1. Pokud se pořád nemůžete připojit vzdáleně ke svému virtuálnímu počítači, přečtěte si téma [připojení řešení potíží s vzdálené plochy k virtuálnímu počítači s Windows v Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pokud ztratíte připojení k řadiči domény Windows, je potřeba ho obnovit ze zálohy řadiče domény.

## <a name="next-steps"></a>Další postup

- Pokud nereaguje rozšíření přístupu virtuálních počítačů Azure a budete moct resetovat heslo, můžete [resetování místního hesla Windows do režimu offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tato metoda je složitější a vyžaduje, abyste k jinému virtuálnímu počítači připojit virtuální pevný disk problematického virtuálního počítače. Postupujte podle kroků popsaných v tomto článku nejprve a pokusit se metoda offline heslo resetovat jenom v případě, že tyto kroky nefungují.

- [Další informace o rozšíření virtuálních počítačů Azure a funkce](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

- [Připojte se k virtuálnímu počítači Azure pomocí RDP nebo SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

- [Řešení potíží s připojeními vzdálené plochy k virtuálnímu počítači s Windows v Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

