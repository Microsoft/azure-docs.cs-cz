---
title: Instalace trendu Micro Deep Security na virtuální počítač
description: Tento článek popisuje, jak nainstalovat a nakonfigurovat zabezpečení Trend Micro na virtuální ms vytvořené pomocí modelu klasického nasazení v Azure.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 04/20/2018
ms.author: akjosh
ms.openlocfilehash: cffd2eab3a616b4d16d847d0f2e1a26655f40459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919919"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Jak nainstalovat a nakonfigurovat Trend Micro Deep Security jako služba na virtuálním počítači se systémem Windows

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
V tomto článku se zobrazí postup instalace a konfigurace technologie Trend Micro Deep Security as a Service na novém nebo existujícím virtuálním počítači se systémem Windows Server. Deep Security as a Service zahrnuje ochranu proti malwaru, bránu firewall, systém prevence vniknutí a monitorování integrity.

Klient je nainstalován jako rozšíření zabezpečení prostřednictvím agenta virtuálního zařízení. Na novém virtuálním počítači nainstalujete agenta deep security, protože agent virtuálního počítače se automaticky vytvoří portálem Azure.

Existující virtuální počítač vytvořený pomocí portálu Azure, Azure CLI nebo PowerShell nemusí mít agenta virtuálního počítače. Pro existující virtuální počítač, který nemá agenta virtuálního počítače, je třeba jej nejprve stáhnout a nainstalovat. Tento článek se zabývá oběma situacemi.

Pokud máte aktuální předplatné od Společnosti Trend Micro pro místní řešení, můžete ho použít k ochraně virtuálních počítačů Azure. Pokud ještě nejste zákazníkem, můžete si zaregistrovat zkušební předplatné. Další informace o tomto řešení najdete v blogu Trend Micro v příspěvku [Microsoft Azure VM Agent Extension For Deep Security](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Instalace agenta deep security na nový virtuální počítač

[Portál Azure](https://portal.azure.com) umožňuje nainstalovat rozšíření zabezpečení Trend Micro, když k vytvoření virtuálního počítače použijete bitovou kopii z **webu Marketplace.** Pokud vytváříte jeden virtuální počítač, pomocí portálu je snadný způsob, jak přidat ochranu z Trend Micro.

Pomocí položky z **webu Marketplace** se otevře průvodce, který vám pomůže nastavit virtuální počítač. K instalaci rozšíření zabezpečení Trend Micro se používá okno **Nastavení,** třetí panel průvodce.  Obecné pokyny najdete [v tématu Vytvoření virtuálního počítače se systémem Windows na webu Azure Portal](../windows/classic/tutorial.md).

Až se dostanete k okně **Nastavení** průvodce, proveďte následující kroky:

1. V dalším podokně klikněte na **Rozšíření**a potom klikněte na **Přidat rozšíření.**

   ![Začít přidávat rozšíření][1]

2. V podokně **Nový prostředek** vyberte Deep **Security Agent.** V podokně Agent přímého zabezpečení klepněte na tlačítko **Vytvořit**.

   ![Identifikovat agenta hlubokého zabezpečení][2]

3. Zadejte **identifikátor klienta** a heslo pro **aktivaci klienta** pro rozšíření. Volitelně můžete zadat **identifikátor zásad zabezpečení**. Potom klepnutím na **tlačítko OK** přidejte klienta.

   ![Poskytnout podrobnosti o rozšíření][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instalace agenta deep security na existující virtuální počítač
Chcete-li nainstalovat agenta na existující virtuální počítač, potřebujete následující položky:

* Modul Azure PowerShell verze 0.8.2 nebo novější, nainstalovaný v místním počítači. Verzi Prostředí Azure PowerShell, kterou jste nainstalovali, můžete zkontrolovat pomocí příkazu **Verze formátové tabulky Get-Module |** Pokyny a odkaz na nejnovější verzi najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview). Přihlaste se k `Add-AzureAccount`předplatnému Azure pomocí .
* Agent virtuálního počítače nainstalovaný v cílovém virtuálním počítači.

Nejprve ověřte, že agent virtuálního zařízení je už nainstalovaný. Vyplňte název cloudové služby a název virtuálního počítače a pak spusťte následující příkazy na příkazovém řádku AzureShellu na úrovni správce. Nahraďte vše v uvozovkách, včetně < a > znaků.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Pokud neznáte cloudovou službu a název virtuálního počítače, spusťte **Get-AzureVM** a zobrazte tyto informace pro všechny virtuální počítače v aktuálním předplatném.

Pokud příkaz **write-host** vrátí **true**, je nainstalován agent virtuálního zařízení. Pokud vrátí **False**, podívejte se na pokyny a odkaz na stažení v blogu Azure [VM Agent a rozšíření - část 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Pokud je nainstalován agent virtuálního zařízení, spusťte tyto příkazy.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Další kroky
Trvá několik minut, než se agent spustí po instalaci. Poté je třeba aktivovat deep security na virtuálním počítači, aby jej mohl spravovat Správce hlubokého zabezpečení. Další pokyny naleznete v následujících článcích:

* Článek společnosti Trend o tomto [řešení, Okamžité cloudové zabezpečení pro Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* [Ukázkový skript prostředí Windows PowerShell](https://go.microsoft.com/fwlink/?LinkId=404100) pro konfiguraci virtuálního počítače
* [Pokyny](https://go.microsoft.com/fwlink/?LinkId=404099) pro vzorek

## <a name="additional-resources"></a>Další zdroje
[Jak se přihlásit k virtuálnímu počítači se systémem Windows Server]

[Rozšíření a funkce virtuálních počítačů Azure]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Jak se přihlásit k virtuálnímu počítači se systémem Windows Server]:../windows/classic/connect-logon.md
[Rozšíření a funkce virtuálních počítačů Azure]: https://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
