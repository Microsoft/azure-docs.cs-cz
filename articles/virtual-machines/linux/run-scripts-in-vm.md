---
title: Spouštění skriptů na virtuálním počítači Azure Linux
description: Toto téma popisuje, jak spouštět skripty na virtuálním počítači.
services: automation
ms.service: virtual-machines
ms.collection: linux
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: how-to
ms.openlocfilehash: 633f4fa6254b2db7a802bb29bffc83b271216d49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601750"
---
# <a name="run-scripts-in-your-linux-vm"></a>Spouštění skriptů na VIRTUÁLNÍm počítači se systémem Linux

Pokud chcete automatizovat úlohy nebo řešit problémy, možná budete muset na virtuálním počítači spustit příkazy. Následující článek poskytuje stručný přehled funkcí, které jsou k dispozici ke spouštění skriptů a příkazů v rámci virtuálních počítačů.

## <a name="custom-script-extension"></a>Rozšíření vlastních skriptů

[Rozšíření vlastních skriptů](../extensions/custom-script-linux.md) se primárně používá pro konfiguraci po nasazení a instalaci softwaru.

* Stažení a spuštění skriptů na virtuálních počítačích Azure.
* Dá se spustit pomocí Azure Resource Manager šablon, Azure CLI, REST API, PowerShellu nebo Azure Portal.
* Soubory skriptu si můžete stáhnout z Azure Storage nebo GitHubu nebo z počítače, když je spustíte z Azure Portal.
* Spusťte skript prostředí PowerShell na počítačích s Windows a skriptu bash v počítačích se systémem Linux.
* Užitečné pro konfiguraci po nasazení, instalaci softwaru a další úlohy konfigurace nebo správy.

## <a name="run-command"></a>Spustit příkaz

Funkce [příkazu Run](run-command.md) umožňuje správu virtuálních počítačů a aplikací a odstraňování potíží pomocí skriptů a je k dispozici i v případě, že je počítač nedostupný, například pokud brána firewall hosta nemá otevřený port RDP nebo SSH.

* Spouštění skriptů na virtuálních počítačích Azure.
* Dá se spustit pomocí [Azure Portal](run-command.md), [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [Azure CLI](/cli/azure/vm/run-command#az_vm_run_command_invoke)nebo [PowerShellu](/powershell/module/az.compute/invoke-azvmruncommand) .
* Rychlé spuštění skriptu a zobrazení výstupu a opakování podle potřeby v Azure Portal.
* Skript lze zadat přímo nebo můžete spustit jeden z vestavěných skriptů.
* Spusťte skript prostředí PowerShell na počítačích s Windows a skriptu bash v počítačích se systémem Linux.
* Hodí se pro správu virtuálních počítačů a aplikací a spouštění skriptů ve virtuálních počítačích, které jsou nedostupné.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

[Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) poskytuje obecnou správu počítačů, aplikací a prostředí pomocí vlastních skriptů uživatele uložených v účtu Automation.

* Spouštějte skripty v počítačích Azure a mimo Azure.
* Dá se spustit pomocí Azure Portal, Azure CLI, REST API, PowerShellu, Webhooku.
* Skripty uložené a spravované v účtu Automation.
* Spuštění PowerShellu, pracovního postupu PowerShellu, Pythonu nebo grafických runbooků
* Doba běhu skriptu není nijak omezena.
* Současně může běžet více skriptů.
* Je vrácen a uložen úplný výstup skriptu.
* Historie úlohy je k dispozici po dobu 90 dnů.
* Skripty mohou běžet jako místní systém nebo s přihlašovacími údaji poskytnutými uživatelem.
* Vyžaduje [ruční instalaci](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Konzola sériového portu

[Sériová konzola](/troubleshoot/azure/virtual-machines/serial-console-linux) poskytuje přímý přístup k virtuálnímu počítači, podobně jako s klávesnicí připojenou k virtuálnímu počítači.

* Spusťte příkazy na virtuálních počítačích Azure.
* Dá se spustit pomocí textové konzole pro počítač v Azure Portal.
* Přihlaste se k počítači pomocí místního uživatelského účtu.
* Užitečné v případě, že je potřeba přístup k virtuálnímu počítači bez ohledu na stav sítě nebo operačního systému počítače.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o různých funkcích, které jsou k dispozici ke spouštění skriptů a příkazů v rámci virtuálních počítačů.

* [Rozšíření vlastních skriptů](../extensions/custom-script-linux.md)
* [Spuštění příkazu](run-command.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)
* [Sériová konzola](/troubleshoot/azure/virtual-machines/serial-console-linux)