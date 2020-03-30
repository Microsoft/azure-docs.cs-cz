---
title: Jak upgradovat agenta Azure Monitor pro závislost virtuálních počítače
description: Tento článek popisuje, jak upgradovat Agent závislostí Azure Monitor pro virtuální počítače pomocí příkazového řádku, průvodce nastavením a dalších metod.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: c55bee9880c4134f2e304a7fc5176225477fe5f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480755"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>Jak upgradovat agenta Azure Monitor pro závislost virtuálních počítače

Po počátečním nasazení agenta závislostí Azure Monitor pro virtuální počítače jsou vydány aktualizace, které zahrnují opravy chyb nebo podporu nových funkcí nebo funkcí.  Tento článek vám pomůže pochopit metody, které jsou k dispozici a jak provést upgrade ručně nebo prostřednictvím automatizace.

## <a name="upgrade-options"></a>Možnosti upgradu 

Agent závislostí pro Windows a Linux lze upgradovat na nejnovější verzi ručně nebo automaticky v závislosti na scénáři nasazení a prostředí, ve které je počítač spuštěn. Následující metody lze použít k inovaci agenta.

|Prostředí |Způsob instalace |Metoda upgradu |
|------------|--------------------|---------------|
|Virtuální počítač Azure | Rozšíření virtuálního počítače agenta závislostí pro [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) a [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | Agent se automaticky upgraduje ve výchozím nastavení, pokud jste nakonfigurovali šablonu Azure Resource Manager uoptovat nastavením vlastnosti *autoUpgradeMinorVersion* na **false**. Upgrade pro dílčí verzi, kde je zakázán automatický upgrade a hlavní verze upgrade postupujte stejným způsobem - odinstalovat a znovu nainstalovat rozšíření. |
| Image vlastních virtuálních virtuálních počítačů Azure | Ruční instalace agenta závislostí pro Windows/Linux | Aktualizace virtuálních počítačů na nejnovější verzi agenta je třeba provést z příkazového řádku s instalačním balíčkem systému Windows nebo samorozbalovacím a instalovatelným balíčkem skriptů prostředí linux.|
| Virtuální počítače, které nejsou azure, | Ruční instalace agenta závislostí pro Windows/Linux | Aktualizace virtuálních počítačů na nejnovější verzi agenta je třeba provést z příkazového řádku s instalačním balíčkem systému Windows nebo samorozbalovacím a instalovatelným balíčkem skriptů prostředí linux. |

## <a name="upgrade-windows-agent"></a>Upgrade agenta systému Windows 

Chcete-li aktualizovat agenta na virtuálním počítači se systémem Windows na nejnovější verzi, která není nainstalována pomocí rozšíření virtuálního zařízení agenta závislostí, spusťte buď z příkazového řádku, skriptu nebo jiného řešení automatizace, nebo pomocí Průvodce instalací InstallDependencyAgent-Windows.exe.  

Nejnovější verzi agenta windows si můžete stáhnout [zde](https://aka.ms/dependencyagentwindows).

### <a name="using-the-setup-wizard"></a>Použití Průvodce instalací

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Spusťte **instalačního programu InstallDependencyAgent-Windows.exe.**
   
3. Chcete-li odinstalovat předchozí verzi agenta závislostí, odinstalujte předchozí verzi agenta závislostí a nainstalujte nejnovější verzi. **Dependency Agent Setup**


### <a name="from-the-command-line"></a>Z příkazového řádku

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Spusťte následující příkaz.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    Parametr `/RebootMode=manual` zabraňuje upgradu automaticky restartovat počítač, pokud některé procesy používají soubory z předchozí verze a mají zámek na ně. 

3. Chcete-li potvrdit, že `install.log` upgrade byl úspěšný, zkontrolujte podrobné informace o nastavení. Adresář protokolu je *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Upgrade agenta Linuxu 

Upgrade z předchozích verzí agenta závislostí v Systému Linux je podporován a prováděn podle stejného příkazu jako nová instalace.

Nejnovější verzi agenta windows si můžete stáhnout [zde](https://aka.ms/dependencyagentlinux).

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Spusťte následující`sh InstallDependencyAgent-Linux64.bin -s`příkaz jako root . 

Pokud se agentovi závislostí nepodaří spustit, zkontrolujte v protokolech podrobné informace o chybě. U agentů Linuxu je adresář protokolu */var/opt/microsoft/dependency-agent/log*. 

## <a name="next-steps"></a>Další kroky

Pokud chcete po určitou dobu přestat sledovat virtuální počítače nebo úplně odebrat Azure Monitor pro virtuální počítače, přečtěte si část [Zakázat monitorování virtuálních počítačů ve službě Azure Monitor pro virtuální počítače](vminsights-optout.md).
