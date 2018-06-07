---
title: Spouštět skripty ve virtuálním počítači Azure Linux
description: Toto téma popisuje, jak ke spouštění skriptů na virtuálním počítači
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 209b476e21a3a9ba08eb907a279c7c5f8faa1c17
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660781"
---
# <a name="run-scripts-in-your-linux-vm"></a>Spouštět skripty ve virtuálním počítačům s Linuxem

K automatizaci úloh nebo řešení problémů, musíte ke spuštění příkazů ve virtuálním počítači. V následujícím článku poskytuje stručný přehled funkcí, které jsou k dispozici pro spouštění skripty a příkazy v rámci virtuálních počítačů.

## <a name="custom-script-extension"></a>Rozšíření vlastních skriptů

[Rozšíření vlastních skriptů](../extensions/custom-script-linux.md) používá primárně u post nasazení konfigurace a instalace softwaru.

* Stáhněte a spouštět skripty ve virtuálních počítačích Azure.
* Můžete spustit pomocí šablon Azure Resource Manageru, rozhraní příkazového řádku Azure, rozhraní REST API, prostředí PowerShell nebo portálu Azure.
* Soubory skriptu můžete stáhnout z úložiště Azure nebo GitHub nebo získané z vašeho počítače, kdy spustit z portálu Azure.
* Spustit skript prostředí PowerShell v počítačích s Windows a Bash skript v počítačích s Linuxem.
* Užitečné pro konfiguraci nasazení post, instalace softwaru a další konfigurace nebo úlohy správy.

## <a name="run-command"></a>Spusťte příkaz

[Spustit příkaz](run-command.md) funkce umožňuje virtuálního počítače a Správa aplikací a řešení problémů pomocí skriptů a je k dispozici i v případě, že tento počítač není sítě připojený.

* Spouštět skripty ve virtuálních počítačích Azure.
* Lze spouštět s využitím [portál Azure](run-command.md), [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [rozhraní příkazového řádku Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), nebo [prostředí PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand)
* Rychle spustit výstup skriptu a zobrazení a opakujte podle potřeby na portálu Azure.
* Skript lze zadat přímo, nebo můžete spustit jeden z předdefinovaných skriptů.
* Spustit skript prostředí PowerShell v počítačích s Windows a Bash skript v počítačích s Linuxem.
* Užitečné pro virtuální počítač a Správa aplikací a ke spouštění skriptů ve virtuálních počítačích, které nejsou připojené sítě.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

[Hybridní pracovní proces Runbooku](../../automation/automation-hybrid-runbook-worker.md) poskytuje obecné správu počítače, aplikace a prostředí uživatele vlastní skripty uložené na účtu Automation.

* Spouštět skripty ve službě Azure a počítače mimo Azure.
* Můžete spustit pomocí portálu Azure, Azure CLI, REST API, Powershellu, webhooku.
* Skripty, ukládání a správy na účtu Automation.
* Spusťte prostředí PowerShell, pracovní postup prostředí PowerShell, Python nebo grafický runbook
* Žádný časový limit na dobu běhu skriptu.
* Několik skriptů můžou běžet souběžně.
* Výstup skriptu úplné se vrátil a uloží.
* Historie úlohy je k dispozici pro 90 dní.
* Skripty můžete spustit jako místní systém nebo s uživatelem zadané přihlašovací údaje.
* Vyžaduje [ruční instalace](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Konzola sériového portu

[Konzoly sériového portu](serial-console.md) poskytuje přímý přístup k virtuálnímu počítači, podobně jako na situaci, kdy klávesnice připojené k virtuálnímu počítači.

* Příkazy spouštějte ve virtuálních počítačích Azure.
* Můžete spustit pomocí konzoly pro počítač založený na textu na portálu Azure.
* Přihlaste se k počítači s místní uživatelský účet.
* To užitečné, pokud je nutný přístup k virtuálnímu počítači bez ohledu na stav sítě nebo operační systém počítače.

## <a name="next-steps"></a>Další postup

Další informace o různých funkcí, které jsou k dispozici pro spouštění skripty a příkazy v rámci virtuálních počítačů.

* [Rozšíření vlastních skriptů](../extensions/custom-script-linux.md)
* [Spusťte příkaz](run-command.md)
* [Hybridní pracovní proces Runbooku](../../automation/automation-hybrid-runbook-worker.md)
* [Konzoly sériového portu](serial-console.md)