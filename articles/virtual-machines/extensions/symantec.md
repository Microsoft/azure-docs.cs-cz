---
title: Instalace služby Symantec Endpoint Protection na virtuální montovana Symantec v Azure
description: Zjistěte, jak nainstalovat a nakonfigurovat rozšíření zabezpečení Symantec Endpoint Protection na nový nebo existující virtuální počítač Azure vytvořený pomocí klasického modelu nasazení.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 03/31/2017
ms.author: akjosh
ms.openlocfilehash: 63f9441d4df9551405c2ab2bf8c0c67d7de5753c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919902"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Jak nainstalovat a nakonfigurovat službu Symantec Endpoint Protection na virtuálním počítači se systémem Windows
[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití modelu klasické nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

V tomto článku se zobrazí postup instalace a konfigurace klienta Symantec Endpoint Protection na existujícím virtuálním počítači (VM) se systémem Windows Server. Tento úplný klient zahrnuje služby, jako je ochrana proti virům a spywaru, brána firewall a ochrana proti vniknutí. Klient je nainstalován jako rozšíření zabezpečení pomocí agenta virtuálního zařízení.

Pokud máte stávající předplatné od společnosti Symantec pro místní řešení, můžete ho použít k ochraně virtuálních počítačů Azure. Pokud ještě nejste zákazníkem, můžete si zaregistrovat zkušební předplatné. Další informace o tomto řešení najdete [v tématu Symantec Endpoint Protection na platformě Azure od Microsoftu][Symantec]. Tato stránka obsahuje také odkazy na informace o licencích a pokyny pro instalaci klienta, pokud jste již zákazníkem společnosti Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Instalace služby Symantec Endpoint Protection na existující virtuální počítač
Než začnete, potřebujete následující:

* Modul Azure PowerShell verze 0.8.2 nebo novější v pracovním počítači. Můžete zkontrolovat verzi Azure PowerShellu, kterou jste nainstalovali pomocí příkazu **Verze formátové tabulky Get-Module |** Pokyny a odkaz na nejnovější verzi najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell][PS]. Přihlaste se k `Add-AzureAccount`předplatnému Azure pomocí .
* Agent virtuálního počítače spuštěný ve virtuálním počítači Azure.

Nejprve ověřte, že agent virtuálního počítače je už nainstalovaný ve virtuálním počítači. Vyplňte název cloudové služby a název virtuálního počítače a pak spusťte následující příkazy na příkazovém řádku AzureShellu na úrovni správce. Nahraďte vše v uvozovkách, včetně < a > znaků.

> [!TIP]
> Pokud neznáte názvy cloudové služby a virtuálních počítačů, spusťte **Get-AzureVM** a uveďte názvy všech virtuálních počítačů ve vašem aktuálním předplatném.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Pokud příkaz **write-host** zobrazí **True**, je nainstalován agent virtuálního zařízení. Pokud se zobrazí **False**, podívejte se na pokyny a odkaz na stažení v blogu Azure [VM Agent a rozšíření - část 2][Agent].

Pokud je nainstalován agent virtuálního zařízení, spusťte tyto příkazy a nainstalujte agenta Symantec Endpoint Protection.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Chcete-li ověřit, zda bylo nainstalováno a zda je rozšíření zabezpečení společnosti Symantec aktuální:

1. Přihlaste se k virtuálnímu počítači. Pokyny naleznete v [tématu Jak se přihlásit k virtuálnímu počítači se systémem Windows Server][Logon].
2. V systému Windows Server 2008 R2 klepněte na **tlačítko Spustit > program Symantec Endpoint Protection**. V systému Windows Server 2012 nebo Windows Server 2012 R2 zadejte z úvodní obrazovky **položku Symantec**a klepněte na **položku Symantec Endpoint Protection**.
3. Na kartě **Stav** v okně **Ochrana koncového bodu aplikace Status-Symantec** použijte aktualizace nebo v případě potřeby restartujte.

## <a name="additional-resources"></a>Další zdroje
[Jak se přihlásit k virtuálnímu počítači se systémem Windows Server][Logon]

[Rozšíření a funkce virtuálních počítačů Azure][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
