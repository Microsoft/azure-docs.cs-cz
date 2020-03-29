---
title: Nelze se vzdáleně připojit k virtuálním počítačům Azure, protože se virtuální počítač spustí do nouzového režimu | Dokumenty společnosti Microsoft
description: Přečtěte si, jak vyřešit problém, ve kterém nelze RDP na virtuální ms, protože virtuální ho spouští do nouzového režimu.| Dokumenty společnosti Microsoft
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918202"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Nelze RDP na virtuální ms, protože virtuální ho spouští do nouzového režimu

Tento článek ukazuje, jak vyřešit problém, ve kterém se nemůžete připojit k virtuálním počítačům Azure Windows (VM), protože virtuální počítač je nakonfigurovaný pro spuštění do nouzového režimu.


## <a name="symptoms"></a>Příznaky

Nelze vytvořit připojení RDP nebo jiná připojení (například HTTP) k virtuálnímu počítači v Azure, protože virtuální počítač je nakonfigurovaný pro spuštění do nouzového režimu. Když zkontrolujete snímek obrazovky v [diagnostice spouštění](../troubleshooting/boot-diagnostics.md) na webu Azure Portal, může se zobrazit, že se virtuální počítač spouští normálně, ale síťové rozhraní není dostupné:

![Obrázek o síťovém inferce v nouzovém režimu](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Příčina

Služba RDP není v nouzovém režimu k dispozici. Pouze základní systémové programy a služby jsou načteny při spuštění virtuálního počítače do nouzového režimu. To platí pro dvě různé verze nouzového režimu, které jsou "Safe Boot minimální" a "Safe Boot s připojením".


## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte snímek disku operačního systému ovlivněného virtuálního počítače jako zálohu. Další informace naleznete [v tématu Snímek disku](../windows/snapshot-copy-managed-disk.md).

Chcete-li tento problém vyřešit, použijte sériový ovládací prvek ke konfiguraci virtuálního počítače pro spuštění do normálního režimu nebo [opravu virtuálního počítače offline](#repair-the-vm-offline) pomocí virtuálního počítače pro obnovení.

### <a name="use-serial-control"></a>Použití sériového řízení

1. Připojte se k [konzoli Serial Console a otevřete instanci CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Pokud konzola Serial Console není na vašem virtuálním počítači povolená, přečtěte si informace [o opravě virtuálního počítače offline](#repair-the-vm-offline).
2. Zkontrolujte konfigurační data spouštění:

        bcdedit /enum

    Pokud je virtuální virtuální počítače nakonfigurován pro spuštění do nouzového režimu, zobrazí se další příznak v části **Windows Boot Loader** s názvem **safeboot**. Pokud nevidíte příznak **safeboot,** virtuální ho dispozičně není v nouzovém režimu. Tento článek se nevztahuje na váš scénář.

    Příznak **safeboot** se může zobrazit s následujícími hodnotami:
   - Minimální
   - Network (Síť)

     V jednom z těchto dvou režimů rdp nebude spuštěn. Proto oprava zůstává stejná.

     ![Obrázek příznaku nouzového režimu](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Odstraňte příznak **safemoade,** aby se virtuální bod spustil do normálního režimu:

        bcdedit /deletevalue {current} safeboot

4. Zkontrolujte konfigurační data spouštění a ujistěte se, že je odstraněn příznak **safeboot:**

        bcdedit /enum

5. Restartujte virtuální počítač a zkontrolujte, zda je problém vyřešen.

### <a name="repair-the-vm-offline"></a>Oprava virtuálního virtuálního montovadova offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojení disku operačního systému k virtuálnímu virtuálnímu počítače pro obnovení

1. [Připojte disk operačního systému k virtuálnímu virtuálnímu počítače pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
2. Spusťte připojení vzdálené plochy k virtuálnímu počítači pro obnovení.
3. Zkontrolujte, zda je disk v konzole pro správu disků označen jako **online.** Poznamenejte si písmeno jednotky, které je přiřazeno k připojenému disku operačního systému.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Povolit protokol výpisu a sériovou konzolu (volitelné)

Protokol výpisu a sériová konzola nám pomohou provést další řešení potíží, pokud problém nelze vyřešit řešením v tomto článku.

Chcete-li povolit protokol s výpisem stavu paměti a konzolu Serial Console, spusťte následující skript.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními **(Spustit jako správce**).
2. Spusťte tento skript:

    V tomto skriptu předpokládáme, že písmeno jednotky, které je přiřazeno k připojenému disku operačního systému, je F. Nahraďte toto písmeno jednotky příslušnou hodnotou pro váš virtuální počítač.

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

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Konfigurace spuštění systému Windows do normálního režimu

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními **(Spustit jako správce**).
2. Zkontrolujte konfigurační data spouštění. V následujících příkazech předpokládáme, že písmeno jednotky přiřazené k připojenému disku operačního systému je F. Nahraďte toto písmeno jednotky příslušnou hodnotou pro váš virtuální počítač.

        bcdedit /store F:\boot\bcd /enum
    Poznamenejte si název identifikátoru oddílu, který má složku **\windows.** Ve výchozím nastavení je název identifikátoru "Výchozí".

    Pokud je virtuální virtuální počítače nakonfigurován pro spuštění do nouzového režimu, zobrazí se další příznak v části **Windows Boot Loader** s názvem **safeboot**. Pokud nevidíte příznak **safeboot,** tento článek se nevztahuje na váš scénář.

    ![Obrázek o identifikátoru spouštění](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Odeberte příznak **safeboot,** aby se virtuální montova spustil do normálního režimu:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Zkontrolujte konfigurační data spouštění a ujistěte se, že je odstraněn příznak **safeboot:**

        bcdedit /store F:\boot\bcd /enum
5. [Odpojte disk operačního systému a znovu vytvořte virtuální hod](../windows/troubleshoot-recovery-disks-portal.md). Potom zkontrolujte, zda je problém vyřešen.
