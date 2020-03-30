---
title: Obnovit službu Vzdálená plocha nebo její heslo správce ve virtuálním počítači se systémem Windows | Dokumenty společnosti Microsoft
description: Zjistěte, jak resetovat heslo účtu nebo vzdálenou plochu na virtuálním počítači s Windows pomocí portálu Azure nebo Azure PowerShellu.
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
ms.openlocfilehash: 580ec443dc087f270e30856c336a5699bbf1ae71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058443"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Obnovení služby Vzdálená plocha nebo jejího hesla správce ve virtuálním počítači se systémem Windows
Pokud se nemůžete připojit k virtuálnímu počítači (VM) windows, můžete obnovit heslo místního správce nebo obnovit konfiguraci služby Vzdálená plocha (není v řadičích domény windows podporováno). Heslo můžete resetovat pomocí webu Azure Portal nebo rozšíření VMAccess v Azure PowerShellu. Po přihlášení k virtuálnímu počítači resetujte heslo příslušného místního správce.  
Pokud používáte PowerShell, ujistěte se, že máte [nainstalovaný a nakonfigurovaný nejnovější modul PowerShellu](/powershell/azure/overview) a jste přihlášení k předplatnému Azure. Můžete také [provést tento postup určený pro virtuální počítače vytvořené pomocí modelu nasazení Classic](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

Vzdálenou plochu a přihlašovací údaje můžete resetovat následujícími způsoby:

- [Resetování pomocí portálu Azure](#reset-by-using-the-azure-portal)

- [Resetování pomocí rozšíření VMAccess a prostředí PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Resetování pomocí portálu Azure

Nejdřív se přihlaste k [portálu Azure](https://portal.azure.com) a v levé nabídce vyberte **Virtuální počítače.** 

### <a name="reset-the-local-administrator-account-password"></a>**Obnovení hesla účtu místního správce**

1. Vyberte virtuální počítač s Windows a pak v části **Podpora + poradce při potížích**vyberte Obnovit **heslo.** Zobrazí se okno **Obnovit heslo.**

2. Vyberte **Obnovit heslo**, zadejte uživatelské jméno a heslo a pak vyberte **Aktualizovat**. 

3. Zkuste se znovu připojit k virtuálnímu počítači.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Obnovení konfigurace služby Vzdálená plocha**

Tento proces povolí službu Vzdálená plocha ve virtuálním počítači a vytvoří pravidlo brány firewall pro výchozí port RDP 3389.

1. Vyberte virtuální počítač s Windows a pak v části **Podpora + poradce při potížích**vyberte Obnovit **heslo.** Zobrazí se okno **Obnovit heslo.** 

2. Vyberte **pouze obnovit konfiguraci** a pak vyberte **Aktualizovat**. 

3. Zkuste se znovu připojit k virtuálnímu počítači.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Resetování pomocí rozšíření VMAccess a prostředí PowerShell

Nejprve se ujistěte, že máte [nainstalovaný a nakonfigurovaný nejnovější modul PowerShellu](/powershell/azure/overview) a jste přihlášení k předplatnému Azure pomocí rutiny [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)

### <a name="reset-the-local-administrator-account-password"></a>**Obnovení hesla účtu místního správce**

- Obnovte heslo správce nebo uživatelské jméno pomocí rutiny [prostředí PowerShell Set-AzVMAccessExtension.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) Nastavení `typeHandlerVersion` musí být 2.0 nebo vyšší, protože verze 1 je zastaralá. 

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
    > Pokud na virtuálním počítači zadáte jiný název než aktuální účet místního správce, rozšíření VMAccess přidá účet místního správce s tímto názvem a k tomuto účtu přiřadí zadané heslo. Pokud existuje účet místního správce na vašem virtuálním počítači, rozšíření VMAccess obnoví heslo. Pokud je účet zakázán, rozšíření VMAccess jej povolí.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Obnovení konfigurace služby Vzdálená plocha**

1. Obnovte vzdálený přístup k virtuálnímu počítači pomocí rutiny Prostředí PowerShell [Set-AzVMAccessExtension.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) Následující příklad obnoví rozšíření přístupu pojmenované `myVMAccess` na `myVM` virtuálním počítači pojmenované ve `myResourceGroup` skupině prostředků:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > V každém okamžiku může mít virtuální hod pouze jednoho agenta pro přístup k virtuálnímu jevu. Chcete-li nastavit vlastnosti agenta `-ForceRerun` přístupu k virtuálnímu počítače, použijte možnost. Při použití `-ForceRerun`se ujistěte, že používáte stejný název pro agenta přístupu k virtuálnímu provozu, který jste použili v předchozích příkazech.

1. Pokud se pořád nemůžete vzdáleně připojit k virtuálnímu počítači, [přečtěte si článek Poradce při potížích s připojením ke vzdálené ploše k virtuálnímu počítači Azure se systémem Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pokud dojde ke ztrátě připojení k řadiči domény systému Windows, bude nutné jej obnovit ze zálohy řadiče domény.

## <a name="next-steps"></a>Další kroky

- Pokud rozšíření přístupu k virtuálnímu počítači Azure nereaguje a nemůžete obnovit heslo, můžete [obnovit místní heslo windows offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tato metoda je pokročilejší a vyžaduje připojení virtuálního pevného disku problematického virtuálního počítače k jinému virtuálnímu počítači. Nejprve postupujte podle kroků zdokumentovaných v tomto článku a pokuste se o metodu obnovení hesla offline pouze v případě, že tyto kroky nefungují.

- [Přečtěte si o rozšířeních a funkcích virtuálních virtuálních počítače Azure](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

- [Připojte se k virtuálnímu počítači Azure pomocí RDP nebo SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

- [Poradce při potížích s připojením ke vzdálené ploše k virtuálnímu počítači Azure se systémem Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

