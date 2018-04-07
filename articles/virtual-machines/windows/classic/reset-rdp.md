---
title: Resetovat heslo nebo konfigurace vzdálené plochy na virtuální počítač s Windows v Azure | Microsoft Docs
description: Zjistěte, jak resetovat heslo k účtu nebo služeb vzdálené plochy na virtuální počítač s Windows vytvořené pomocí modelu nasazení Classic pomocí portálu Azure nebo Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: 516cb740f9acad19dac77db0239341b42a2b27fe
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>Obnovení služby Vzdálená plocha nebo jeho heslo pro přihlášení do systému Windows virtuálního počítače, vytvořené pomocí modelu nasazení Classic
> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Můžete také [proveďte tyto kroky pro virtuální počítače vytvořené pomocí modelu nasazení Resource Manager](../reset-rdp.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]


Pokud se nemůžete připojit k virtuálnímu počítači (VM) systému Windows, můžete resetovat heslo místního správce nebo resetovat konfiguraci služby Vzdálená plocha. Portál Azure nebo rozšíření pro přístup virtuálních počítačů v prostředí Azure PowerShell můžete použít k resetování hesla.

## <a name="ways-to-reset-configuration-or-credentials"></a>Jak obnovit konfiguraci nebo přihlašovací údaje
Vzdálená plocha a přihlašovací údaje můžete resetovat několika různými způsoby, v závislosti na vašich potřeb:

- [Resetovat pomocí portálu Azure](#azure-portal)
- [Resetovat pomocí Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Můžete použít [portál Azure](https://portal.azure.com) resetovat služby Vzdálená plocha. Rozbalte nabídku portálu, klikněte na tři řádky v levém horním rohu a pak klikněte na **virtuálních počítačů (klasické)**:

![Procházením vyhledejte virtuálních počítačů Azure](./media/reset-rdp/Portal-Select-Classic-VM.png)

Vyberte virtuální počítač se systémem Windows a pak klikněte na tlačítko **resetovat vzdálený...** . Resetování konfigurace vzdálené plochy se zobrazí dialogové okno následující:

![Stránka Konfigurace resetování protokolu RDP](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

Můžete taky resetovat uživatelské jméno a heslo účtu místního správce. Z virtuálního počítače, klikněte na tlačítko **podporu + Poradce při potížích s** > **resetovat heslo**. Zobrazí se okno resetování hesla:

![Stránka pro resetování hesla](./media/reset-rdp/Portal-PW-Reset-Windows.png)

Když zadáte nové uživatelské jméno a heslo, klikněte na tlačítko **Uložit**.

## <a name="vmaccess-extension-and-powershell"></a>Rozšíření VMAccess a prostředí PowerShell
Zkontrolujte, zda že je na virtuálním počítači nainstalovaný Agent virtuálního počítače. Rozšíření VMAccess není potřeba nainstalovat, abyste mohli používat, dokud Agent virtuálního počítače je k dispozici. Ověřte, zda je Agent virtuálního počítače již nainstalován pomocí následujícího příkazu. (Nahraďte "myCloudService" a "Můjvp" názvy cloudové služby a virtuální počítač, v uvedeném pořadí. Dozvíte se tak, že spustíte tyto názvy `Get-AzureVM` bez parametrů.)

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Pokud **zápisu hostitele** příkaz zobrazí **True**, je nainstalovaný Agent virtuálního počítače. Pokud se zobrazí **False**, najdete pokyny a odkaz na stažení v [agenta virtuálního počítače a rozšíření – část 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) Azure příspěvku na blogu.

Pokud vytvoříte virtuální počítač pomocí portálu, zkontrolujte, zda `$vm.GetInstance().ProvisionGuestAgent` vrátí **True**. Pokud ne, můžete ho nastavit pomocí tohoto příkazu:

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

Tento příkaz zabrání k následující chybě, když používáte **Set-AzureVMExtension** příkazu v dalších krocích: "Zřízení Agent hosta musí být povolené na objekt virtuálního počítače před nastavením rozšíření pro přístup virtuálních počítačů IaaS."

### <a name="reset-the-local-administrator-account-password"></a>**Resetovat heslo k účtu místního správce**
Vytvoření pověření přihlášení pomocí aktuální název účtu místního správce a nové heslo a pak spusťte `Set-AzureVMAccessExtension` následujícím způsobem.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Pokud zadáte jiný název než aktuálního účtu, rozšíření VMAccess Přejmenuje účet místního správce, přiřadí heslo k tomuto účtu a vydá odhlašování vzdálené plochy. Pokud je účet místního správce zakázán, rozšíření VMAccess povolí ho.

Tyto příkazy také obnovit konfiguraci služby Vzdálená plocha.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Resetovat konfiguraci služby Vzdálená plocha**
Chcete-li obnovit konfiguraci služby Vzdálená plocha, spusťte následující příkaz:

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

Rozšíření VMAccess běží na virtuálním počítači dva příkazy:

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

Tento příkaz povolí předdefinovaná skupina brány Windows Firewall, která umožní příchozí provoz vzdálené plochy, který používá TCP port 3389.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

Tento příkaz nastaví fDenyTSConnections hodnotu registru na 0, povolení připojení ke vzdálené ploše.

## <a name="next-steps"></a>Další postup
Pokud neodpovídá rozšíření přístup k virtuálnímu počítači Azure a nemůžete resetovat heslo, můžete [resetovat offline místní heslo pro Windows](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tato metoda je pokročilejší proces a vyžaduje, abyste připojit virtuální pevný disk problematické virtuálního počítače k jiným virtuálním Počítačem. Postupujte podle kroků popsaných v tomto článku nejprve a pouze pokusí metodu offline heslo resetovat jako poslední možnost.

[Rozšíření virtuálního počítače Azure a funkce](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Připojit k virtuální počítač Azure s protokolu RDP nebo SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Řešení potíží s připojení ke vzdálené ploše do systému Windows Azure virtuálního počítače](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

