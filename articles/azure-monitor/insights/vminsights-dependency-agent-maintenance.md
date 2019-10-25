---
title: Postup upgradu agenta závislostí Azure Monitor pro virtuální počítače | Microsoft Docs
description: Tento článek popisuje, jak upgradovat agenta Azure Monitor pro virtuální počítače závislostí pomocí příkazového řádku, Průvodce instalací a dalších metod.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/30/2019
ms.author: magoedte
ms.openlocfilehash: 37dcd21e17a47827f9bd4f6e5a756b321cf94495
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809835"
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

Chcete-li aktualizovat agenta na virtuálním počítači s Windows na nejnovější verzi, která není nainstalovaná pomocí rozšíření virtuálního počítače agenta závislostí, buď spusťte z příkazového řádku, skriptu nebo jiného řešení automatizace, nebo pomocí Průvodce instalací InstallDependencyAgent-Windows. exe.  

Nejnovější verzi agenta pro Windows si můžete stáhnout [tady](https://aka.ms/dependencyagentwindows).

### <a name="using-the-setup-wizard"></a>Pomocí Průvodce instalací

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Spusťte **InstallDependencyAgent-Windows. exe** a spusťte Průvodce instalací nástroje.

3. V dialogovém okně **dependency agent nastavení 9.9.1** **klikněte na Souhlasím a** přijměte licenční smlouvu.

5. V dialogovém okně **dependency agent odinstalace 9.9.0** klikněte na **Další**. Na stránce stav se zobrazuje průběh odinstalace předchozí verze.

6. V dialogovém okně **dependency agent odinstalace 9.9.0** klikněte na **odinstalovat** , abyste mohli pokračovat v odinstalaci předchozí verze z cesty zadané v dialogovém okně. 

7. V dialogovém okně **dependency agent odinstalace 9.9.0** se zobrazí průběh odinstalace a po dokončení se zobrazí stránka **dokončení odinstalace Dependency agent** . Klikněte na **Finish** (Dokončit).

8. V dialogovém okně **dependency Agent instalace 9.9.1** se zobrazí průběh instalace. Po zobrazení stránky **dokončení odinstalace Dependency agent** klikněte na **Dokončit**. 

### <a name="from-the-command-line"></a>Z příkazového řádku

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Spusťte následující příkaz.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    Parametr `/RebootMode=manual` brání upgradu v automatickém restartování počítače, pokud některé procesy používají soubory z předchozí verze a mají pro ně zámek. 

3. Chcete-li ověřit, zda upgrade proběhl úspěšně, zkontrolujte `install.log` podrobnější informace o nastavení. Adresář protokolu je *%ProgramFiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Upgrade agenta pro Linux 

Upgrade z předchozích verzí agenta závislostí v systému Linux je podporován a proveden podle stejného příkazu jako nová instalace.

Nejnovější verzi agenta pro Windows si můžete stáhnout [tady](https://aka.ms/dependencyagentlinux).

1. Přihlaste se k počítači pomocí účtu, který má práva správce.

2. Spusťte následující příkaz jako kořenový`sh InstallDependencyAgent-Linux64.bin -s`. 

Pokud se nepovede spustit agenta závislostí, podrobnější informace o chybě najdete v protokolech. V agentech Linux se adresář protokolu */var/opt/Microsoft/Dependency-agent/log*. 

## <a name="next-steps"></a>Další kroky

Pokud chcete zastavit monitorování virtuálních počítačů po určitou dobu nebo Azure Monitor pro virtuální počítače zcela odebrat, přečtěte si téma [zakázání monitorování virtuálních počítačů v Azure monitor pro virtuální počítače](vminsights-optout.md).