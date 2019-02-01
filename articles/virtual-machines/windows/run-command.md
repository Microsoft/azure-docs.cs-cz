---
title: Spouštění skriptů prostředí PowerShell v virtuálního počítače s Windows v Azure
description: Toto téma popisuje, jak spouštět skripty prostředí PowerShell v rámci virtuálního počítače s Windows Azure pomocí příkazu Spustit
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 33f96c67e7179104d1895cf62f834d3b592bee04
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487621"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>Spouštění skriptů prostředí PowerShell ve virtuálním počítači Windows pomocí příkazu Spustit

Spusťte příkaz používá agent virtuálního počítače ke spouštění skriptů prostředí PowerShell v rámci virtuálního počítače Windows Azure. Tyto skripty lze použít obecné počítače nebo Správa aplikací a umožňuje rychle diagnostikovat a opravit problémy s přístup a síť virtuálního počítače a získat zpět na dobrý stav virtuálního počítače.

## <a name="benefits"></a>Výhody

Existuje několik možností, které slouží k přístupu k virtuálním počítačům. Spusťte příkaz můžete spustit skripty na virtuálních počítačích vzdáleně pomocí agenta virtuálního počítače. Spusťte příkaz se dá použít prostřednictvím webu Azure portal, [rozhraní REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), nebo [Powershellu](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) pro virtuální počítače s Windows.

Tato možnost je užitečná ve všech scénářích, kde chcete spustit skript v rámci virtuálních počítačů a je jedním z jediný způsob, jak odstraňovat potíže a opravte virtuální počítač, který nemá protokol RDP nebo otevřít SSH port z důvodu nesprávné sítě nebo správce konfigurace.

## <a name="restrictions"></a>Omezení

Při použití příkazu Spustit platí následující omezení:

* Výstup se omezí na poslední 4096 bajtů.
* Minimální doba pro spuštění skriptu je přibližně 20 sekund.
* Skripty spouštěné jako systém na Windows
* Může spustit jeden skript v čase
* Skripty, které výzvu k zadání informace (interaktivní režim) nejsou podporovány.
* Nelze zrušit spouštění skriptu
* Maximální doba, kterou můžete spustit skript je 90 minut, po kterém vyprší časový limit
* Odchozí připojení z virtuálního počítače je potřeba vrátit výsledky ze skriptu.

> [!NOTE]
> Aby fungovala správně, spustit příkaz vyžaduje připojení (port 443) k veřejným IP adresám Azure. Pokud přípona nemá přístup do těchto koncových bodů, může skripty úspěšně spuštěno, ale vracet výsledky. Pokud můžete, blokují přenosy na virtuální počítač, můžete použít [značky služeb](../../virtual-network/security-overview.md#service-tags) pro povolení provozu na veřejné IP adresy Azure s použitím `AzureCloud` značky.

## <a name="run-a-command"></a>Spuštění příkazu

Přejděte k virtuálnímu počítači s [Azure](https://portal.azure.com) a vyberte **spusťte příkaz** pod **operace**. Zobrazí se seznam dostupných příkazů ke spuštění ve virtuálním počítači.

![Spuštění seznamu příkazů](./media/run-command/run-command-list.png)

Zvolte příkaz spustit. Některé příkazy mohou mít nepovinné nebo povinné vstupní parametry. Pro tyto příkazy jsou uvedeny parametry jako textových polí můžete zadávat vstupní hodnoty. U každého příkazu můžete zobrazit skript, který je spuštěn tak, že rozbalíte **zobrazit skript**. **RunPowerShellScript** se liší od dalších příkazů, protože to umožňuje poskytovat vlastní skript.

> [!NOTE]
> Integrované příkazy se nedají upravovat.

Po příkazu je vybrán, klikněte na tlačítko **spustit** pro spuštění skriptu. Skript spustí a po dokončení vrátí výstup a chyby v okně výstup. Následující snímek obrazovky ukazuje příklad výstupu spuštění **RDPSettings** příkazu.

![Spusťte skript výstup příkazu](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>Příkazy

Tato tabulka zobrazuje seznam příkazů, které jsou k dispozici pro virtuální počítače s Windows. **RunPowerShellScript** příkaz lze použít ke spuštění libovolného vlastního skriptu, který chcete.

|**Název**|**Popis**|
|---|---|
|**RunPowerShellScript**|Provede skript prostředí PowerShell|
|**EnableRemotePS**|Nakonfiguruje počítač pro povolení vzdáleného prostředí PowerShell.|
|**EnableAdminAccount**|Zkontroluje, jestli je zakázaný účet místního správce a pokud ano povolí ho.|
|**IPConfig**| Zobrazí podrobné informace o IP adresu, masku podsítě a výchozí bránu pro každý adaptér vázaný na TCP/IP.|
|**RDPSettings**|Kontroluje, nastavení registru a nastavení zásad domény. Akce zásad navrhuje, počítač je součástí domény nebo upraví nastavení na výchozí hodnoty.|
|**ResetRDPCert**|Odstraní certifikát SSL, které jsou vázané na naslouchací proces RDP a obnoví zabezpečení listerner RDP na výchozí hodnoty. Tento skript použijte, pokud nastanou nějaké problémy s certifikátem.|
|**SetRDPPort**|Nastaví výchozí nebo uživatel zadat číslo portu pro připojení ke vzdálené ploše. Povolí pravidlo brány firewall pro příchozí přístup k portu.|

## <a name="powershell"></a>PowerShell

Následuje příklad použití [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) rutinu spustit skript prostředí PowerShell na Virtuálním počítači Azure. Rutina očekává, že skript odkazuje `ScriptPath` proměnnou deklarovanou místní kde rutina se spustila.

```azurepowershell-interactive
Invoke-AzureRmVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Omezení přístupu ke spuštění příkazu

Výpis spouštění příkazů nebo s podrobnostmi o příkaz vyžaduje `Microsoft.Compute/locations/runCommands/read` oprávnění, která předdefinované [čtečky](../../role-based-access-control/built-in-roles.md#reader) role a vyšší.

Spuštění příkazu vyžaduje `Microsoft.Compute/virtualMachines/runCommand/action` oprávnění, která [Přispěvatel](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) role a vyšší.

Můžete použít jednu z [integrované](../../role-based-access-control/built-in-roles.md) role nebo vytvořte [vlastní](../../role-based-access-control/custom-roles.md) roli spustit příkaz.

## <a name="next-steps"></a>Další postup

Zobrazit, [spouštět skripty ve virtuálním počítači Windows](run-scripts-in-vm.md) Další informace o dalších způsobech vzdáleně spouštět skripty a příkazy ve virtuálním počítači.
