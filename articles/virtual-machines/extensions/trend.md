---
title: Instalace Trend Micro hlubokého zabezpečení na virtuálním počítači
description: Tento článek popisuje, jak nainstalovat a nakonfigurovat Trend Micro Security na virtuálním počítači vytvořeném pomocí modelu nasazení Classic v Azure.
author: axayjo
tags: azure-service-management
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/20/2018
ms.author: akjosh
ms.openlocfilehash: bed1d567aa48a11c01ae952e6a4a2028e260e6e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288522"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Postup instalace a konfigurace Trend Micro hlubokého zabezpečení jako služby na virtuálním počítači s Windows

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
V tomto článku se dozvíte, jak nainstalovat a nakonfigurovat trend Microal úrovně zabezpečení jako službu na novém nebo existujícím virtuálním počítači s Windows serverem. Špičkové zabezpečení jako služba zahrnuje ochranu proti malwaru, bránu firewall, systém prevence vniknutí a monitorování integrity.

Klient se nainstaluje jako rozšíření zabezpečení prostřednictvím agenta virtuálního počítače. Na novém virtuálním počítači nainstalujete hloubkový Agent zabezpečení, protože Azure Portal automaticky vytvoří agent virtuálního počítače.

Existující virtuální počítač vytvořený pomocí Azure Portal, Azure CLI nebo PowerShellu možná nemá agenta virtuálního počítače. Pro existující virtuální počítač, který nemá agenta virtuálního počítače, ho budete muset stáhnout a nainstalovat jako první. Tento článek se zabývá oběma situacemi.

Pokud máte aktuální předplatné od společnosti Trend Micro pro místní řešení, můžete ho použít k ochraně virtuálních počítačů Azure. Pokud ještě nejste zákazník, můžete si zaregistrovat zkušební verzi předplatného. Další informace o tomto řešení najdete v příspěvku na blogu Trend Micro [Microsoft Azure rozšíření agenta virtuálního počítače pro zajištění hloubkového zabezpečení](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Instalace hloubkového agenta zabezpečení do nového virtuálního počítače

[Azure Portal](https://portal.azure.com) umožňuje nainstalovat rozšíření Trend Micro Security při použití image z **Marketplace** k vytvoření virtuálního počítače. Pokud vytváříte jeden virtuální počítač, můžete pomocí portálu snadno přidat ochranu z Trend Micro.

Když použijete záznam z **Marketplace** , otevře se průvodce, který vám pomůže nastavit virtuální počítač. K instalaci rozšíření Trend Micro Security použijte okno **Nastavení** , třetí panel průvodce.  Obecné pokyny najdete v tématu [Vytvoření virtuálního počítače s Windows v Azure Portal](../windows/quick-create-portal.md).

Po zobrazení okna **Nastavení** v průvodci proveďte následující kroky:

1. Klikněte na **rozšíření**a potom v dalším podokně klikněte na **Přidat rozšíření** .

   ![Začněte přidávat rozšíření.][1]

2. V podokně **nový prostředek** vyberte **hloubkový Agent zabezpečení** . V podokně hloubkový Agent zabezpečení klikněte na **vytvořit**.

   ![Identifikace nadrozsáhlého agenta zabezpečení][2]

3. Zadejte **identifikátor tenanta** a **heslo aktivace tenanta** pro toto rozšíření. Volitelně můžete zadat **identifikátor zásady zabezpečení**. Potom kliknutím na tlačítko **OK** přidejte klienta.

   ![Zadat podrobnosti rozšíření][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instalace hloubkového agenta zabezpečení na existujícím virtuálním počítači
K instalaci agenta na existující virtuální počítač budete potřebovat následující položky:

* Azure PowerShell modul, verze 0.8.2 nebo novější, nainstalované na místním počítači. Verzi Azure PowerShell, kterou jste nainstalovali, můžete zjistit pomocí příkazu **Get-Module Azure | Format-Table Version** . Pokyny a odkaz na nejnovější verzi najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/). Přihlaste se k předplatnému Azure pomocí `Add-AzureAccount` .
* Agent virtuálního počítače je nainstalovaný na cílovém virtuálním počítači.

Nejprve ověřte, zda je agent virtuálního počítače již nainstalován. Zadejte název cloudové služby a název virtuálního počítače a potom na příkazovém řádku Azure PowerShell na úrovni správce spusťte následující příkazy. Nahradí vše v uvozovkách, včetně < a > znaků.

```azurepowershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Pokud neznáte cloudovou službu a název virtuálního počítače, spusťte příkaz **Get-AzureVM** , který zobrazí informace o všech virtuálních počítačích v aktuálním předplatném.

Pokud se příkaz **Write-Host** vrátí na **hodnotu true**, nainstaluje se agent virtuálního počítače. Pokud vrátí **hodnotu false**, přečtěte si pokyny a odkaz na stažení v příspěvku na blogu Azure [Agent a rozšíření virtuálního počítače – část 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Pokud je nainstalován agent virtuálního počítače, spusťte tyto příkazy.

```azurepowershell
$Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM
```

## <a name="next-steps"></a>Další kroky
Spuštění agenta při jeho instalaci trvá několik minut. Potom je potřeba na virtuálním počítači aktivovat hloubkové zabezpečení, aby ho bylo možné spravovat pomocí hlubokého správce zabezpečení. Další pokyny najdete v následujících článcích:

* Článek o tomto řešení v trendech, [rychlých cloudových zabezpečení pro Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* [Ukázkový skript prostředí Windows PowerShell](https://go.microsoft.com/fwlink/?LinkId=404100) pro konfiguraci virtuálního počítače
* [Pokyny](https://go.microsoft.com/fwlink/?LinkId=404099) k ukázce

## <a name="additional-resources"></a>Další zdroje
[Jak se přihlásit k virtuálnímu počítači s Windows serverem]

[Rozšíření a funkce virtuálních počítačů Azure]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Jak se přihlásit k virtuálnímu počítači s Windows serverem]:../windows/classic/connect-logon.md
[Rozšíření a funkce virtuálních počítačů Azure]: features-windows.md
