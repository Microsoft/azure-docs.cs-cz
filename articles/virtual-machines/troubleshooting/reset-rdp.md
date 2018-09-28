---
title: Resetovat heslo nebo konfigurace vzdálené plochy na virtuálním počítači s Windows | Dokumentace Microsoftu
description: Zjistěte, jak resetovat heslo k účtu nebo služby Vzdálená plocha na virtuálním počítači s Windows pomocí webu Azure portal nebo Azure Powershellu.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: cynthn
ms.openlocfilehash: a8db7ef82136bae51c99bcfd2a4743e09ebf5712
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413273"
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Jak obnovit službu Vzdálená plocha nebo jeho heslo přihlášení do virtuálního počítače s Windows
Pokud se nemůžete připojit k virtuálnímu počítači Windows (VM), můžete resetovat heslo místního správce nebo resetovat konfiguraci služby Vzdálená plocha (není podporováno ve Windows řadiče domény). Na webu Azure portal nebo rozšíření přístupu virtuálních počítačů v prostředí Azure PowerShell můžete použít k resetování hesla. Po přihlášení k virtuálnímu počítači, měli byste resetovat heslo tohoto uživatele.  
Pokud používáte PowerShell, ujistěte se, že máte [nejnovější modul Powershellu nainstalovanou a nakonfigurovanou](/powershell/azure/overview) a přihlášeni ke svému předplatnému Azure. Můžete také [proveďte tyto kroky pro virtuální počítače vytvořené pomocí modelu nasazení Classic](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

## <a name="ways-to-reset-configuration-or-credentials"></a>Způsoby, jak resetování konfigurace nebo přihlašovací údaje
Služby Vzdálená plocha a přihlašovací údaje můžete resetovat několika různými způsoby podle svých potřeb:

- [Resetovat pomocí webu Azure portal](#azure-portal)
- [Obnovit pomocí Azure Powershellu](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>portál Azure
Rozbalte nabídku portálu, klikněte na tři pruhy v levém horním rohu a pak klikněte na **virtuálních počítačů**:

![Procházet pro váš virtuální počítač Azure](./media/reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**Resetování hesla účtu místního správce**

Vyberte svůj virtuální počítač Windows klikněte **podpora a řešení potíží** > **resetovat heslo**. Zobrazí se okno resetování hesla:

![Stránku pro resetování hesla](./media/reset-rdp/Portal-RM-PW-Reset-Windows.png)

Zadejte uživatelské jméno a nové heslo a pak klikněte na tlačítko **aktualizace**. Zkuste se znovu připojit k virtuálnímu počítači.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Resetovat konfiguraci služby Vzdálená plocha**

Vyberte svůj virtuální počítač Windows klikněte **podpora a řešení potíží** > **resetovat heslo**. Zobrazí se okno resetování hesla. 

![Resetovat konfiguraci RDP](./media/reset-rdp/Portal-RM-RDP-Reset.png)

Vyberte **resetovat jenom konfiguraci** z rozevírací nabídky, poté klikněte na tlačítko **aktualizace**. Zkuste se znovu připojit k virtuálnímu počítači.


## <a name="vmaccess-extension-and-powershell"></a>Rozšíření VMAccess a prostředí PowerShell
Ujistěte se, že máte [nejnovější modul Powershellu nainstalovanou a nakonfigurovanou](/powershell/azure/overview) a přihlášení k předplatnému Azure pomocí `Connect-AzureRmAccount` rutiny.

### <a name="reset-the-local-administrator-account-password"></a>**Resetování hesla účtu místního správce**
Obnovení správce heslo nebo uživatelské jméno s [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) rutiny Powershellu. TypeHandlerVersion musí být 2.0 nebo novější, jako je zastaralá verze 1. 

```powershell
$SubID = "<SUBSCRIPTION ID>" 
$RgName = "<RESOURCE GROUP NAME>" 
$VmName = "<VM NAME>" 
$Location = "<LOCATION>" 
 
Connect-AzureRmAccount 
Select-AzureRMSubscription -SubscriptionId $SubID 
Set-AzureRmVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
```

> [!NOTE] 
> Pokud zadáte jiný název než aktuální účet místního správce na svém virtuálním počítači, bude rozšíření VMAccess přidejte účet místního správce s tímto názvem a přiřadit zadané heslo k tomuto účtu. Pokud existuje účet místního správce na svém virtuálním počítači se resetuje heslo a pokud je účet zakázán, rozšíření VMAccess povolí ho.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Resetovat konfiguraci služby Vzdálená plocha**
Resetování vzdáleného přístupu k vašemu virtuálnímu počítači s [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) rutiny Powershellu. Následující příklad resetuje rozšíření přístupu s názvem `myVMAccess` na virtuálním počítači s názvem `myVM` v `myResourceGroup` skupina prostředků:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> V libovolném bodě virtuální počítač může mít pouze jednoho agenta virtuálního počítače přístup. Chcete-li nastavit přístup k virtuálním počítačům vlastnosti agenta úspěšně, `-ForceRerun` možnost se dá použít. Při použití `-ForceRerun`, je nutné použít stejný název pro agenta virtuálního počítače přístup k použití ve všech předchozích příkazů.

Pokud se pořád nemůžete připojit vzdáleně ke svému virtuálnímu počítači, přečtěte si další kroky k vyzkoušení na [připojení řešení potíží s vzdálené plochy k virtuálnímu počítači s Windows v Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pokud ztratíte připojení k řadiči domény Windows, je potřeba ho obnovit ze zálohy řadiče domény.

## <a name="next-steps"></a>Další postup
Pokud se neobjeví odpověď rozšíření přístupu virtuálních počítačů Azure a nemůžete resetovat heslo, můžete [resetování místního hesla Windows do režimu offline](../windows/reset-local-password-without-agent.md). Tato metoda je pokročilejší proces a vyžaduje, abyste k jinému virtuálnímu počítači připojit virtuální pevný disk problematického virtuálního počítače. Postupujte podle kroků popsaných v tomto článku nejprve a pouze pokusí metoda obnovení offline heslo jako poslední možnost.

[Funkce a rozšíření virtuálních počítačů Azure](../extensions/features-windows.md)

[Připojte se k virtuálnímu počítači Azure pomocí RDP nebo SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Řešení potíží s připojeními vzdálené plochy k virtuálnímu počítači s Windows v Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

