---
title: Postup upgradu agenta závislostí Azure Monitor pro virtuální počítače
description: Tento článek popisuje, jak upgradovat agenta Azure Monitor pro virtuální počítače závislostí pomocí příkazového řádku, Průvodce instalací a dalších metod.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: 01dd8422658aa0c8982733e48782efd27c1bf5be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81617841"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>Postup upgradu agenta závislostí Azure Monitor pro virtuální počítače

Po počátečním nasazení agenta závislostí Azure Monitor pro virtuální počítače se uvolní aktualizace, které zahrnují opravy chyb nebo podporu nových funkcí nebo funkcí.  Tento článek vám pomůže pochopit dostupné metody a postup ručního provedení upgradu nebo automatizace.

## <a name="upgrade-options"></a>Možnosti upgradu 

Agenta závislostí pro systém Windows a Linux lze upgradovat na nejnovější verzi ručně nebo automaticky v závislosti na scénáři nasazení a prostředí, ve kterém počítač běží. K upgradu agenta lze použít následující metody.

|Prostředí |Metoda instalace |Metoda upgradu |
|------------|--------------------|---------------|
|Virtuální počítač Azure | Rozšíření VM agenta závislosti pro [systémy Windows](../../virtual-machines/extensions/agent-dependency-windows.md) a [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | Agent je automaticky upgradován ve výchozím nastavení, pokud nenakonfigurovali šablonu Azure Resource Manager tak, aby se odhlásila nastavením vlastnosti *autoUpgradeMinorVersion* na **hodnotu false**. Upgrade pro podverzi, kde je automatický upgrade zakázán, a upgrade hlavní verze se řídí stejným způsobem – odinstalovat a přeinstalovat rozšíření. |
| Vlastní image virtuálních počítačů Azure | Ruční instalace agenta závislostí pro systém Windows/Linux | Aktualizace virtuálních počítačů na nejnovější verzi agenta je třeba provést z příkazového řádku se spuštěným balíčkem Instalační služby systému Windows nebo pomocí automatického extrahování a instalovatelných sad prostředků prostředí pro systém Linux.|
| Virtuální počítače mimo Azure | Ruční instalace agenta závislostí pro systém Windows/Linux | Aktualizace virtuálních počítačů na nejnovější verzi agenta je třeba provést z příkazového řádku se spuštěným balíčkem Instalační služby systému Windows nebo pomocí automatického extrahování a instalovatelných sad prostředků prostředí pro systém Linux. |

## <a name="upgrade-windows-agent"></a>Upgradovat agenta Windows 

Chcete-li aktualizovat agenta na virtuálním počítači s Windows na nejnovější verzi, která není nainstalovaná pomocí rozšíření virtuálního počítače agenta závislosti, spusťte buď z příkazového řádku, skriptu nebo jiného řešení automatizace, nebo pomocí Průvodce instalací InstallDependencyAgent-Windows.exe.  

Nejnovější verzi agenta pro Windows si můžete stáhnout [tady](https://aka.ms/dependencyagentwindows).

### <a name="using-the-setup-wizard"></a>Pomocí Průvodce instalací

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Spusťte **InstallDependencyAgent-Windows.exe** pro spuštění Průvodce instalací nástroje.
   
3. Postupujte podle pokynů průvodce **instalací nástroje Dependency agent** k odinstalaci předchozí verze agenta závislostí a pak nainstalujte nejnovější verzi.


### <a name="from-the-command-line"></a>Z příkazového řádku

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Spusťte následující příkaz.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    `/RebootMode=manual`Parametr brání upgradu v automatickém restartování počítače, pokud některé procesy používají soubory z předchozí verze a mají pro ně zámek. 

3. Pokud chcete potvrdit, že upgrade proběhl úspěšně, zkontrolujte `install.log` podrobné informace o nastavení. Adresář protokolu je *%ProgramFiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Upgrade agenta pro Linux 

Upgrade z předchozích verzí agenta závislostí v systému Linux je podporován a proveden podle stejného příkazu jako nová instalace.

Nejnovější verzi agenta pro Linux si můžete stáhnout [odsud](https://aka.ms/dependencyagentlinux).

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Spusťte následující příkaz jako kořenový `sh InstallDependencyAgent-Linux64.bin -s` . 

Pokud se nepovede spustit agenta závislostí, podrobnější informace o chybě najdete v protokolech. V agentech Linux se adresář protokolu */var/opt/Microsoft/Dependency-agent/log*. 

## <a name="next-steps"></a>Další kroky

Pokud chcete zastavit monitorování virtuálních počítačů po určitou dobu nebo Azure Monitor pro virtuální počítače zcela odebrat, přečtěte si téma [zakázání monitorování virtuálních počítačů v Azure monitor pro virtuální počítače](vminsights-optout.md).
