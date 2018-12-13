---
title: Nelze připojit k Azure Virtual Machines vzdálené vzhledem k tomu, že virtuální počítač se spustí v nouzovém režimu | Dokumentace Microsoftu
description: Zjistěte, jak řešit potíže, ve kterém nelze připojení RDP k virtuálnímu počítači, protože virtuální počítač se spustí v nouzovém režimu. | Dokumentace Microsoftu
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 0ef4aa988f4adc855051b213013636b4a04f1cca
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316967"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Nelze provést připojení RDP k virtuálnímu počítači, protože virtuální počítač se spustí v nouzovém režimu

Tento článek popisuje, jak vyřešit problém, ve kterém nemůže připojit k Azure Windows Virtual Machines (VM), protože virtuální počítač je nakonfigurovaný na spuštění v nouzovém režimu.

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a classic](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Resource Manageru, který vám doporučujeme používat pro nová nasazení namísto modelu nasazení classic.

## <a name="symptoms"></a>Příznaky

Nemůžete provádět připojení ke vzdálené ploše nebo jiné připojení (např. HTTP) k virtuálnímu počítači v Azure vzhledem k tomu, že virtuální počítač je nakonfigurovaný na spuštění v nouzovém režimu. Když vrátíte se změnami na snímku obrazovky [Diagnostika spouštění](../troubleshooting/boot-diagnostics.md) na webu Azure Portal, můžete všimnout, že virtuální počítač se spustí normálně, ale není k dispozici síťové rozhraní:

![Obrázek o inferce sítě v nouzovém režimu](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Příčina

Služba protokolu RDP není k dispozici v nouzovém režimu. Pouze základní systém programů a služeb se načtou při virtuální počítač se spustí v nouzovém režimu. To platí pro dvě různé verze nouzovém režimu, které jsou "Bezpečné spuštění minimální" a "Bezpečné spuštění připojení".


## <a name="solution"></a>Řešení

Předtím, než budete postupovat podle těchto kroků, vytvořte snímek disku s operačním systémem virtuálního počítače ovlivněný jako záložní. Další informace najdete v tématu [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).

Pokud chcete tento problém vyřešit, pomocí sériového portu ovládací prvek nakonfigurovat virtuální počítač spustí v normálním režimu nebo [opravte virtuální počítač v režimu offline](#repair-the-vm-offline) pomocí virtuální počítač pro obnovení.

### <a name="use-serial-control"></a>Použití sériového portu ovládacího prvku

1. Připojte se k [sériové konzoly a otevřené instance CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Pokud konzole sériového portu není povolená na virtuálním počítači, přečtěte si téma [opravte virtuální počítač v režimu offline](#repair-the-vm-offline).
2. Kontrola konfiguračních dat spouštění:

        bcdedit /enum

    Pokud virtuální počítač je nakonfigurovaný pro spuštění v nouzovém režimu, zobrazí se další příznak v části **Windows spouštěcí zavaděč** část s názvem **bezpečného**. Pokud se nezobrazí **bezpečného** příznak, virtuální počítač není v nouzovém režimu. Tento článek se nevztahuje na váš scénář.

    **Bezpečného** příznak může zobrazit následující hodnoty:
    - Minimální
    - Síť

    V některém z těchto dvou režimech nebude spuštěno protokolu RDP. Proto oprava zůstává stejná.

    ![Obrázek o příznak Nouzový režim](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Odstranit **safemoade** příznak, tak virtuální počítač se spustí v normálním režimu:

        bcdedit /deletevalue {current} safeboot

4. Zkontrolujte, že konfigurační data spouštění **bezpečného** odebrán příznak:

        bcdedit /enum

5. Restartujte virtuální počítač a potom zkontrolujte, zda byl problém vyřešen.

### <a name="repair-the-vm-offline"></a>Opravte virtuální počítač v režimu offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojte disk s operačním systémem pro virtuální počítač pro obnovení

1. [Připojte disk s operačním systémem pro virtuální počítač pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
2. Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení.
3. Ujistěte se, že disk je označený jako **Online** v konzole Správa disků. Poznamenejte si písmeno jednotky, která je přiřazena připojeném disku s operačním systémem.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Povolení protokolu výpisu stavu systému a konzoly sériového portu (volitelné)

Protokol s výpisem paměti a konzoly sériového portu nám pomůže udělat další řešení potíží s Pokud potíže nelze vyřešit pomocí řešení v tomto článku.

Pokud chcete povolit protokol s výpisem paměti a konzoly sériového portu, spusťte následující skript.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (**spustit jako správce**).
2. Spusťte tento skript:

    V tomto skriptu předpokládáme, že je písmeno jednotky, která je přiřazena připojeném disku s operačním systémem F. nahradit toto písmeno jednotky s odpovídající hodnotou pro váš virtuální počítač.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Konfigurace Windows pro spuštění v normálním režimu.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (**spustit jako správce**).
2. Zkontrolujte konfigurační data spouštění. V následujících příkazech předpokládáme, že je písmeno jednotky, která je přiřazena připojeném disku s operačním systémem F. nahradit toto písmeno jednotky s odpovídající hodnotou pro váš virtuální počítač.

        bcdedit /store F:\boot\bcd /enum
    Poznamenejte si název identifikátoru oddílu, který má **\windows** složky. Ve výchozím nastavení je název identifikátoru "Výchozí".

    Pokud virtuální počítač je nakonfigurovaný pro spuštění v nouzovém režimu, zobrazí se další příznak v části **Windows spouštěcí zavaděč** část s názvem **bezpečného**. Pokud se nezobrazí **bezpečného** příznak, v tomto článku se nedá použít pro váš scénář.

    ![Obrázek o spouštěcí identifikátor](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Odeberte **bezpečného** příznak, tak virtuální počítač se spustí v normálním režimu:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Zkontrolujte, že konfigurační data spouštění **bezpečného** odebrán příznak:

        bcdedit /store F:\boot\bcd /enum
5. [Odpojit disk s operačním systémem a znovu vytvořte virtuální počítač](../windows/troubleshoot-recovery-disks-portal.md). Zkontrolujte, zda je problém vyřešen.
