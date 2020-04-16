---
title: Náprava nekompatibilních konfiguračních serverů stavu automatizace Azure
description: Jak znovu použít konfigurace na vyžádání na servery, kde byl stav konfigurace posunut
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: cc5884e1f70bdccee4e7a113e6e3ee2d6604b50a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406085"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Náprava nekompatibilních serverů DSC

Pokud jsou servery registrovány pomocí konfigurace stavu `ApplyOnly`azure `ApplyandMonitor`automatizace, je režim konfigurace nastaven na , , nebo `ApplyAndAutoCorrect`. Pokud režim není nastaven `ApplyAndAutoCorrect`na , servery, které se z jakéhokoli důvodu pohybují od kompatibilního stavu, zůstanou nekompatibilní, dokud nebudou ručně opraveny.

Azure compute nabízí funkci s názvem Spustit příkaz, která zákazníkům umožňuje spouštět skripty uvnitř virtuálních počítačů.
Tento dokument obsahuje ukázkové skripty pro tuto funkci při ruční opravě posunu konfigurace.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Správný posun virtuálních počítačů s Windows pomocí PowerShellu

Podrobné pokyny pomocí funkce Spustit příkaz na virtuálních počítačích s Windows najdete na stránce dokumentace [Spuštění skriptů prostředí PowerShell v virtuálním počítači systému Windows s příkazem Spustit](/azure/virtual-machines/windows/run-command).

Chcete-li vynutit uzel konfigurace stavu azure automatizace stáhnout nejnovější konfiguraci a použít ji, použijte [rutinu Update-DscConfiguration.](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Správný posun virtuálních počítačů Linuxu

Podobná funkce není v současné době k dispozici pro servery Linux.
Jedinou možností je zopakovat proces registrace.

Uzly Azure můžete opravit posun z portálu Azure nebo pomocí rutin modulu Az. Podrobnosti o tomto procesu jsou popsány v [onboardingových počítačích pro správu pomocí konfigurace stavu automatizace Azure](automation-dsc-onboarding.md#onboard-vms-by-using-the-azure-portal).
U hybridních uzlů můžete opravit posun pomocí zahrnutých skriptů Pythonu.
Viz [PowerShell DSC pro linuxové repo](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Další kroky

- Odkaz na rutinu prostředí PowerShell naleznete v tématu [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Příklad použití konfigurace stavu azure automatizace v kanálu průběžného nasazení najdete v [tématu průběžné nasazení pomocí konfigurace stavu automatizace Azure a Chocolatey](automation-dsc-cd-chocolatey.md).