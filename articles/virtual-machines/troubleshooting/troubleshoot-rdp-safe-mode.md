---
title: Nejde se připojit k Virtual Machines Azure vzdáleně, protože virtuální počítač se spouští v nouzovém režimu | Microsoft Docs
description: Naučte se řešit potíže, ve kterých se nedaří připojit k virtuálnímu počítači pomocí protokolu RDP, protože virtuální počítač se spouští v nouzovém režimu. | Microsoft Docs
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
ms.openlocfilehash: d424cccc0a50198f3ca8c6c040afb87f44282d47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86508895"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Nejde se připojit k virtuálnímu počítači, protože virtuální počítač se spouští do nouzového režimu.

Tento článek popisuje, jak vyřešit problém, ve kterém se nemůžete připojit k Azure Windows Virtual Machines (virtuální počítače), protože virtuální počítač je nakonfigurovaný tak, aby se spouštěl do nouzového režimu.


## <a name="symptoms"></a>Příznaky

K virtuálnímu počítači v Azure nemůžete vytvořit připojení RDP ani jiná připojení (například HTTP), protože virtuální počítač je nakonfigurovaný tak, aby se spouštěl do nouzového režimu. Když zkontrolujete snímek obrazovky v [diagnostice spouštění](../troubleshooting/boot-diagnostics.md) v Azure Portal, může se stát, že se virtuální počítač spouští normálně, ale síťové rozhraní není k dispozici:

![Obrázek o síťovém inferce v bezpečném režimu](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Příčina

Služba RDP není v bezpečném režimu k dispozici. Když se virtuální počítač spustí v nouzovém režimu, načtou se jenom důležité systémové programy a služby. To platí pro dvě různé verze bezpečného režimu, které jsou "bezpečné spuštění" minimální "a" bezpečné spuštění s připojením ".


## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte si snímek disku s operačním systémem ovlivněného virtuálního počítače jako záložního. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

Pokud chcete tento problém vyřešit, nakonfigurujte pomocí sériového řízení virtuální počítač tak, aby se spouštěl do normálního režimu, nebo [opravte virtuální počítač offline](#repair-the-vm-offline) pomocí virtuálního počítače pro obnovení.

### <a name="use-serial-control"></a>Použití ovládacího prvku sériového portu

1. Připojte se ke [konzole sériového prostředí a otevřete instanci cmd](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Pokud na vašem VIRTUÁLNÍm počítači není povolena sériová konzola, přečtěte si téma [Oprava virtuálního počítače v režimu offline](#repair-the-vm-offline).
2. Ověřte konfigurační data spouštění:

    ```console
    bcdedit /enum
    ```

    Pokud je virtuální počítač nakonfigurovaný tak, aby se spouštěl do nouzového režimu, zobrazí se v části **zaváděcí program pro spouštění systému Windows** s názvem **safeboot**další příznak. Pokud nevidíte příznak **safeboot** , virtuální počítač není v bezpečném režimu. Tento článek se nevztahuje na váš scénář.

    Příznak **safeboot** se může zobrazit s následujícími hodnotami:
   - Minimální
   - Síť

     V obou těchto dvou režimech se protokol RDP nespustí. Oprava proto zůstane stejná.

     ![Obrázek s příznakem bezpečného režimu](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Odstraňte příznak **safemoade** , aby se virtuální počítač spouštěl v normálním režimu:

    ```console
    bcdedit /deletevalue {current} safeboot
    ```

4. Zkontrolujte konfigurační data spouštění a ujistěte se, že je odstraněný příznak **safeboot** :

    ```console
    bcdedit /enum
    ```

5. Restartujte virtuální počítač a potom zkontrolujte, jestli je problém vyřešený.

### <a name="repair-the-vm-offline"></a>Oprava virtuálního počítače v režimu offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojení disku s operačním systémem k virtuálnímu počítači pro obnovení

1. [Připojte disk s operačním systémem k virtuálnímu počítači pro obnovení](./troubleshoot-recovery-disks-portal-windows.md).
2. Spusťte připojení ke vzdálené ploše virtuálního počítače pro obnovení.
3. Ujistěte se, že je disk označen jako **online** v konzole pro správu disků. Poznamenejte si písmeno jednotky přiřazené k připojenému disku s operačním systémem.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Povolit protokol výpisu a sériovou konzolu (volitelné)

Protokol výpisu a sériová konzola vám pomůže s dalšími řešeními potíží, pokud problém nepůjde vyřešit řešením v tomto článku.

Pokud chcete povolit protokol výpisu a sériovou konzolu, spusťte následující skript.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (**Spustit jako správce**).
2. Spusťte tento skript:

    V tomto skriptu předpokládáme, že písmeno jednotky přiřazené k připojenému disku s operačním systémem je F. nahraďte toto písmeno jednotky odpovídající hodnotou pro váš virtuální počítač.

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

    ```console
    bcdedit /store F:\boot\bcd /enum
    ```

    Poznamenejte si název identifikátoru oddílu, který má složku **\Windows** . Ve výchozím nastavení je název identifikátoru "výchozí".

    Pokud je virtuální počítač nakonfigurovaný tak, aby se spouštěl do nouzového režimu, zobrazí se v části **zaváděcí program pro spouštění systému Windows** s názvem **safeboot**další příznak. Pokud nevidíte příznak **safeboot** , Tento článek se nevztahuje na váš scénář.

    ![Obrázek o identifikátoru spouštění](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Odeberte příznak **safeboot** , aby se virtuální počítač spouštěl v normálním režimu:

    ```console
    bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
    ```

4. Zkontrolujte konfigurační data spouštění a ujistěte se, že je odstraněný příznak **safeboot** :

    ```console
    bcdedit /store F:\boot\bcd /enum
    ```

5. [Odpojte disk s operačním systémem a znovu vytvořte virtuální počítač](./troubleshoot-recovery-disks-portal-windows.md). Potom zkontrolujte, zda byl problém vyřešen.
