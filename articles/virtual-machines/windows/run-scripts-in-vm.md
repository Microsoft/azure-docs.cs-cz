---
title: Spouštění skriptů ve Virtuálním počítači Windows Azure
description: Toto téma popisuje, jak spouštět skripty virtuálního počítače s Windows
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: ec34ff874eae9bbdd53470f135bcf0c182d5daed
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55983334"
---
# <a name="run-scripts-in-your-windows-vm"></a>Spouštění skriptů ve virtuálním počítači Windows

K automatizaci úloh nebo řešení problémů, budete muset na virtuálním počítači spouštět příkazy. Následující článek poskytuje stručný přehled funkcí, které je možné spouštět skripty a příkazy v rámci vašich virtuálních počítačů.

## <a name="custom-script-extension"></a>Rozšíření vlastních skriptů

[Rozšíření vlastních skriptů](../extensions/custom-script-windows.md) slouží především pro příspěvek nasazení konfigurace a instalace softwaru.

* Stažení a spuštění skriptů ve službě Azure virtual machines.
* Je možné spustit pomocí šablon Azure Resource Manageru, rozhraní příkazového řádku Azure, rozhraní REST API, Powershellu nebo webu Azure portal.
* Soubory skriptu si můžete stáhnout z úložiště Azure nebo z Githubu, nebo z vašeho počítače, kdy spustit z webu Azure portal k dispozici.
* Spustit skript prostředí PowerShell na počítačích s Windows nebo z prostředí Bash skript v počítačích s Linuxem.
* Užitečné pro konfiguraci po nasazení, instalace softwaru a další konfigurace nebo úlohy správy.

## <a name="run-command"></a>Spustit příkaz

[Spustit příkaz](run-command.md) funkce umožňuje virtuálních počítačů a správu aplikací a řešení problémů pomocí skriptů a je k dispozici, i když počítač není dostupný, pokud brána firewall hosta nemá port RDP nebo SSH Otevřete.

* Spouštění skriptů ve službě Azure virtual machines.
* Lze spouštět s využitím [webu Azure portal](run-command.md), [rozhraní REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [rozhraní příkazového řádku Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke), nebo [prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand)
* Rychle spusťte skript a zobrazit výstup a opakujte podle potřeby na webu Azure Portal.
* Skript může být zadán přímo nebo některou z předdefinovaných skripty.
* Spustit skript prostředí PowerShell na počítačích s Windows nebo z prostředí Bash skript v počítačích s Linuxem.
* Užitečné pro virtuální počítač a správu aplikací a ke spouštění skriptů ve virtuálních počítačích, které nejsou dostupné.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

[Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) poskytuje obecné správy počítačů, aplikací a prostředí ve vlastních skriptech uživatele uložené v účtu Automation.

* Spouštění skriptů v Azure i počítače umístěné mimo Azure.
* Je možné spustit pomocí webu Azure portal, rozhraní příkazového řádku Azure, rozhraní REST API, Powershellu, webhooku.
* Skripty, ukládání a správy účtu Automation.
* Spusťte prostředí PowerShell, pracovní postup prostředí PowerShell, Python nebo grafický runbook
* Čas spuštění žádný časový limit na skript.
* Několik skriptů můžou běžet souběžně.
* Úplná skript výstup je vrácena a uložené.
* Historie úloh je k dispozici po dobu 90 dnů.
* Skripty můžete spouštět jako místní systém nebo s uživatelem zadané přihlašovací údaje.
* Vyžaduje [ruční instalace](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Konzola sériového portu

[Konzoly sériového portu](serial-console.md) poskytuje přímý přístup k virtuálnímu počítači, podobně jako s tím, že klávesnice připojené k virtuálnímu počítači.

* Spouštění příkazů ve službě Azure virtual machines.
* Je možné spustit pomocí konzoly založený na textu do počítače na webu Azure Portal.
* Přihlaste se k počítači pomocí místního uživatelského účtu.
* Užitečné, pokud je nutný přístup k virtuálnímu počítači bez ohledu na stav sítě nebo operační systém počítače.

## <a name="next-steps"></a>Další postup

Další informace o různých funkcí, které je možné spouštět skripty a příkazy v rámci vašich virtuálních počítačů.

* [Rozšíření vlastních skriptů](../extensions/custom-script-windows.md)
* [Spusťte příkaz](run-command.md)
* [Funkce hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)
* [Konzola sériového portu](serial-console.md)
