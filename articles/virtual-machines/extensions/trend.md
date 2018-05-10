---
title: Nainstalujte Trend malých hloubkové zabezpečení na virtuálním počítači | Microsoft Docs
description: Tento článek popisuje postup instalace a konfigurace Trend Micro zabezpečení na virtuálním počítači vytvořené pomocí modelu nasazení Classic v Azure.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
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
ms.author: danis
ms.openlocfilehash: 6098d310bcc6fe5df2378688b78277fc7e4b16bc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Jak nainstalovat a nakonfigurovat Trend Micro Deep Security na virtuálním počítači s Windows jako službu
[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
Tento článek ukazuje, jak nainstalovat a nakonfigurovat Trend Micro hluboké Security jako služba na nový nebo existující virtuální počítač (VM) s Windows serverem. Hloubkové zabezpečení jako služba zahrnuje ochrana proti malwaru, brána firewall, narušení prevence systém a monitorování integrity.

Klient je nainstalován jako rozšíření zabezpečení prostřednictvím agenta virtuálního počítače. Na nový virtuální počítač nainstalujte hloubkové Agent zabezpečení jako Agent virtuálního počítače je vytvářena automaticky nástrojem portálu Azure.

Existující virtuální počítač vytvořený pomocí portálu Azure, Azure CLI nebo prostředí PowerShell nemusí mít agenta virtuálního počítače. Pro stávajícího virtuálního počítače, který nemá agenta virtuálního počítače potřebujete stáhněte a nainstalujte ji jako první. Tento článek se týká obou situacích.

Pokud máte z Trend Micro aktuální předplatné pro místní řešení, můžete k ochraně virtuální počítače Azure. Pokud vám ještě nejsou zákazníka, můžete zaregistrovat zkušební předplatné. Další informace o tomto řešení najdete v příspěvku blogu Trend Micro [Microsoft Azure virtuálního počítače agenta rozšíření pro přímý zabezpečení](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Nainstalujte agenta hloubkové zabezpečení na nový virtuální počítač

[Portál Azure](http://portal.azure.com) umožňuje nainstalovat rozšíření zabezpečení Trend Micro při použití bitovou kopii **Marketplace** k vytvoření virtuálního počítače. Pokud vytváříte jeden virtuální počítač, pomocí portálu je snadný způsob, jak přidat ochranu z Trend Micro.

Pomocí položky z **Marketplace** otevře průvodce, který vám pomůže nastavit virtuální počítač. Můžete použít **nastavení** okna, panel třetí průvodce, chcete-li nainstalovat rozšíření Trend Micro zabezpečení.  Obecné pokyny najdete v tématu [vytvoření virtuálního počítače se systémem Windows na portálu Azure](../windows/classic/tutorial.md).

Jakmile přejdete **nastavení** okno průvodce proveďte následující kroky:

1. Klikněte na tlačítko **rozšíření**, pak klikněte na tlačítko **přidat rozšíření** v podokně Další.

   ![Začít přidávat rozšíření][1]

2. Vyberte **hloubkové Agent zabezpečení** v **nový prostředek** podokně. V podokně hloubkové Agent zabezpečení, klikněte na tlačítko **vytvořit**.

   ![Identifikovat Agent hloubkové zabezpečení][2]

3. Zadejte **identifikátor klienta** a **heslo aktivace klienta** pro rozšíření. Volitelně můžete zadat **identifikátor zásady zabezpečení**. Potom klikněte na **OK** přidat klienta.

   ![Zadejte podrobnosti o rozšíření][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Hloubkové zabezpečení agenta nainstalovat na existující virtuální počítač
Chcete-li nainstalovat agenta na existující virtuální počítač, je třeba následující položky:

* Modul Azure PowerShell, verze 0.8.2 nebo novější, nainstalovaná v místním počítači. Můžete zkontrolovat verzi prostředí Azure PowerShell, který jste nainstalovali pomocí **Get-Module azure | verze formátu tabulky** příkaz. Pokyny a odkaz na nejnovější verzi naleznete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview). Přihlaste se k předplatnému Azure pomocí `Add-AzureAccount`.
* Agent virtuálního počítače nainstalovaný na cílový virtuální počítač.

Nejprve ověří, že je již nainstalován Agent virtuálního počítače. Zadejte název cloudové služby a název virtuálního počítače a potom spusťte následující příkazy příkazového řádku Azure PowerShell úrovni správce. Nahraďte všechna data v uvozovkách, včetně < a > znaků.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Pokud si nejste jisti, Cloudová služba a název virtuálního počítače, spusťte **Get-AzureVM** zobrazit tyto informace pro všechny virtuální počítače v aktuálním předplatném.

Pokud **zápisu hostitele** příkaz vrátí **True**, je nainstalovaný Agent virtuálního počítače. Vrátí-li **False**, najdete pokyny a odkaz na stažení v Azure příspěvku na blogu [agenta virtuálního počítače a rozšíření – část 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Pokud je nainstalován Agent virtuálního počítače, spusťte tyto příkazy.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Další postup
Jak dlouho trvá několik minut začít spouštět po instalaci agenta. Potom musíte aktivovat hloubkové zabezpečení na virtuálním počítači, můžete spravovat pomocí hloubkové zabezpečení správce. Najdete v následujících článcích další pokyny:

* Trend na článek o tomto řešení [Instant-On cloudu zabezpečení pro Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
* A [ukázkový skript prostředí Windows PowerShell](http://go.microsoft.com/fwlink/?LinkId=404100) ke konfiguraci virtuálního počítače
* [Pokyny](http://go.microsoft.com/fwlink/?LinkId=404099) pro ukázku

## <a name="additional-resources"></a>Další zdroje informací:
[Jak se přihlásit do virtuálního počítače se systémem Windows Server]

[Rozšíření virtuálního počítače Azure a funkce]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Jak se přihlásit do virtuálního počítače se systémem Windows Server]:../windows/classic/connect-logon.md
[Rozšíření virtuálního počítače Azure a funkce]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
