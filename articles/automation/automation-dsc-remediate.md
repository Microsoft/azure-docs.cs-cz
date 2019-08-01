---
title: Napravit nekompatibilní servery konfigurace stavu Azure Automation
description: Postup opakovaného aplikování konfigurace na vyžádání na servery, na kterých se změnil stav konfigurace
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: a6c7639cb4988eb13dfaa1c151085cda6e53b5d3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68614495"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Oprava serverů DSC, které nevyhovují předpisům

Pokud jsou servery registrovány s konfigurací stavu Azure Automation, je režim konfigurace nastaven na hodnotu ApplyOnly, ApplyandMonitor nebo ApplyAndAutoCorrect.
Pokud režim není nastavený na automatických opravách, servery, které se od stavu v pořádku z nějakého důvodu, zůstanou nekompatibilní, dokud je ručně neopravíte.

Azure COMPUTE nabízí funkci s názvem Run Command, která umožňuje zákazníkům spouštět skripty v rámci virtuálních počítačů.
Tento dokument popisuje příklady skriptů pro tuto funkci při ruční opravě posunu konfigurace.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Správný posun virtuálních počítačů s Windows pomocí PowerShellu

Podrobné pokyny k použití funkce příkazu spustit na virtuálních počítačích s Windows najdete na stránce dokumentace [spuštění skriptů PowerShellu na virtuálním počítači s Windows pomocí příkazu Spustit](/azure/virtual-machines/windows/run-command).

Pokud chcete vynutit, aby uzel Konfigurace stavu Azure Automation stáhnout nejnovější konfiguraci a použít ho, použijte `Update-DscConfiguration` rutinu.
Podrobnosti najdete v dokumentaci k rutině [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Správný posun virtuálních počítačů se systémem Linux

Podobné funkce nejsou aktuálně k dispozici pro servery se systémem Linux.
Jedinou možností je zopakovat proces registrace.
V případě uzlů Azure je možné na portálu provést změnu oproti chybám pomocí rutin AZ Automation.
Podrobnosti o tomto procesu jsou popsány v části [počítače připojování počítačů pro správu podle konfigurace stavu Azure Automation](/azure/automation/automation-dsc-onboarding#azure-portal).
U hybridních uzlů se dá pomocí zahrnutých skriptů Pythonu udělat korekci.
Podívejte se na dokumentaci v [úložišti POWERSHELL DSC pro Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Další kroky

- Referenční informace k rutinám PowerShellu najdete v tématu [rutiny konfigurace stavu Azure Automation](/powershell/module/azurerm.automation/#automation) .
- Příklad použití konfigurace stavu Azure Automation v kanálu průběžného nasazování najdete v tématu průběžné [nasazování pomocí Azure Automation konfigurace stavu a čokolády](automation-dsc-cd-chocolatey.md) .
