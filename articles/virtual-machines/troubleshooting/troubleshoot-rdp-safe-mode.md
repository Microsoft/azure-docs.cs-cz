---
title: Nelze připojit k Azure Virtual Machines vzdálené vzhledem k tomu, že virtuální počítač se spustí v nouzovém režimu | Dokumentace Microsoftu
description: Zjistěte, jak řešit potíže, ve kterém nelze připojení RDP k virtuálnímu počítači, protože virtuální počítač se spustí v nouzovém režimu. | Dokumentace Microsoftu
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 7bc2c0f472a03c3f069a889c360bea9017a780f2
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918202"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Nelze provést připojení RDP k virtuálnímu počítači, protože virtuální počítač se spustí v nouzovém režimu

Tento článek popisuje, jak vyřešit problém, ve kterém nemůže připojit k Azure Windows Virtual Machines (VM), protože virtuální počítač je nakonfigurovaný na spuštění v nouzovém režimu.


## <a name="symptoms"></a>Příznaky

Nemůžete provádět připojení ke vzdálené ploše nebo jiné připojení (např. HTTP) k virtuálnímu počítači v Azure vzhledem k tomu, že virtuální počítač je nakonfigurovaný na spuštění v nouzovém režimu. Když zkontrolujete snímek obrazovky v [diagnostice spouštění](../troubleshooting/boot-diagnostics.md) v Azure Portal, může se stát, že se virtuální počítač spouští normálně, ale síťové rozhraní není k dispozici:

![Obrázek o inferce sítě v nouzovém režimu](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Příčina

Služba protokolu RDP není k dispozici v nouzovém režimu. Pouze základní systém programů a služeb se načtou při virtuální počítač se spustí v nouzovém režimu. To platí pro dvě různé verze nouzovém režimu, které jsou "Bezpečné spuštění minimální" a "Bezpečné spuštění připojení".


## <a name="solution"></a>Řešení

Předtím, než budete postupovat podle těchto kroků, vytvořte snímek disku s operačním systémem virtuálního počítače ovlivněný jako záložní. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

Pokud chcete tento problém vyřešit, nakonfigurujte pomocí sériového řízení virtuální počítač tak, aby se spouštěl do normálního režimu, nebo [opravte virtuální počítač offline](#repair-the-vm-offline) pomocí virtuálního počítače pro obnovení.

### <a name="use-serial-control"></a>Použití sériového portu ovládacího prvku

1. Připojte se ke [konzole sériového prostředí a otevřete instanci cmd](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Pokud na vašem VIRTUÁLNÍm počítači není povolena sériová konzola, přečtěte si téma [Oprava virtuálního počítače v režimu offline](#repair-the-vm-offline).
2. Kontrola konfiguračních dat spouštění:

        bcdedit /enum

    Pokud je virtuální počítač nakonfigurovaný tak, aby se spouštěl do nouzového režimu, zobrazí se v části **zaváděcí program pro spouštění systému Windows** s názvem **safeboot**další příznak. Pokud nevidíte příznak **safeboot** , virtuální počítač není v bezpečném režimu. Tento článek se nevztahuje na váš scénář.

    Příznak **safeboot** se může zobrazit s následujícími hodnotami:
   - Minimální
   - Síť

     V některém z těchto dvou režimech nebude spuštěno protokolu RDP. Proto oprava zůstává stejná.

     ![Obrázek o příznak Nouzový režim](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Odstraňte příznak **safemoade** , aby se virtuální počítač spouštěl v normálním režimu:

        bcdedit /deletevalue {current} safeboot

4. Zkontrolujte konfigurační data spouštění a ujistěte se, že je odstraněný příznak **safeboot** :

        bcdedit /enum

5. Restartujte virtuální počítač a potom zkontrolujte, zda byl problém vyřešen.

### <a name="repair-the-vm-offline"></a>Opravte virtuální počítač v režimu offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojte disk s operačním systémem pro virtuální počítač pro obnovení

1. [Připojte disk s operačním systémem k virtuálnímu počítači pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
2. Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení.
3. Ujistěte se, že je disk označen jako **online** v konzole pro správu disků. Poznamenejte si písmeno jednotky, která je přiřazena připojeném disku s operačním systémem.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Povolení protokolu výpisu stavu systému a konzoly sériového portu (volitelné)

Protokol s výpisem paměti a konzoly sériového portu nám pomůže udělat další řešení potíží s Pokud potíže nelze vyřešit pomocí řešení v tomto článku.

Pokud chcete povolit protokol s výpisem paměti a konzoly sériového portu, spusťte následující skript.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (**Spustit jako správce**).
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

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Nakonfigurujte systém Windows tak, aby se spouštěl do normálního režimu.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (**Spustit jako správce**).
2. Ověřte konfigurační data spouštění. V následujících příkazech předpokládáme, že písmeno jednotky přiřazené k připojenému disku s operačním systémem je F. nahraďte toto písmeno jednotky odpovídající hodnotou pro váš virtuální počítač.

        bcdedit /store F:\boot\bcd /enum
    Poznamenejte si název identifikátoru oddílu, který má složku **\Windows** . Ve výchozím nastavení je název identifikátoru "výchozí".

    Pokud je virtuální počítač nakonfigurovaný tak, aby se spouštěl do nouzového režimu, zobrazí se v části **zaváděcí program pro spouštění systému Windows** s názvem **safeboot**další příznak. Pokud nevidíte příznak **safeboot** , Tento článek se nevztahuje na váš scénář.

    ![Obrázek o identifikátoru spouštění](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Odeberte příznak **safeboot** , aby se virtuální počítač spouštěl v normálním režimu:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Zkontrolujte konfigurační data spouštění a ujistěte se, že je odstraněný příznak **safeboot** :

        bcdedit /store F:\boot\bcd /enum
5. [Odpojte disk s operačním systémem a znovu vytvořte virtuální počítač](../windows/troubleshoot-recovery-disks-portal.md). Potom zkontrolujte, zda byl problém vyřešen.
