---
title: Napravit servery konfigurace stavu nesplňujících požadavky Azure Automation
description: V tomto článku se dozvíte, jak znovu použít konfigurace na vyžádání na servery, na kterých se změnil stav konfigurace.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: ff785bf3ace7c65f83fe8e505f0544edd24776d8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836851"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>Napravit servery konfigurace stavu nesplňujících požadavky Azure Automation

Pokud jsou servery registrovány s konfigurací stavu Azure Automation, režim konfigurace je nastaven na `ApplyOnly` , `ApplyandMonitor` nebo `ApplyAndAutoCorrect` . Pokud není režim nastavený na `ApplyAndAutoCorrect` , servery, které přestanou z nekompatibilního stavu, zůstávají z nějakého důvodu nekompatibilní, dokud je ručně neopravíte.

Azure COMPUTE nabízí funkci s názvem Run Command, která umožňuje zákazníkům spouštět skripty v rámci virtuálních počítačů.
Tento dokument popisuje příklady skriptů pro tuto funkci při ruční opravě posunu konfigurace.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Správný posun virtuálních počítačů s Windows pomocí PowerShellu

Pomocí funkce příkazu můžete opravit posun virtuálních počítačů s Windows `Run` . Podívejte [se na téma spuštění skriptů PowerShellu na virtuálním počítači s Windows pomocí příkazu Spustit](/azure/virtual-machines/windows/run-command).

Pokud chcete vynutit, aby uzel Konfigurace stavu Azure Automation stáhnout nejnovější konfiguraci a použít ho, použijte rutinu [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) .

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Správný posun virtuálních počítačů se systémem Linux

Pro virtuální počítače se systémem Linux není k dispozici možnost použití `Run` příkazu. Pro tyto počítače můžete jenom opravit posun opakováním procesu registrace. 

U uzlů Azure můžete ze Azure Portal opravit posun pomocí rutin AZ Module. Podrobnosti o tomto procesu najdete v dokumentaci [Povolení virtuálního počítače pomocí Azure Portal](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal).

U hybridních uzlů můžete opravit posun pomocí skriptů Pythonu. Viz [provádění operací DSC z počítače se systémem Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Další kroky

- Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Příklad použití konfigurace stavu Azure Automation v kanálu nepřetržitého nasazení najdete v tématu [Nastavení průběžného nasazování s čokoládou](automation-dsc-cd-chocolatey.md).