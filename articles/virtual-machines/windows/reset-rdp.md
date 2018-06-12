---
title: Resetovat heslo nebo konfigurace vzdálené plochy na virtuální počítač s Windows | Microsoft Docs
description: Zjistěte, jak resetovat heslo k účtu nebo služeb vzdálené plochy na virtuální počítač s Windows pomocí portálu Azure nebo Azure PowerShell.
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
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: cynthn
ms.openlocfilehash: b61b7501c94e9682a3b324488caf119ce4aad3df
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267199"
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Obnovení služby Vzdálená plocha nebo jeho heslo pro přihlášení do systému Windows virtuálního počítače
Pokud se nemůžete připojit k virtuálnímu počítači (VM) systému Windows, můžete resetovat heslo místního správce nebo resetovat konfiguraci služby Vzdálená plocha (nejsou podporovány na řadiče domény systému Windows). Portál Azure nebo rozšíření pro přístup virtuálních počítačů v prostředí Azure PowerShell můžete použít k resetování hesla. Po přihlášení do virtuálního počítače byste měli obnovit heslo pro daného uživatele.  
Pokud používáte prostředí PowerShell, ujistěte se, že máte [nejnovější modul prostředí PowerShell nainstalovaný a nakonfigurovaný](/powershell/azure/overview) a přihlášení k předplatnému Azure. Můžete také [proveďte tyto kroky pro virtuální počítače vytvořené pomocí modelu nasazení Classic](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

## <a name="ways-to-reset-configuration-or-credentials"></a>Jak obnovit konfiguraci nebo přihlašovací údaje
Vzdálená plocha a přihlašovací údaje můžete resetovat několika různými způsoby, v závislosti na vašich potřeb:

- [Resetovat pomocí portálu Azure](#azure-portal)
- [Resetovat pomocí Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Rozbalte nabídku portálu, klikněte na tři řádky v levém horním rohu a pak klikněte na **virtuální počítače**:

![Procházením vyhledejte virtuálních počítačů Azure](./media/reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**Resetovat heslo k účtu místního správce**

Vyberte virtuální počítač se systémem Windows a klikněte na **podporu + Poradce při potížích s** > **resetovat heslo**. Zobrazí se okno resetování hesla:

![Stránka pro resetování hesla](./media/reset-rdp/Portal-RM-PW-Reset-Windows.png)

Zadejte uživatelské jméno a nové heslo a potom klikněte na tlačítko **aktualizace**. Zkuste znovu připojit k virtuálnímu počítači.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Resetovat konfiguraci služby Vzdálená plocha**

Vyberte virtuální počítač se systémem Windows a klikněte na **podporu + Poradce při potížích s** > **resetovat heslo**. Zobrazí se okno resetování hesla. 

![Resetování konfigurace RDP](./media/reset-rdp/Portal-RM-RDP-Reset.png)

Vyberte **jenom resetování konfigurace** v rozevírací nabídce klikněte **aktualizace**. Zkuste znovu připojit k virtuálnímu počítači.


## <a name="vmaccess-extension-and-powershell"></a>Rozšíření VMAccess a prostředí PowerShell
Ujistěte se, že máte [nejnovější modul prostředí PowerShell nainstalovaný a nakonfigurovaný](/powershell/azure/overview) a přihlášení k předplatnému Azure s `Connect-AzureRmAccount` rutiny.

### <a name="reset-the-local-administrator-account-password"></a>**Resetovat heslo k účtu místního správce**
Resetování správce heslo nebo uživatelské jméno s [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) rutiny prostředí PowerShell. TypeHandlerVersion musí být 2.0 nebo novější, jako je zastaralý verze 1. 

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
> Pokud zadáte jiný název než aktuální účet místního správce na vašem virtuálním počítači, bude rozšíření VMAccess přidejte účet místního správce s tímto názvem a přiřaďte zadané heslo k tomuto účtu. Pokud existuje účet místního správce na vašem virtuálním počítači, resetuje heslo a pokud je účet zakázán, rozšíření VMAccess povolí ho.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Resetovat konfiguraci služby Vzdálená plocha**
Obnovte vzdálený přístup k virtuálnímu počítači pomocí [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) rutiny prostředí PowerShell. Následující příklad resetuje rozšíření pro přístup s názvem `myVMAccess` ve virtuálním počítači s názvem `myVM` v `myResourceGroup` skupiny prostředků:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> Kdykoli virtuální počítač může mít pouze jednoho agenta virtuálního počítače přístup. Chcete-li nastavit virtuální počítač přístup k vlastnosti agenta úspěšně, `-ForceRerun` možnost lze použít. Při použití `-ForceRerun`, nezapomeňte použít stejný název pro přístup agenta virtuálního počítače v rámci všech předchozích příkazů.

Pokud se pořád nedá vzdáleně připojit k virtuálnímu počítači, najdete v části Další kroky opakujte [řešení potíží s připojení ke vzdálené ploše systému Windows Azure virtuálnímu počítači](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pokud ztratíte připojení k řadiči domény systému Windows, musíte obnovit ze zálohy řadiče domény.

## <a name="next-steps"></a>Další postup
Pokud neodpovídá rozšíření přístup k virtuálnímu počítači Azure a nemůžete resetovat heslo, můžete [resetovat offline místní heslo pro Windows](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tato metoda je pokročilejší proces a vyžaduje, abyste připojit virtuální pevný disk problematické virtuálního počítače k jiným virtuálním Počítačem. Postupujte podle kroků popsaných v tomto článku nejprve a pouze pokusí metodu offline heslo resetovat jako poslední možnost.

[Rozšíření virtuálního počítače Azure a funkce](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Připojit k virtuální počítač Azure s protokolu RDP nebo SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Řešení potíží s připojení ke vzdálené ploše do systému Windows Azure virtuálního počítače](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

