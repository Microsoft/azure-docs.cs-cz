---
title: Resetování služby Vzdálená plocha nebo jejího hesla správce ve virtuálním počítači s Windows | Microsoft Docs
description: Přečtěte si, jak resetovat heslo účtu nebo službu Vzdálená plocha na virtuálním počítači s Windows pomocí Azure Portal nebo Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 80bcac36271d86334e11cb3cd161cd96777e6fe5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87832446"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Resetování služby Vzdálená plocha nebo jejího hesla správce ve virtuálním počítači s Windows
Pokud se nemůžete připojit k virtuálnímu počítači s Windows (VM), můžete resetovat heslo místního správce nebo Resetovat konfiguraci služby Vzdálená plocha (nepodporuje se na řadičích domény se systémem Windows). Heslo můžete resetovat pomocí webu Azure Portal nebo rozšíření VMAccess v Azure PowerShellu. Po přihlášení k virtuálnímu počítači resetujte heslo příslušného místního správce.  
Pokud používáte PowerShell, ujistěte se, že máte [nainstalovaný a nakonfigurovaný nejnovější modul prostředí PowerShell](/powershell/azure/) a že jste přihlášeni ke svému předplatnému Azure. Můžete také [provést tento postup určený pro virtuální počítače vytvořené pomocí modelu nasazení Classic](/previous-versions/azure/virtual-machines/windows/classic/reset-rdp).

Vzdálenou plochu a přihlašovací údaje můžete resetovat následujícími způsoby:

- [Resetování pomocí Azure Portal](#reset-by-using-the-azure-portal)

- [Resetování pomocí rozšíření VMAccess a PowerShellu](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Resetování pomocí Azure Portal

Nejdřív se přihlaste k [Azure Portal](https://portal.azure.com) a v levé nabídce vyberte **virtuální počítače** . 

### <a name="reset-the-local-administrator-account-password"></a>**Resetovat heslo účtu místního správce**

1. Vyberte svůj virtuální počítač s Windows a potom v části **Podpora a řešení potíží**vyberte **resetovat heslo** . Zobrazí se okno **resetovat heslo** .

2. Vyberte **resetovat heslo**, zadejte uživatelské jméno a heslo a pak vyberte **aktualizovat**. 

3. Zkuste se znovu připojit ke svému VIRTUÁLNÍmu počítači.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Resetování konfigurace služby Vzdálená plocha**

Tento proces povolí na virtuálním počítači službu Vzdálená plocha a vytvoří pravidlo brány firewall pro výchozí port RDP 3389.

1. Vyberte svůj virtuální počítač s Windows a potom v části **Podpora a řešení potíží**vyberte **resetovat heslo** . Zobrazí se okno **resetovat heslo** . 

2. Vyberte možnost **resetovat jenom konfiguraci** a pak vyberte **aktualizovat**. 

3. Zkuste se znovu připojit ke svému VIRTUÁLNÍmu počítači.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Resetování pomocí rozšíření VMAccess a PowerShellu

Nejdřív se ujistěte, že máte [nainstalovaný a nakonfigurovaný nejnovější modul prostředí PowerShell](/powershell/azure/) a že jste ke svému předplatnému Azure přihlášení pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

### <a name="reset-the-local-administrator-account-password"></a>**Resetovat heslo účtu místního správce**

- Obnovte heslo nebo uživatelské jméno správce pomocí rutiny [set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) prostředí PowerShell. `typeHandlerVersion`Nastavení musí být 2,0 nebo vyšší, protože verze 1 je zastaralá. 

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
    > Pokud zadáte jiný název, než je aktuální účet místního správce na VIRTUÁLNÍm počítači, rozšíření VMAccess přidá účet místního správce s tímto názvem a přiřadí zadané heslo k tomuto účtu. Pokud účet místního správce ve vašem VIRTUÁLNÍm počítači existuje, rozšíření VMAccess resetuje heslo. Pokud je účet zakázaný, rozšíření VMAccess ho umožní.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Resetování konfigurace služby Vzdálená plocha**

1. Obnovte vzdálený přístup k VIRTUÁLNÍmu počítači pomocí rutiny [set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) prostředí PowerShell. V následujícím příkladu se obnoví rozšíření přístupu s názvem `myVMAccess` na virtuálním počítači s názvem `myVM` ve `myResourceGroup` skupině prostředků:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > Virtuální počítač může mít v jakémkoli okamžiku jenom jeden agent přístupu k virtuálnímu počítači. Pokud chcete nastavit vlastnosti agenta přístupu k virtuálnímu počítači, použijte `-ForceRerun` možnost. Když použijete `-ForceRerun` , ujistěte se, že používáte stejný název pro agenta přístupu k virtuálnímu počítači, který jste mohli použít v jakémkoli z předchozích příkazů.

1. Pokud se stále nemůžete vzdáleně připojit k virtuálnímu počítači, přečtěte si téma [řešení potíží s připojením ke vzdálené ploše na virtuálním počítači Azure se systémem Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pokud ztratíte připojení k řadiči domény Windows, budete ho muset obnovit z zálohování řadiče domény.

## <a name="next-steps"></a>Další kroky

- Pokud rozšíření přístupu k virtuálnímu počítači Azure nereaguje a nemůžete resetovat heslo, můžete [místní heslo systému Windows obnovit offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tato metoda je pokročilejší a vyžaduje, abyste připojili virtuální pevný disk problematického virtuálního počítače k jinému virtuálnímu počítači. Postupujte podle kroků popsaných v tomto článku a pokuste se použít metodu resetování hesla offline pouze v případě, že tyto kroky nefungují.

- [Přečtěte si o funkcích a rozšíření virtuálních počítačů Azure](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

- [Připojte se k virtuálnímu počítači Azure pomocí RDP nebo SSH](/previous-versions/azure/dn535788(v=azure.100)).

- [Řešení potíží s připojením vzdálené plochy k virtuálnímu počítači Azure se systémem Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).