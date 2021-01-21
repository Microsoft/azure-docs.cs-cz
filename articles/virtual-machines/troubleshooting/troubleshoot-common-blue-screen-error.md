---
title: Při spouštění virtuálního počítače Azure došlo k chybám modré obrazovky | Microsoft Docs
description: Naučte se, jak řešit potíže, při kterých se při spouštění zobrazila chyba modré obrazovky | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: 9a95ddf882e5edba9daa8ff91c02d1df1f50bceb
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632972"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Při spouštění virtuálního počítače Azure zobrazuje Windows chybou modrou obrazovku
Tento článek popisuje chyby modré obrazovky, se kterými se můžete setkat při spuštění virtuálního počítače s Windows v Microsoft Azure. Poskytuje kroky, které vám pomůžou shromáždit data pro lístek podpory. 


## <a name="symptom"></a>Příznak 

Virtuální počítač s Windows se nespustí. Když zkontrolujete spouštěcí snímky obrazovky v [diagnostice spouštění](./boot-diagnostics.md), zobrazí se některá z následujících chybových zpráv na modré obrazovce:

- náš počítač narazil na problém a je potřeba ho restartovat. Jenom shromažďujeme nějaké informace o chybách a pak se můžete restartovat.
- Váš počítač narazil na problém a je potřeba ho restartovat.

V této části jsou uvedené běžné chybové zprávy, se kterými se můžete setkat při správě virtuálních počítačů:

## <a name="cause"></a>Příčina

Může to být několik důvodů, proč by se vám zobrazila chyba Stop. Mezi nejběžnější příčiny patří:

- Problém s ovladačem
- Poškozený systémový soubor nebo paměť
- Aplikace přistupuje ke zakázanému sektoru paměti.

## <a name="collect-memory-dump-file"></a>Shromáždit soubor s výpisem paměti

> [!TIP]
> Pokud máte nedávno zálohovaný virtuální počítač, můžete zkusit [obnovit virtuální počítač ze zálohy](../../backup/backup-azure-arm-restore-vms.md) a opravit problém při spouštění.

Chcete-li tento problém vyřešit, je třeba nejprve shromáždit soubor s výpisem paměti pro chybu a kontaktovat podporu se souborem s výpisem paměti. Chcete-li shromáždit soubor s výpisem paměti, postupujte podle následujících kroků:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojení disku s operačním systémem k virtuálnímu počítači pro obnovení

1. Pořídit snímek disku s operačním systémem ovlivněného virtuálního počítače jako zálohy. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).
2. [Připojte disk s operačním systémem k virtuálnímu počítači pro obnovení](./troubleshoot-recovery-disks-portal-windows.md). 
3. Vzdálená plocha do virtuálního počítače pro obnovení.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Vyhledat soubor s výpisem paměti a odeslat lístek podpory

1. Na virtuálním počítači pro obnovení otevřete složku Windows na připojeném disku s operačním systémem. Pokud je písmeno přiřazené k připojenému disku s operačním systémem F, je nutné přejít na F:\Windows.
2. Vyhledejte soubor Memory. dmp a pak [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem paměti. 

Pokud soubor s výpisem paměti nemůžete najít, přejděte k dalšímu kroku a Povolte protokol výpisu a sériovou konzolu.

### <a name="enable-dump-log-and-serial-console"></a>Povolit protokol výpisu paměti a sériová konzola

Pokud chcete povolit protokol výpisu a sériovou konzolu, spusťte následující skript.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte tento skript:

    V tomto skriptu předpokládáme, že písmeno jednotky přiřazené k připojenému disku s operačním systémem je F.  Nahraďte ji odpovídající hodnotou ve vašem VIRTUÁLNÍm počítači.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

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

    1. Ujistěte se, že na disku je dostatek místa pro přidělení tolika paměti jako paměti RAM, což závisí na velikosti, kterou vybíráte pro tento virtuální počítač.
    2. Pokud není dostatek místa nebo se jedná o virtuální počítač velké velikosti (G, GS nebo E-series), můžete změnit umístění, kde se tento soubor vytvoří, a odkazovat na jakýkoli jiný datový disk, který je připojený k virtuálnímu počítači. K tomu budete muset změnit následující klíč:

    ```config-reg
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

    reg unload HKLM\BROKENSYSTEM
    ```

3. [Odpojte disk s operačním systémem a pak znovu připojte disk s operačním systémem k ovlivněnému virtuálnímu počítači](./troubleshoot-recovery-disks-portal-windows.md).
4. Spusťte virtuální počítač, aby se problém reprodukoval, a pak se vygeneruje soubor s výpisem paměti.
5. Připojte disk s operačním systémem k virtuálnímu počítači pro obnovení, shromážděte soubor výpisu paměti a pak [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem paměti.
