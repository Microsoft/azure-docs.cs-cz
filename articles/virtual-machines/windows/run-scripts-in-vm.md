---
title: Spouštění skriptů ve virtuálním počítači Azure s Windows
description: Toto téma popisuje, jak spouštět skripty ve virtuálním počítači se systémem Windows.
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e10cab5261d6bd970135273bd4632b7c916641de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67476762"
---
# <a name="run-scripts-in-your-windows-vm"></a>Spouštění skriptů ve virtuálním počítači s Windows

Chcete-li automatizovat úlohy nebo řešit problémy, budete muset spustit příkazy ve virtuálním provozu. Následující článek poskytuje stručný přehled funkcí, které jsou k dispozici pro spouštění skriptů a příkazů v rámci virtuálních počítačů.

## <a name="custom-script-extension"></a>Rozšíření vlastních skriptů

Rozšíření [vlastní skript se](../extensions/custom-script-windows.md) používá především pro konfiguraci po nasazení a instalaci softwaru.

* Stahování a spouštění skriptů ve virtuálních počítačích Azure.
* Dá se spouštět pomocí šablon Azure Resource Manager, Azure CLI, REST API, PowerShellu nebo portálu Azure.
* Soubory skriptů se můžou stahovat z azure úložiště nebo GitHubu nebo poskytovat z počítače, když je běží z webu Azure Portal.
* Spusťte skript PowerShell u počítačů se systémem Windows a skript Bash v počítačích s Linuxem.
* Užitečné pro konfiguraci po nasazení, instalaci softwaru a další úlohy konfigurace nebo správy.

## <a name="run-command"></a>Spusťte příkaz .

Funkce [Spustit příkaz](run-command.md) umožňuje správu virtuálních strojů a aplikací a odstraňování potíží pomocí skriptů a je k dispozici i v případě, že počítač není dostupný, například pokud brána firewall hosta nemá otevřený port RDP nebo SSH.

* Spouštění skriptů ve virtuálních počítačích Azure.
* Dá se spouštět pomocí [portálu Azure](run-command.md)Portal , [rozhraní REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), rozhraní API [Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)nebo [Prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand)
* Rychle spusťte skript a zobrazte výstup a opakujte podle potřeby na webu Azure Portal.
* Skript lze zadat přímo nebo můžete spustit jeden z vestavěných skriptů.
* Spusťte skript PowerShell u počítačů se systémem Windows a skript Bash v počítačích s Linuxem.
* Užitečné pro správu virtuálních počítačů a aplikací a pro spouštění skriptů ve virtuálních počítačích, které jsou nedostupné.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

[Hybridní runbook worker](../../automation/automation-hybrid-runbook-worker.md) poskytuje obecnou správu počítače, aplikací a prostředí s vlastními skripty uživatele uloženými v účtu Automation.

* Spouštění skriptů v azure a počítačích mimo Azure.
* Dá se spouštět pomocí portálu Azure, Azure CLI, ROZHRANÍ REST API, PowerShellu a webhooku.
* Skripty uložené a spravované v účtu automatizace.
* Spuštění powershellových, pracovních postupů powershellu, pythonu nebo grafických runbooků
* Žádný časový limit na dobu spuštění skriptu.
* Souběžně lze spouštět více skriptů.
* Úplný výstup skriptu je vrácen a uložen.
* Historie úloh je k dispozici po dobu 90 dnů.
* Skripty lze spustit jako místní systém nebo s pověřeními zadanými uživatelem.
* Vyžaduje [ruční instalaci](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Konzola sériového portu

[Konzola Serial](serial-console.md) poskytuje přímý přístup k virtuálnímu virtuálnímu zařízení, podobně jako klávesnice připojená k virtuálnímu virtuálnímu zařízení.

* Spouštění příkazů ve virtuálních počítačích Azure.
* Lze spustit pomocí textové konzoly do počítače na webu Azure Portal.
* Přihlaste se k počítači pomocí místního uživatelského účtu.
* Užitečné při přístupu k virtuálnímu počítači bez ohledu na stav sítě nebo operačního systému počítače.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o různých funkcích, které jsou k dispozici pro spouštění skriptů a příkazů v rámci virtuálních počítačů.

* [Rozšíření vlastního skriptu](../extensions/custom-script-windows.md)
* [Spustit příkaz](run-command.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)
* [Sériová konzola](serial-console.md)
