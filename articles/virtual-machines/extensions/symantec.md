---
title: Instalace Symantec Endpoint Protection na virtuální počítač s Windows v Azure
description: Přečtěte si, jak nainstalovat a nakonfigurovat rozšíření Symantec Endpoint Protection Security na novém nebo existujícím virtuálním počítači Azure vytvořeném pomocí modelu nasazení Classic.
author: roiyz
tags: azure-service-management
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/31/2017
ms.author: akjosh
ms.openlocfilehash: a89ec99af02b32d452fba1dffb0387ae6013a7c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292432"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Jak nainstalovat a nakonfigurovat Symantec Endpoint Protection na virtuálním počítači s Windows
[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a Classic](../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

V tomto článku se dozvíte, jak nainstalovat a nakonfigurovat klienta Symantec Endpoint Protection na stávajícím virtuálním počítači s Windows serverem. Tento úplný klient zahrnuje služby, jako je ochrana před viry a spywarem, brána firewall a prevence vniknutí. Klient se nainstaluje jako rozšíření zabezpečení pomocí agenta virtuálního počítače.

Pokud máte stávající předplatné od Symantecu pro místní řešení, můžete ho použít k ochraně virtuálních počítačů Azure. Pokud ještě nejste zákazník, můžete si zaregistrovat zkušební verzi předplatného. Další informace o tomto řešení najdete v tématu [Symantec Endpoint Protection na platformě Azure od Microsoftu][Symantec]. Tato stránka obsahuje také odkazy na informace o licencování a pokyny pro instalaci klienta, pokud už jste zákazníkem Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Instalace Symantec Endpoint Protection na stávajícím virtuálním počítači
Než začnete, budete potřebovat následující:

* Azure PowerShell modul verze 0.8.2 nebo novější, ve vašem pracovním počítači. Verzi Azure PowerShell, kterou jste nainstalovali, můžete zjistit pomocí příkazu **Get-Module Azure | Format-Table Version** . Pokyny a odkaz na nejnovější verzi najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell][PS]. Přihlaste se k předplatnému Azure pomocí `Add-AzureAccount` .
* Agent virtuálního počítače běžící na virtuálním počítači Azure.

Nejdřív ověřte, že je na virtuálním počítači už nainstalovaný agent virtuálního počítače. Zadejte název cloudové služby a název virtuálního počítače a potom na příkazovém řádku Azure PowerShell na úrovni správce spusťte následující příkazy. Nahradí vše v uvozovkách, včetně < a > znaků.

> [!TIP]
> Pokud neznáte název cloudové služby a virtuálního počítače, spusťte příkaz **Get-AzureVM** , který zobrazí seznam názvů všech virtuálních počítačů v aktuálním předplatném.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Pokud se v příkazu **Write-Host** zobrazí **hodnota true**, je nainstalován agent virtuálního počítače. Pokud se zobrazí **hodnota NEPRAVDA**, Projděte si pokyny a odkaz na stažení v příspěvku na blogu Azure [Agent a rozšíření virtuálního počítače – část 2][Agent].

Pokud je nainstalován agent virtuálního počítače, spusťte tyto příkazy a nainstalujte agenta Symantec Endpoint Protection.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Ověření, zda bylo nainstalováno rozšíření zabezpečení Symantec a je aktuální:

1. Přihlaste se k virtuálnímu počítači. Pokyny najdete v tématu [jak se přihlásit k virtuálnímu počítači s Windows serverem][Logon].
2. Pro Windows Server 2008 R2 klikněte na **Start > Symantec Endpoint Protection**. Pro Windows Server 2012 nebo Windows Server 2012 R2 na obrazovce Start zadejte **Symantec**a pak klikněte na **Symantec Endpoint Protection**.
3. Na kartě **stav** okna **stav-Symantec Endpoint Protection** použijte v případě potřeby aktualizace nebo restartovat.

## <a name="additional-resources"></a>Další zdroje
[Jak se přihlásit k virtuálnímu počítači s Windows serverem][Logon]

[Rozšíření a funkce virtuálních počítačů Azure][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azure/

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: features-windows.md
