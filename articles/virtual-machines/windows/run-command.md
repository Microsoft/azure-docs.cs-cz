---
title: Spouštění skriptů prostředí PowerShell ve virtuálním počítači Windows v Azure
description: Toto téma popisuje, jak spouštět skripty prostředí PowerShell v rámci virtuálního počítače Azure Windows pomocí spustit příkaz
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: ddbac24020110e32792286a1ac64070316cfb081
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332710"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>Spusťte skripty prostředí PowerShell v systému Windows virtuálního počítače se spustit příkaz

Spusťte příkaz používá agent virtuálního počítače ke spouštění skriptů prostředí PowerShell v rámci virtuálního počítače Windows Azure. Tyto skripty lze použít obecné počítače nebo Správa aplikací a umožňuje rychle diagnostikovat a opravit problémy přístup a síť virtuálních počítačů a získat zpět na dobrý stav virtuálního počítače.

## <a name="benefits"></a>Výhody

Nejsou k dispozici více možností, které slouží k přístupu k virtuálním počítačům. Spusťte příkaz může spouštět skripty na virtuální počítače vzdáleně pomocí agenta virtuálního počítače. Spusťte příkaz lze použít prostřednictvím portálu Azure [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [rozhraní příkazového řádku Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), nebo [prostředí PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand).

Tato možnost je užitečná ve všech scénářích, kdy chcete spustit skript v rámci virtuálních počítačů a jeden jediný způsob, jak vyřešit a opravte virtuální počítač, který nemá protokol RDP nebo SSH port otevřít z důvodu nesprávné sítě nebo správce konfigurace.

## <a name="restrictions"></a>Omezení

Při použití spustit příkaz platí následující omezení:

* Výstup je omezeno na posledních 4096 bajtů
* Minimální hodnota času spuštění skriptu je přibližně 20 sekund
* Skripty spustit jako systému v systému Windows
* Jeden skript současně může spustit.
* Nelze zrušit spouštění skriptu
* Maximální doba spuštění skriptu je 90 minut, po který dojde k vypršení časového limitu

**PermissionsConfig-OrchestratorUsersGroup***GroupName***-OrchestratorUser***UserName***\-remote** 

## <a name="run-a-command"></a>Spuštění příkazu

Přejděte do virtuálního počítače v [Azure](https://portal.azure.com) a vyberte **spusťte příkaz** pod **operace**. Zobrazí seznam dostupných příkazů pro spuštění ve virtuálním počítači.

![Spusťte příkaz seznamu](./media/run-command/run-command-list.png)

Vyberte příkaz ke spuštění. Některé příkazy může mít volitelné nebo požadované vstupní parametry. Parametry jako textové pole k zabezpečení vstupní hodnoty jsou k dispozici pro tyto příkazy. U každého příkazu můžete zobrazit skript, který je spuštěn rozšířením **zobrazit skript**. **RunPowerShellScript** se liší od jinými příkazy, jako umožňuje zadat vlastní skript.

> [!NOTE]
> Integrované příkazy se nedají upravovat.

Jakmile je zvoleno příkaz, klikněte na možnost **spustit** pro spuštění skriptu. Skript spustí a po dokončení vrátí výstup a všechny chyby v okně výstupu. Následující snímek obrazovky ukazuje příklad výstup spuštění **RDPSettings** příkaz.

![Spustit výstup příkazu skriptu](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>Příkazy

Tato tabulka uvádí seznam příkazů, které jsou k dispozici pro virtuální počítače Windows. **RunPowerShellScript** příkaz lze použít ke spuštění všech vlastních skriptů, které chcete.

|**Název**|**Popis**|
|---|---|
|**RunPowerShellScript**|Spustí skript prostředí PowerShell|
|**EnableRemotePS**|Nakonfiguruje počítač pro povolení vzdáleného prostředí PowerShell.|
|**EnableAdminAccount**|Kontroluje, pokud je zakázán místní účet správce a pokud ano umožňuje.|
|**IPConfig**| Zobrazí podrobné informace o IP adres, masku podsítě a výchozí bránu pro každý adaptér vázaný na protokol TCP/IP.|
|**RDPSettings**|Ověří, nastavení registru a nastavení zásad domény. Akce zásad navrhuje, pokud je počítač součástí domény nebo upraví nastavení výchozí hodnoty.|
|**ResetAccountPassword**| Resetuje heslo účtu předdefinovaného účtu Administrator.|
|**ResetRDPCert**|Odstraní certifikát SSL, který je vázaný na naslouchací proces protokolu RDP a obnoví listerner zabezpečení protokolu RDP na výchozí. Tento skript použijte, pokud se zobrazí všechny problémy s certifikátem.|
|**SetRDPPort**|Nastaví výchozí nebo uživatel zadat číslo portu pro připojení ke vzdálené ploše. Umožňuje pravidlo brány firewall pro příchozí přístup k portu.|

## <a name="powershell"></a>PowerShell

Následuje příklad použití [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) můžete spustit skript prostředí PowerShell ve virtuálním počítači Azure.

```azurepowershell-interactive
Invoke-AzureRmVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Omezení přístupu ke spuštění příkazu

Zobrazení nebo výpis příkazy spouštějte podrobnosti příkazu vyžadují `Microsoft.Compute/locations/runCommands/read` oprávnění, která integrované [čtečky](../../role-based-access-control/built-in-roles.md#reader) role a vyšší.

Spuštění příkaz vyžaduje `Microsoft.Compute/virtualMachines/runCommand/action` oprávnění, která [Přispěvatel](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) role a vyšší.

Můžete použít jednu z [předdefinované](../../role-based-access-control/built-in-roles.md) role nebo vytvořte [vlastní](../../role-based-access-control/custom-roles.md) role použít příkaz spustit.

## <a name="next-steps"></a>Další postup

Zobrazit, [spouštět skripty ve vaší virtuální počítač s Windows](run-scripts-in-vm.md) Další informace o další způsoby, jak spouštět skripty a příkazy vzdáleně ve vašem virtuálním počítači.