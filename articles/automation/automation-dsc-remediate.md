---
title: Napravit servery konfigurace stavu nesplňujících požadavky Azure Automation
description: Postup opakovaného aplikování konfigurace na vyžádání na servery, na kterých se změnil stav konfigurace
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: dfe62c54bfb10d70f1dbf19daec90eec68e66431
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406085"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Napravit nekompatibilní servery DSC

Pokud jsou servery registrovány s konfigurací stavu Azure Automation, režim konfigurace je nastaven na `ApplyOnly`, `ApplyandMonitor`nebo `ApplyAndAutoCorrect`. Pokud není režim nastavený na `ApplyAndAutoCorrect`, servery, které přestanou z nekompatibilního stavu, zůstávají z nějakého důvodu nekompatibilní, dokud je ručně neopravíte.

Azure COMPUTE nabízí funkci s názvem Run Command, která umožňuje zákazníkům spouštět skripty v rámci virtuálních počítačů.
Tento dokument popisuje příklady skriptů pro tuto funkci při ruční opravě posunu konfigurace.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Správný posun virtuálních počítačů s Windows pomocí PowerShellu

Podrobné pokyny k použití funkce příkazu spustit na virtuálních počítačích s Windows najdete na stránce dokumentace [spuštění skriptů PowerShellu na virtuálním počítači s Windows pomocí příkazu Spustit](/azure/virtual-machines/windows/run-command).

Pokud chcete vynutit, aby uzel Konfigurace stavu Azure Automation stáhnout nejnovější konfiguraci a použít ho, použijte rutinu [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) .

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Správný posun virtuálních počítačů se systémem Linux

Podobné funkce nejsou aktuálně k dispozici pro servery se systémem Linux.
Jedinou možností je zopakovat proces registrace.
U uzlů Azure můžete ze Azure Portal opravit posun pomocí rutin AZ Module. Podrobnosti o tomto procesu najdete v dokumentaci [k připojování počítačů pro správu pomocí konfigurace stavu Azure Automation](automation-dsc-onboarding.md#onboard-a-vm-using-azure-portal).
U hybridních uzlů můžete opravit posun pomocí zahrnutých skriptů Pythonu.
Podívejte se [na úložiště prostředí POWERSHELL DSC pro Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Další kroky

- Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Příklad použití konfigurace stavu Azure Automation v kanálu průběžného nasazování najdete v tématu [průběžné nasazování pomocí konfigurace Azure Automation stavu a čokolády](automation-dsc-cd-chocolatey.md).