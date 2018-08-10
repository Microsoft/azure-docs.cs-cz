---
title: Instalace aplikace Symantec Endpoint Protection na Windows virtuální počítač v Azure | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat a nakonfigurovat Symantec Endpoint Protection rozšíření zabezpečení na nového nebo existujícího virtuálního počítače Azure vytvořené pomocí modelu nasazení Classic.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
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
ms.author: roiyz
ms.openlocfilehash: 455ca0ed1de5df3a184d900aeae286dfd5233a60
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716504"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Jak nainstalovat a nakonfigurovat Symantec Endpoint Protection na virtuálním počítači s Windows
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Tento článek ukazuje, jak nainstalovat a nakonfigurovat Symantec Endpoint Protection klienta na existujícího virtuálního počítače (VM) s Windows serverem. Tato úplného klienta zahrnuje služby, jako je virů a spywaru ochrany, brány firewall a prevenci neoprávněných vniknutí. Klientovi je nainstalován jako rozšíření zabezpečení pomocí agenta virtuálního počítače.

Pokud máte stávající předplatné od společnosti Symantec pro místní řešení, můžete k ochraně virtuálních počítačů Azure. Pokud vám zákazník ještě nejsou, můžete zaregistrovat zkušební předplatné. Další informace o tomto řešení najdete v tématu [Symantec Endpoint Protection na platformu Microsoft Azure][Symantec]. Tato stránka obsahuje také odkazy na licenční informace a pokyny pro instalaci klienta, pokud jste již zákazníků Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Instalace aplikace Symantec Endpoint Protection na existující virtuální počítač
Než začnete, budete potřebovat následující:

* Modul Azure PowerShell, verze 0.8.2 nebo novějších systémů na počítači v práci. Můžete zkontrolovat verzi Azure Powershellu, kterou jste nainstalovali pomocí **Get-Module azure | format-table verze** příkazu. Pokyny a odkaz na nejnovější verzi najdete v tématu [instalace a konfigurace prostředí Azure PowerShell][PS]. Přihlaste se k předplatnému Azure pomocí `Add-AzureAccount`.
* Agent virtuálního počítače spuštěného na virtuálním počítači Azure.

Nejprve ověřte, že Agent virtuálního počítače je už nainstalovaný na virtuálním počítači. Zadejte název cloudové služby a název virtuálního počítače a pak spusťte následující příkazy příkazového řádku Azure PowerShell na úrovni správce. Nahradit vše, co v uvozovkách, včetně < a > znaků.

> [!TIP]
> Pokud si nejste jisti, cloudovou službu a názvy virtuálních počítačů, spusťte **Get-AzureVM** vypsat názvy všech virtuálních počítačů v aktuálním předplatném.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Pokud **write-host** příkaz zobrazí **True**, je nainstalovaný Agent virtuálního počítače. Pokud se zobrazí **False**, podívejte se pokyny a odkaz ke stažení v příspěvku na blogu Azure [agenta virtuálního počítače a rozšíření – část 2][Agent].

Pokud je nainstalovaný Agent virtuálního počítače, spusťte tyto příkazy pro instalaci agenta aplikace Symantec Endpoint Protection.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Pokud chcete ověřit, že rozšíření Symantec zabezpečení byla nainstalována a je aktuální:

1. Přihlaste se k virtuálnímu počítači. Pokyny najdete v tématu [jak se přihlásit k virtuální počítač s Windows serverem][Logon].
2. Windows Server 2008 R2, klikněte na tlačítko **Start > Symantec Endpoint Protection**. Pro Windows Server 2012 nebo Windows Server 2012 R2, na obrazovce start zadejte **Symantec**a potom klikněte na tlačítko **Symantec Endpoint Protection**.
3. Z **stav** karty **stav Symantec Endpoint Protection** okna, aktualizace nebo v případě potřeby restartovat.

## <a name="additional-resources"></a>Další zdroje informací:
[Jak se přihlásit k virtuálnímu počítači s Windows serverem][Logon]

[Funkce a rozšíření virtuálních počítačů Azure][Ext]

<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493
