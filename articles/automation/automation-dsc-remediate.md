---
title: Navádění nekompatibilních konfiguračních serverů stavu azure automatizace
description: Jak znovu použít konfigurace na vyžádání na servery, kde byl stav konfigurace posunut
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: a6c7639cb4988eb13dfaa1c151085cda6e53b5d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68614495"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Oprava serverů DSC, které nevyhovují předpisům

Když jsou servery registrovány pomocí konfigurace stavu automatizace Azure, "Režim konfigurace" je nastavena na ApplyOnly, ApplyandMonitor nebo ApplyAndAutoCorrect.
Pokud režim není nastaven na automatické opravy, servery, které se z jakéhokoli důvodu odkloní od kompatibilního stavu, zůstanou nekompatibilní, dokud nebudou ručně opraveny.

Azure compute nabízí funkci s názvem Spustit příkaz, která zákazníkům umožňuje spouštět skripty uvnitř virtuálních počítačů.
Tento dokument obsahuje ukázkové skripty pro tuto funkci při ruční opravě posunu konfigurace.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Správný posun virtuálních počítačů s Windows pomocí PowerShellu

Podrobné pokyny pomocí funkce Spustit příkaz na virtuálních počítačích s Windows najdete na stránce dokumentace [Spuštění skriptů prostředí PowerShell v virtuálním počítači se systémem Windows pomocí příkazu Spustit](/azure/virtual-machines/windows/run-command).

Chcete-li vynutit uzel konfigurace stavu azure automatizace stáhnout `Update-DscConfiguration` nejnovější konfiguraci a použít ji, použijte rutinu.
Podrobnosti naleznete v dokumentaci k rutině [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Správný posun virtuálních počítačů Linuxu

Podobná funkce není v současné době k dispozici pro servery Linux.
Jedinou možností je zopakovat proces registrace.
U uzlů Azure lze opravu driftu provést z portálu nebo pomocí rutin Az Automation.
Podrobnosti o tomto procesu jsou popsány na stránce [Onboarding machines pro správu pomocí konfigurace stavu automatizace Azure](/azure/automation/automation-dsc-onboarding#azure-portal).
U hybridních uzlů lze korekci driftu provést pomocí zahrnutých skriptů Pythonu.
Podívejte se na dokumentaci v [prostředí PowerShell DSC pro linuxové repo](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Další kroky

- Informace o odkazu na rutinu prostředí PowerShell najdete v tématu [Rutiny konfigurace stavu automatizace Azure](/powershell/module/azurerm.automation/#automation)
- Příklad použití konfigurace stavu azure automatizace v kanálu průběžného nasazení najdete v [tématu Průběžné nasazení pomocí konfigurace stavu automatizace Azure a Chocolatey](automation-dsc-cd-chocolatey.md)
