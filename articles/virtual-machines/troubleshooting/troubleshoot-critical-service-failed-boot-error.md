---
title: KRITICKá služba se nezdařila při spouštění virtuálního počítače Azure | Microsoft Docs
description: Přečtěte si, jak řešit potíže s chybou "0x0000005A-CRITICAL SERVICE FAILed", ke kterému dochází při spouštění | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: a937528e3bfd8bea16912d614133988763748bab
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632955"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Při spuštění virtuálního počítače Azure se v systému Windows zobrazí modrá obrazovka "KRITICKá služba neúspěšná"
Tento článek popisuje chybu "KRITICKá služba neúspěšná", která se může vyskytnout při spuštění virtuálního počítače s Windows v Microsoft Azure. Poskytuje kroky pro řešení potíží, které vám pomůžou tyto problémy vyřešit. 


## <a name="symptom"></a>Příznak 

Virtuální počítač s Windows se nespustí. Když zkontrolujete spouštěcí snímky obrazovky v [diagnostice spouštění](./boot-diagnostics.md), zobrazí se na modré obrazovce jedna z následujících chybových zpráv:

- "Váš počítač byl příčinou problému a je nutné ho restartovat. Můžete restartovat. Další informace o tomto problému a možných opravách najdete na webu https://windows.com/stopcode . Pokud zavoláte osobu podpory, poskytněte jim tyto informace: Stop kód: KRITICKá služba se nezdařila. " 
- "Váš počítač byl příčinou problému a je nutné ho restartovat. Jenom shromažďujeme nějaké informace o chybách a pak za vás budeme restartovat. Pokud se chcete dozvědět víc, můžete k této chybě vyhledat online později: CRITICAL_SERVICE_FAILED "

## <a name="cause"></a>Příčina

Chyby Stop mají různé příčiny. Mezi nejběžnější příčiny patří:
- Problém s ovladačem
- Poškozený systémový soubor nebo paměť
- Aplikace přistupuje k zakázanému sektoru paměti.

## <a name="solution"></a>Řešení 

> [!TIP]
> Pokud máte nedávno zálohovaný virtuální počítač, můžete zkusit [obnovit virtuální počítač ze zálohy](../../backup/backup-azure-arm-restore-vms.md) a opravit problém při spouštění.

Pokud chcete tento problém vyřešit, obraťte se na [podporu a odešlete soubor s výpisem paměti](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), který nám pomůže rychle diagnostikovat problém nebo vyzkoušet následující řešení pro samoobslužnou pomoc.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojení disku s operačním systémem k virtuálnímu počítači pro obnovení

1. Pořídit snímek disku s operačním systémem ovlivněného virtuálního počítače jako zálohy. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).
2. [Připojte disk s operačním systémem k virtuálnímu počítači pro obnovení](./troubleshoot-recovery-disks-portal-windows.md). 
3. Navažte připojení ke vzdálené ploše virtuálního počítače pro obnovení.

### <a name="enable-dump-logs-and-serial-console"></a>Povolit protokoly výpisu paměti a sériová konzola

Protokol výpisu paměti a [sériová konzola](./serial-console-windows.md) nám pomůžou provést další řešení potíží.

Pokud chcete povolit protokoly výpisu paměti a sériovou konzolu, spusťte následující skript.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte tento skript:

    V tomto skriptu předpokládáme, že písmeno jednotky přiřazené k připojenému disku s operačním systémem je F. Měli byste ho nahradit odpovídající hodnotou pro váš virtuální počítač.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>Výměna nepodepsaných ovladačů

1. Na virtuálním počítači pro obnovení spusťte z příkazového řádku se zvýšenými oprávněními následující příkaz. Tento příkaz nastaví ovlivněný disk s operačním systémem na spuštění v nouzovém režimu při příštím spuštění:

    ```console
    bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal
    ```

    Pokud je například disk s operačním systémem, který jste připojili, jednotku F, spusťte následující příkaz:

    ```console
    bcdedit /store F: boot\bcd /set {default} safeboot minimal
    ```

2. [Odpojte disk s operačním systémem a pak znovu připojte disk s operačním systémem k ovlivněnému virtuálnímu počítači](troubleshoot-recovery-disks-portal-windows.md). Virtuální počítač se spustí v bezpečném režimu. Pokud stále dochází k chybě, Projděte si volitelný krok.
3. Otevřete okno **Run** a spusťte **Ověřovač** a spusťte nástroj Správce ověřovače ovladačů.
4. Vyberte možnost **automaticky vybrat nepodepsané ovladače** a pak klikněte na tlačítko **Další**.
5. Zobrazí se seznam nepodepsaných souborů ovladačů. Zapamatujte si názvy souborů.
6. Zkopírujte stejné verze těchto souborů z pracovního virtuálního počítače a pak tyto nepodepsané soubory nahraďte. 

7. Odeberte nastavení bezpečného spuštění:

    ```console
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
    ```

8.  Restartujte virtuální počítač. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Volitelné: Analýza protokolů výpisu paměti v režimu zotavení po havárii

Pokud chcete protokoly výpisu paměti analyzovat sami, postupujte takto:

1. Disk s operačním systémem připojte k virtuálnímu počítači pro obnovení.
2. Na disku s operačním systémem, který jste připojili, přejděte na **\Windows\System32\Config**. Zkopírujte všechny soubory jako zálohu pro případ, že je vyžadováno vrácení zpět.
3. Spusťte **Editor registru** (regedit.exe).
4. Vyberte **HKEY_LOCAL_MACHINE** klíč. V nabídce vyberte položku   >  **podregistr Load** File.
5. Přejděte do složky **\windows\system32\config\SYSTEM** na disku s operačním systémem, který jste připojili. Jako název podregistru zadejte **BROKENSYSTEM**. Nový podregistr registru se zobrazí pod klíčem **HKEY_LOCAL_MACHINE** .
6. Přejděte na **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** a proveďte následující změny:

    AutoRestart = 0

    CrashDumpEnabled = 2
7.  Vyberte **BROKENSYSTEM**. V nabídce vyberte **soubor**  >  **Uvolnit podregistr**.
8.  Upravte nastavení BCD tak, aby se spouštěla do režimu ladění. Na příkazovém řádku se zvýšenými oprávněními spusťte následující příkazy:

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [Odpojte disk s operačním systémem a pak znovu připojte disk s operačním systémem k ovlivněnému virtuálnímu počítači](troubleshoot-recovery-disks-portal-windows.md).
10. Spusťte virtuální počítač a zjistěte, jestli zobrazuje analýzu výpisu paměti. Vyhledejte soubor, který se nepodařilo načíst. Tento soubor je potřeba nahradit souborem z pracovního virtuálního počítače. 

    Následuje ukázka analýzy výpisu paměti. Můžete vidět, že **Chyba** je v filecrypt.sys: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. Jakmile virtuální počítač pracuje a normálně se spouští, odeberte nastavení chyby výpisu paměti.

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
