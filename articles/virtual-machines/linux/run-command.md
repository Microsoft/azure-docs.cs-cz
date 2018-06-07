---
title: Spouštět skripty prostředí v virtuální počítač s Linuxem v Azure
description: Toto téma popisuje, jak spouštět skripty v rámci služby Azure Linux virtuálního počítače pomocí příkazu Spustit
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6f21452ddc6c8a48392d24615e8dbcbba8b996c8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660780"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Spusťte prostředí skripty ve virtuálním počítačům s Linuxem se spustit příkaz

Spusťte příkaz umožňuje spouštět skripty prostředí v rámci virtuálního počítače Azure Linux bez ohledu na připojení k síti. Tyto skripty lze použít obecné počítače nebo Správa aplikací a umožňuje rychle diagnostikovat a opravit problémy přístup a síť virtuálních počítačů a získat zpět na dobrý stav virtuálního počítače.

## <a name="benefits"></a>Výhody

Nejsou k dispozici více možností, které slouží k přístupu k virtuálním počítačům. Spusťte příkaz můžete spustit skripty na virtuální počítače bez ohledu na připojení k síti a je k dispozici ve výchozím nastavení (bez instalace požadované). Spusťte příkaz lze použít prostřednictvím portálu Azure [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [rozhraní příkazového řádku Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), nebo [prostředí PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand).

Tato možnost je užitečná ve všech scénářích, kde chcete spustit skript witin virtuální počítače a jeden jediný způsob, jak vyřešit a opravte virtuální počítač, který není připojený k síti z důvodu nesprávné sítě nebo správce konfigurace.

## <a name="restrictions"></a>Omezení

Následuje seznam omezení, které jsou k dispozici při použití příkazu Spustit.

* Výstup je omezená na poslední 4096 bajtů
* Minimální hodnota času spuštění skriptu přibližně 20 sekund
* Spustit skripty jako zvýšenými v systému Linux
* Jeden skript současně může spustit.
* Nelze zrušit spouštění skriptu
* Maximální doba spuštění skriptu je 90 minut, po který dojde k vypršení časového limitu

## <a name="azure-cli"></a>Azure CLI

Následuje příklad použití [az virtuální počítač spustit – příkaz](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) příkaz ke spuštění skriptu prostředí na virtuální počítač Azure Linux.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

## <a name="azure-portal"></a>Azure Portal

Přejděte do virtuálního počítače v [Azure](https://portal.azure.com) a vyberte **spusťte příkaz** pod **operace**. Zobrazí seznam dostupných příkazů pro spuštění ve virtuálním počítači.

![Spusťte příkaz seznamu](./media/run-command/run-command-list.png)

Vyberte příkaz ke spuštění. Některé příkazy může mít volitelné nebo požadované vstupní parametry. Parametry jako textové pole k zabezpečení vstupní hodnoty jsou k dispozici pro tyto příkazy. U každého příkazu můžete zobrazit skript, který je spuštěn rozšířením **zobrazit skript**. **RunPowerShellScript** se liší od jinými příkazy, jako umožňuje zadat vlastní skript. 

> [!NOTE]
> Integrované příkazy se nedají upravovat.

Jakmile je zvoleno příkaz, klikněte na možnost **spustit** pro spuštění skriptu. Skript spustí a po dokončení vrátí výstup a všechny chyby v okně výstupu. Následující snímek obrazovky ukazuje příklad výstup spuštění **ifconfig** příkaz.

![Spustit výstup příkazu skriptu](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>Dostupné příkazy

Tato tabulka uvádí seznam příkazů, které jsou k dispozici pro virtuální počítače s Linuxem. **RunShellScript** příkaz lze použít ke spuštění všech vlastních skriptů, které chcete.

|**Název**|**Popis**|
|---|---|
|**RunShellScript**|Spustí skript prostředí Linux.|
|**ifconfig**| Získáte konfiguraci všech síťových rozhraní.|

## <a name="limiting-access-to-run-command"></a>Omezení přístupu ke spuštění příkazu

Zobrazení nebo výpis příkazy spouštějte podrobnosti příkazu vyžadují `Microsoft.Compute/locations/runCommands/read` oprávnění, která integrované [čtečky](../../role-based-access-control/built-in-roles.md#reader) role a vyšší.

Spuštění příkaz vyžaduje `Microsoft.Compute/virtualMachines/runCommand/action` oprávnění, která [Přispěvatel](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) role a vyšší.

Můžete použít jednu z [předdefinované](../../role-based-access-control/built-in-roles.md) role nebo vytvořte [vlastní](../../role-based-access-control/custom-roles.md) role použít příkaz spustit.

## <a name="next-steps"></a>Další postup

Zobrazit, [spouštět skripty ve virtuálním počítačům s Linuxem](run-scripts-in-vm.md) Další informace o další způsoby, jak spouštět skripty a příkazy vzdáleně ve vašem virtuálním počítači.
