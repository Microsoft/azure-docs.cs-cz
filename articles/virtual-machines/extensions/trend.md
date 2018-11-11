---
title: Nainstalujte Trend Micro Deep Security na virtuálním počítači | Dokumentace Microsoftu
description: Tento článek popisuje, jak nainstalovat a nakonfigurovat Trend Micro security na virtuálním počítači vytvořeném pomocí modelu nasazení Classic do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: roiyz
ms.openlocfilehash: ddb999c252f0802ef57942de694340ae29f2d8ba
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240354"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Jak nainstalovat a nakonfigurovat Trend Micro Deep Security na virtuálním počítači s Windows jako službu
[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
V tomto článku se dozvíte, jak nainstalovat a nakonfigurovat Trend Micro Deep Security jako službu na nový nebo existující virtuální počítač (VM) s Windows serverem. Deep Security jako službu zahrnuje ochrana proti malwaru, brána firewall, napadení systému ochrany před únikem informací a monitorování integrity.

Klientovi je nainstalován jako rozšíření zabezpečení prostřednictvím agenta virtuálního počítače. Na virtuálním počítači instalace Deep Security Agent, protože Agent virtuálního počítače je automaticky vytvořené na webu Azure portal.

Agent virtuálního počítače nemusí mít stávající virtuální počítače vytvořené pomocí webu Azure portal, Azure CLI nebo Powershellu. Pro existující virtuální počítač, který nemá agenta virtuálního počítače budete muset stáhnout a nainstalovat první. Tento článek se týká obou situacích.

Pokud máte v Trend Micro aktuální předplatné pro místní řešení, můžete k ochraně virtuálních počítačů Azure. Pokud vám zákazník ještě nejsou, můžete zaregistrovat zkušební předplatné. Další informace o tomto řešení najdete v příspěvku na blogu Trend Micro [Microsoft Azure VM Agent rozšíření pro Deep Security](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Nainstalovat Deep Security Agent na nový virtuální počítač

[Webu Azure portal](http://portal.azure.com) vám umožní nainstalovat rozšíření zabezpečení Trend Micro, pokud použijete image z **Marketplace** k vytvoření virtuálního počítače. Pokud vytváříte jeden virtuální počítač, na portálu je snadný způsob, jak přidat ochranu z Trend Micro.

Pomocí položky z **Marketplace** otevře se průvodce, který vám pomůže nastavit virtuální počítač. Můžete použít **nastavení** okna, panelu třetí průvodce, chcete-li nainstalovat rozšíření zabezpečení Trend Micro.  Obecné pokyny najdete v tématu [vytvořit virtuální počítač s Windows na webu Azure Portal](../windows/classic/tutorial.md).

Když se zobrazí **nastavení** okno průvodce proveďte následující kroky:

1. Klikněte na tlačítko **rozšíření**, pak klikněte na tlačítko **přidat rozšíření** v dalším podokně.

   ![Začátek přidávání rozšíření][1]

2. Vyberte **Deep Security Agent** v **nový prostředek** podokně. V podokně Deep Security Agent, klikněte na tlačítko **vytvořit**.

   ![Identifikujte Deep Security Agent][2]

3. Zadejte **identifikátor Tenanta** a **heslo aktivace Tenanta** pro rozšíření. Volitelně můžete zadat **identifikátor zásad zabezpečení**. Potom klikněte na **OK** pro přidání klienta.

   ![Zadejte podrobnosti o rozšíření][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Nainstalovat Deep Security Agent na existující virtuální počítač
Instalace agenta na existující virtuální počítač, potřebujete následující položky:

* Modul Azure PowerShell, verze 0.8.2 nebo novější, nainstalované na místním počítači. Můžete zkontrolovat verzi Azure Powershellu, který jste nainstalovali pomocí **Get-Module azure | format-table verze** příkazu. Pokyny a odkaz na nejnovější verzi najdete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview). Přihlaste se k předplatnému Azure pomocí `Add-AzureAccount`.
* Agent virtuálního počítače nainstalovaný na cílovém virtuálním počítači.

Nejprve ověřte, že Agent virtuálního počítače je již nainstalována. Zadejte název cloudové služby a název virtuálního počítače a pak spusťte následující příkazy příkazového řádku Azure PowerShell na úrovni správce. Nahradit vše, co v uvozovkách, včetně < a > znaků.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Pokud si nejste jisti, cloudové služby a název virtuálního počítače, spusťte **Get-AzureVM** k zobrazení těchto informací pro všechny virtuální počítače v aktuálním předplatném.

Pokud **write-host** příkazem **True**, je nainstalovaný Agent virtuálního počítače. Vrátí-li **False**, podívejte se pokyny a odkaz ke stažení v příspěvku na blogu Azure [agenta virtuálního počítače a rozšíření – část 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Pokud je nainstalovaný Agent virtuálního počítače, spusťte tyto příkazy.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Další postup
Trvá několik minut, než spustíte po instalaci agenta. Potom budete muset aktivovat Deep Security na virtuálním počítači, aby se dala spravovat pomocí Deep Security Manager. Naleznete v následujících článcích pro další pokyny:

* Trend na článek o tomto řešení [Instant-On cloudové zabezpečení pro Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* A [ukázkový skript prostředí Windows PowerShell](https://go.microsoft.com/fwlink/?LinkId=404100) ke konfiguraci virtuálního počítače
* [Pokyny](https://go.microsoft.com/fwlink/?LinkId=404099) pro ukázku

## <a name="additional-resources"></a>Další zdroje informací:
[Jak se přihlásit k virtuálnímu počítači s Windows serverem]

[Funkce a rozšíření virtuálních počítačů Azure]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Jak se přihlásit k virtuálnímu počítači s Windows serverem]:../windows/classic/connect-logon.md
[Funkce a rozšíření virtuálních počítačů Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
