---
title: Spouštění skriptů prostředí ve virtuálním počítači s Linuxem v Azure
description: Toto téma popisuje, jak spouštět skripty ve virtuálním počítači s Linuxem v Azure pomocí příkazu Spustit
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 9ba60f770c094f65ee5a4ed6dc21a5e07bac3d27
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48267745"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Spouštění skriptů prostředí v virtuálního počítače s Linuxem pomocí příkazu Spustit

Spusťte příkaz používá agent virtuálního počítače ke spuštění skriptů prostředí v rámci virtuálního počítače Azure s Linuxem. Tyto skripty lze použít obecné počítače nebo Správa aplikací a umožňuje rychle diagnostikovat a opravit problémy s přístup a síť virtuálního počítače a získat zpět na dobrý stav virtuálního počítače.

## <a name="benefits"></a>Výhody

Existuje několik možností, které slouží k přístupu k virtuálním počítačům. Spusťte příkaz můžete spustit skripty na virtuálních počítačích vzdáleně pomocí agenta virtuálního počítače. Spusťte příkaz se dá použít prostřednictvím webu Azure portal, [rozhraní REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), nebo [rozhraní příkazového řádku Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) pro virtuální počítače s Linuxem.

Tato možnost je užitečná ve všech scénářích, kde chcete spustit skript v rámci virtuálních počítačů a je jedním z jediný způsob, jak odstraňovat potíže a opravte virtuální počítač, který nemá protokol RDP nebo otevřít SSH port z důvodu nesprávné sítě nebo správce konfigurace.

## <a name="restrictions"></a>Omezení

Následuje seznam omezení, které jsou k dispozici při použití příkazu Spustit.

* Výstup se omezí na poslední 4096 bajtů
* Minimální doba pro spuštění skriptu přibližně 20 sekund
* Skripty spouštěné ve výchozím nastavení se zvýšenými oprávněními uživatele v Linuxu
* Může spustit jeden skript v čase
* Skripty, které výzvu k zadání informace (interaktivní režim) nejsou podporovány.
* Nelze zrušit spouštění skriptu
* Maximální doba, kterou můžete spustit skript je 90 minut, po kterém vyprší časový limit
* Odchozí připojení z virtuálního počítače je potřeba vrátit výsledky ze skriptu.

## <a name="azure-cli"></a>Azure CLI

Následuje příklad použití [az vm-příkaz Spustit](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) příkaz spustit skript prostředí na virtuálním počítači Azure s Linuxem.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Ke spuštění příkazů jako jiný uživatel, můžete použít `sudo -u` k určení uživatelského účtu, který chcete použít.

## <a name="azure-portal"></a>portál Azure

Přejděte k virtuálnímu počítači s [Azure](https://portal.azure.com) a vyberte **spusťte příkaz** pod **operace**. Zobrazí se seznam dostupných příkazů ke spuštění ve virtuálním počítači.

![Spuštění seznamu příkazů](./media/run-command/run-command-list.png)

Zvolte příkaz spustit. Některé příkazy mohou mít nepovinné nebo povinné vstupní parametry. Pro tyto příkazy jsou uvedeny parametry jako textových polí můžete zadávat vstupní hodnoty. U každého příkazu můžete zobrazit skript, který je spuštěn tak, že rozbalíte **zobrazit skript**. **RunShellScript** se liší od dalších příkazů, protože to umožňuje poskytovat vlastní skript. 

> [!NOTE]
> Integrované příkazy se nedají upravovat.

Po příkazu je vybrán, klikněte na tlačítko **spustit** pro spuštění skriptu. Skript spustí a po dokončení vrátí výstup a chyby v okně výstup. Následující snímek obrazovky ukazuje příklad výstupu spuštění **ifconfig** příkazu.

![Spusťte skript výstup příkazu](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>Dostupné příkazy

Tato tabulka obsahuje seznam příkazů, které jsou k dispozici na virtuálních počítačích s Linuxem. **RunShellScript** příkaz lze použít ke spuštění libovolného vlastního skriptu, který chcete.

|**Název**|**Popis**|
|---|---|
|**RunShellScript**|Spustí skript prostředí Linux.|
|**ifconfig**| Získáte konfiguraci všech síťových rozhraní.|

## <a name="limiting-access-to-run-command"></a>Omezení přístupu ke spuštění příkazu

Výpis spouštění příkazů nebo s podrobnostmi o příkaz vyžaduje `Microsoft.Compute/locations/runCommands/read` oprávnění, která předdefinované [čtečky](../../role-based-access-control/built-in-roles.md#reader) role a vyšší.

Spuštění příkazu vyžaduje `Microsoft.Compute/virtualMachines/runCommand/action` oprávnění, která [Přispěvatel](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) role a vyšší.

Můžete použít jednu z [integrované](../../role-based-access-control/built-in-roles.md) role nebo vytvořte [vlastní](../../role-based-access-control/custom-roles.md) roli spustit příkaz.

## <a name="next-steps"></a>Další postup

Zobrazit, [spouštět skripty ve virtuálním počítačům s Linuxem](run-scripts-in-vm.md) Další informace o dalších způsobech vzdáleně spouštět skripty a příkazy ve virtuálním počítači.
