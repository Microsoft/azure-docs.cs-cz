---
title: Resetovat heslo nebo konfigurace vzdálené plochy na virtuálním počítači s Windows v Azure | Dokumentace Microsoftu
description: Zjistěte, jak resetovat heslo k účtu nebo služby Vzdálená plocha na virtuálním počítači s Windows, vytvořené pomocí modelu nasazení Classic pomocí webu Azure portal nebo Azure Powershellu.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: bbe8059b3a239570c2c9b25586dae9adbe25312d
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931374"
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>Jak obnovit službu Vzdálená plocha nebo jeho heslo přihlášení do virtuálního počítače s Windows, vytvořené pomocí modelu nasazení Classic
> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a classic](../../../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Můžete také [proveďte tyto kroky pro virtuální počítače vytvořené pomocí modelu nasazení Resource Manager](../reset-rdp.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]


Pokud se nemůžete připojit k virtuálnímu počítači Windows (VM), můžete resetovat heslo místního správce nebo resetovat konfiguraci služby Vzdálená plocha. Na webu Azure portal nebo rozšíření přístupu virtuálních počítačů v prostředí Azure PowerShell můžete použít k resetování hesla.

## <a name="ways-to-reset-configuration-or-credentials"></a>Způsoby, jak resetování konfigurace nebo přihlašovací údaje
Služby Vzdálená plocha a přihlašovací údaje můžete resetovat několika různými způsoby podle svých potřeb:

- [Resetovat pomocí webu Azure portal](#azure-portal)
- [Obnovit pomocí Azure Powershellu](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Můžete použít [webu Azure portal](https://portal.azure.com) obnovíte službu Vzdálená plocha. Rozbalte nabídku portálu, klikněte na tři pruhy v levém horním rohu a pak klikněte na **virtuální počítače (classic)**:

![Procházet pro váš virtuální počítač Azure](./media/reset-rdp/Portal-Select-Classic-VM.png)

Vyberte svůj virtuální počítač Windows a pak klikněte na tlačítko **resetovat vzdálený přístup...** . Chcete-li obnovit konfiguraci vzdálené plochy se zobrazí následující dialogové okno:

![Stránka Konfigurace RDP pro resetování](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

Můžete také resetovat uživatelské jméno a heslo účtu místního správce. Z virtuálního počítače, klikněte na tlačítko **podpora a řešení potíží** > **resetovat heslo**. Zobrazí se okno resetování hesla:

![Stránku pro resetování hesla](./media/reset-rdp/Portal-PW-Reset-Windows.png)

Jakmile zadáte nové uživatelské jméno a heslo, klikněte na tlačítko **Uložit**.

## <a name="vmaccess-extension-and-powershell"></a>Rozšíření VMAccess a prostředí PowerShell
Ujistěte se, že na virtuálním počítači je nainstalovaný Agent virtuálního počítače. Rozšíření VMAccess nemusí být nainstalována před použitím, za předpokladu, že Agent virtuálního počítače je k dispozici. Ověřte, že Agent virtuálního počítače je již nainstalována pomocí následujícího příkazu. (Nahradit "myCloudService" a "myVM" názvy cloudové služby a virtuální počítač, v uvedeném pořadí. Další názvy těchto spuštěním `Get-AzureVM` bez parametrů.)

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Pokud **write-host** příkaz zobrazí **True**, je nainstalovaný Agent virtuálního počítače. Pokud se zobrazí **False**, najdete v článku pokynů a odkaz ke stažení v [agenta virtuálního počítače a rozšíření – část 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) příspěvku na blogu Azure.

Pokud jste vytvořili virtuální počítač pomocí portálu, zkontrolujte, zda `$vm.GetInstance().ProvisionGuestAgent` vrátí **True**. Pokud ne, můžete ho nastavit pomocí tohoto příkazu:

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

Tento příkaz brání následující chybu, když máte spuštěného **Set-AzureVMExtension** příkaz v dalších krocích: "Agent hosta zřizování musí být povolena u objektu VM před nastavením rozšíření přístupu virtuálních počítačů IaaS."

### <a name="reset-the-local-administrator-account-password"></a>**Resetování hesla účtu místního správce**
Vytvoření přihlašovacích údajů přihlásit s aktuálním názvem účtu místního správce a nové heslo a pak spusťte `Set-AzureVMAccessExtension` následujícím způsobem.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Pokud zadáte jiný název než aktuálního účtu, rozšíření VMAccess přejmenuje místní účet správce, přiřadí heslo k tomuto účtu a vydá vzdálené plochy odhlašování. Pokud je účet místního správce zakázán, rozšíření VMAccess povolí ho.

Tyto příkazy také resetovat konfiguraci služby Vzdálená plocha.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Resetovat konfiguraci služby Vzdálená plocha**
Chcete-li obnovit konfiguraci služby Vzdálená plocha, spusťte následující příkaz:

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

Rozšíření VMAccess běží na virtuálním počítači dva příkazy:

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

Tento příkaz povolí předdefinované skupiny brány Windows Firewall, která umožní příchozí provoz vzdálené plochy, která používá TCP port 3389.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

Tento příkaz nastaví fDenyTSConnections hodnotu registru na hodnotu 0, povolení připojení ke vzdálené ploše.

## <a name="next-steps"></a>Další postup
Pokud se neobjeví odpověď rozšíření přístupu virtuálních počítačů Azure a nemůžete resetovat heslo, můžete [resetování místního hesla Windows do režimu offline](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tato metoda je pokročilejší proces a vyžaduje, abyste k jinému virtuálnímu počítači připojit virtuální pevný disk problematického virtuálního počítače. Postupujte podle kroků popsaných v tomto článku nejprve a pouze pokusí metoda obnovení offline heslo jako poslední možnost.

[Funkce a rozšíření virtuálních počítačů Azure](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Připojte se k virtuálnímu počítači Azure pomocí RDP nebo SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Řešení potíží s připojeními vzdálené plochy k virtuálnímu počítači s Windows v Azure](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

