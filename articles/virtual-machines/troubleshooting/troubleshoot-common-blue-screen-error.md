---
title: Chyby s modrou obrazovkou při zavádění virtuálního počítače Azure| Dokumenty společnosti Microsoft
description: Zjistěte, jak vyřešit problém, že při spuštění došlo k chybě s modrou obrazovkou| Dokumenty společnosti Microsoft
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
ms.openlocfilehash: beb1562738699bbcede58d8214e69342abbb7c93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266933"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows zobrazuje chybu s modrou obrazovkou při zavádění virtuálního počítače Azure
Tento článek popisuje chyby s modrou obrazovkou, se kterými se můžete setkat při spuštění virtuálního počítače (VM) Windows v Microsoft Azure. Poskytuje kroky, které vám pomohou shromažďovat data pro lístek podpory. 


## <a name="symptom"></a>Příznak 

Virtuální ho virtuálního mísa systému Windows se nespustí. Když v [diagnostice spouštění](./boot-diagnostics.md)spustíte spouštěcí snímky obrazovky , zobrazí se na modré obrazovce jedna z následujících chybových zpráv:

- náš počítač narazil na problém a je třeba restartovat. Právě shromažďujeme nějaké informace o chybách a pak můžete restartovat.
- Váš počítač narazil na problém a je třeba restartovat.

V této části jsou uvedeny běžné chybové zprávy, se kterými se můžete setkat při správě virtuálních měn:

## <a name="cause"></a>Příčina

Může existovat několik důvodů, proč byste se dostali k chybě stop. Mezi nejčastější příčiny patří:

- Problém s ovladačem
- Poškozený systémový soubor nebo paměť
- Aplikace přistupuje k zakázanému sektoru paměti

## <a name="collect-memory-dump-file"></a>Sbírat soubor výpisu stavu paměti

Chcete-li tento problém vyřešit, budete muset nejprve shromáždit soubor s výpisem stavu systému pro selhání a obraťte se na podporu s souborem s výpisem stavu systému. Chcete-li soubor s výpisem stavu paměti shromáždit, postupujte takto:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojení disku operačního systému k virtuálnímu virtuálnímu počítače pro obnovení

1. Pořiďte snímek disku operačního systému ovlivněného virtuálního počítače jako zálohu. Další informace naleznete [v tématu Snímek disku](../windows/snapshot-copy-managed-disk.md).
2. [Připojte disk operačního systému k virtuálnímu virtuálnímu počítače pro obnovení](../windows/troubleshoot-recovery-disks-portal.md). 
3. Vzdálená plocha k virtuálnímu počítači pro obnovení.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Vyhledání souboru s výpisem stavu paměti a odeslání lístku podpory

1. Na virtuálním počítači pro obnovení přejděte do složky Windows na připojeném disku operačního systému. Pokud je písmeno ovladače přiřazené k připojenému disku operačního systému F, musíte přejít na f:\Windows.
2. Vyhledejte soubor memory.dmp a [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem stavu paměti. 

Pokud nemůžete najít soubor s výpisem stavu paměti, přesuňte další krok a povolte protokol výpisu a konzolu Serial Console.

### <a name="enable-dump-log-and-serial-console"></a>Povolení protokolu výpisu a sériové konzoly

Chcete-li povolit protokol s výpisem stavu paměti a konzolu Serial Console, spusťte následující skript.

1. Otevřete relaci příkazu Se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte tento skript:

    V tomto skriptu předpokládáme, že písmeno jednotky, které je přiřazeno k připojenému disku operačního systému, je F.  Nahraďte ji příslušnou hodnotou ve virtuálním počítači.

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

    1. Ujistěte se, že je dostatek místa na disku přidělit tolik paměti jako RAM, což závisí na velikosti, kterou vybíráte pro tento virtuální počítač.
    2. Pokud není dostatek místa nebo je to velký virtuální počítače (G, GS nebo E řady), můžete pak změnit umístění, kde bude vytvořen tento soubor a odkazovat, že na jakýkoli jiný datový disk, který je připojen k virtuálnímu počítače. Chcete-li to provést, budete muset změnit následující klíč:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Odpojte disk operačního systému a potom znovu připojte disk operačního systému k ovlivněnému virtuálnímu počítače](../windows/troubleshoot-recovery-disks-portal.md).
4. Spusťte virtuální hod reprodukovat problém, pak bude generován soubor s výpisem stavu paměti.
5. Připojte disk operačního systému k virtuálnímu virtuálnímu počítače pro obnovení, shromažďujte soubor s výpisem stavu paměti a [potom odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem stavu paměti.



