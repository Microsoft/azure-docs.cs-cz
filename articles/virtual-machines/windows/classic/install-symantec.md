---
title: Nainstalovat službu Symantec Endpoint Protection v systému Windows virtuálního počítače v Azure | Microsoft Docs
description: Zjistěte, jak nainstalovat a nakonfigurovat rozšíření zabezpečení Symantec Endpoint Protection na nového nebo existujícího virtuálního počítače Azure vytvořené pomocí modelu nasazení Classic.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: iainfou
ms.openlocfilehash: bd96cb975bfc30b2561a98a950a9dd3fc060fa54
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Jak nainstalovat a nakonfigurovat Symantec Endpoint Protection na virtuálním počítači s Windows
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Tento článek ukazuje, jak nainstalovat a nakonfigurovat klienta Symantec Endpoint Protection na existující virtuální počítač (VM) s Windows serverem. Tato úplná klienta zahrnuje služby jako virů a spywaru ochrany, brány firewall a prevence vniknutí. Klient je nainstalován jako rozšíření zabezpečení pomocí agenta virtuálního počítače.

Pokud máte stávající předplatné od společnosti Symantec pro místní řešení, můžete chránit virtuální počítače Azure. Pokud vám ještě nejsou zákazníka, můžete zaregistrovat zkušební předplatné. Další informace o tomto řešení najdete v tématu [Symantec Endpoint Protection na platformě Azure společnosti Microsoft][Symantec]. Tato stránka také obsahuje odkazy na licenční informace a pokyny pro instalaci klienta, pokud jste již zákazník Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Nainstalujte Symantec Endpoint Protection na existující virtuální počítač
Než začnete, budete potřebovat následující:

* Modul Azure PowerShell, verze 0.8.2 nebo novější, na počítači v práci. Můžete zkontrolovat verzi prostředí Azure PowerShell, který jste nainstalovali s **Get-Module azure | verze formátu tabulky** příkaz. Pokyny a odkaz na nejnovější verzi naleznete v tématu [postup instalace a konfigurace prostředí Azure PowerShell][PS]. Přihlaste se k předplatnému Azure pomocí `Add-AzureAccount`.
* Virtuální počítač agenta spuštěného na virtuálním počítači Azure.

Nejprve ověří, že je na virtuálním počítači již nainstalován Agent virtuálního počítače. Zadejte název cloudové služby a název virtuálního počítače a potom spusťte následující příkazy příkazového řádku Azure PowerShell úrovni správce. Nahraďte všechna data v uvozovkách, včetně < a > znaků.

> [!TIP]
> Pokud si nejste jisti, Cloudová služba a názvy virtuálních počítačů, spusťte **Get-AzureVM** seznam názvy pro všechny virtuální počítače v aktuálním předplatném.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Pokud **zápisu hostitele** příkaz zobrazí **True**, je nainstalovaný Agent virtuálního počítače. Pokud se zobrazí **False**, najdete pokyny a odkaz na stažení v Azure příspěvku na blogu [agenta virtuálního počítače a rozšíření – část 2][Agent].

Pokud je nainstalován Agent virtuálního počítače, spusťte tyto příkazy pro instalaci agenta Symantec Endpoint Protection.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Chcete-li ověřit, že rozšíření zabezpečení Symantec byl nainstalován a zda je aktuální:

1. Přihlaste se k virtuálnímu počítači. Pokyny najdete v tématu [postup Přihlaste se k Windows serveru spuštěný virtuální počítač][Logon].
2. Windows Server 2008 R2, klikněte na tlačítko **Start > Symantec Endpoint Protection**. Windows Server 2012 nebo Windows Server 2012 R2, na obrazovce start zadejte **Symantec**a potom klikněte na **Symantec Endpoint Protection**.
3. Z **stav** kartě **stav Symantec Endpoint Protection** časové období, aktualizace nebo v případě potřeby restartovat.

## <a name="additional-resources"></a>Další zdroje informací:
[Postup Přihlaste se k virtuálního počítače se systémem Windows Server][Logon]

[Rozšíření virtuálního počítače Azure a funkce][Ext]

<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493
