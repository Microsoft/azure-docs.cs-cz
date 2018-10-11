---
title: KRITICKÉ služby se nezdařila při dalším spuštění virtuálního počítače Azure | Dokumentace Microsoftu
description: Zjistěte, jak řešit chyby "FAILED 0x0000005A kritické služby", která proběhne při dalším spuštění | Dokumentace Microsoftu
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: d8140966f3ba8674938a4e21b0990371390d3516
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071142"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Windows zobrazí "Kritické služby se nezdařilo" modré obrazovky při dalším spuštění virtuálního počítače Azure
Tento článek popisuje chybu "Kritické služby se nezdařilo", které můžete narazit při spuštění Windows virtuální počítač (VM) v Microsoft Azure. Poskytuje řešení problémů s kroky pro řešení problémů. 

> [!NOTE] 
> Azure nabízí dva různé modely nasazení pro vytváření a práci s prostředky: [nástroj Resource Manager a klasický režim](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek popisuje pomocí modelu nasazení Resource Manageru, který vám doporučujeme používat pro nová nasazení namísto modelu nasazení classic.

## <a name="symptom"></a>Příznak 

Virtuální počítač Windows nespustí. Když vrátíte se změnami na snímcích obrazovky spouštěcí [Diagnostika spouštění](./boot-diagnostics.md), uvidíte jednu z následujících chybových zpráv na modrá obrazovka:

- "Počítač narazili na problém a vyžaduje restartování. Je možné restartovat. Další informace o tomto problému a je to možné opravy http://windows.com/stopcode. Při volání pracovníci technické podpory, poskytnout jim tyto informace: kód zastavení: kritické služby se nezdařilo " 
- "Počítač narazili na problém a vyžaduje restartování. Právě Shromáždíme některé informace o chybě, a jsme budete restartujte za vás. Pokud se chcete dozvědět víc, můžete hledat online později pro tuto chybu: CRITICAL_SERVICE_FAILED "

## <a name="cause"></a>Příčina

Existují různé příčiny chyby stop. Nejčastější příčiny patří:
- Problém s ovladačem
- Poškozená systémový soubor nebo paměti
- Aplikace přistupuje k zakázané sektoru paměti

## <a name="solution"></a>Řešení 

Chcete-li vyřešit tento problém [obraťte se na podporu a odeslat soubor s výpisem paměti](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), což pomůže nám to problém rychleji diagnostikovat, nebo zkuste následující samoobslužné podpory pro řešení.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojte disk s operačním systémem pro virtuální počítač pro obnovení

1. Pořízení snímku disku s operačním systémem virtuálního počítače ovlivněný jako záložní. Další informace najdete v tématu [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).
2. [Připojte disk s operačním systémem pro virtuální počítač pro obnovení](./troubleshoot-recovery-disks-portal-windows.md). 
3. Navázat připojení ke vzdálené ploše pro virtuální počítač pro obnovení.

### <a name="enable-dump-logs-and-serial-console"></a>Povolit protokoly s výpisem paměti a konzoly sériového portu

V protokolu s výpisem paměti a [konzoly sériového portu](./serial-console-windows.md) pomůže nám to udělat další informace o řešení.

Pokud chcete povolit protokoly s výpisem paměti a konzoly sériového portu, spusťte následující skript.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte tento skript:

    V tomto skriptu předpokládáme, že je písmeno jednotky, která je přiřazena připojeném disku s operačním systémem F. By měl nahradit ho záznamem má hodnotu vhodnou pro váš virtuální počítač.

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

### <a name="replace-the-unsigned-drivers"></a>Nahraďte nepodepsaných ovladačů

1. Na virtuální počítač pro obnovení, spusťte následující příkaz z příkazového řádku se zvýšenými oprávněními. Tento příkaz nastaví postiženém disku operačního systému spustit v nouzovém režimu při příštím spuštění počítače:

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Pokud disk s operačním systémem, který jste připojili je jednotka F, spusťte následující příkaz:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Odpojit disk s operačním systémem a znovu připojte disk s operačním systémem k virtuálnímu počítači ovlivněné](troubleshoot-recovery-disks-portal-windows.md). Virtuální počítač se spustí v nouzovém režimu. Pokud stále dochází k chybě, přejděte [volitelný krok](#optional-analysis-the-dump-logs-in-boot-debug-mode).
3. Otevřít **spustit** pole a spusťte **verifier** spustit nástroj Správce ověřování ovladačů.
4. Vyberte **automaticky vybrat nepodepsaných ovladačů**a potom klikněte na tlačítko **Další**.
5. Zobrazí se seznam soubory ovladačů, které jsou bez znaménka. Mějte na paměti názvy souborů.
6. Zkopírujte tyto soubory stejné verze funkčním virtuálním počítači a nahradit tyto soubory bez znaménka. 

7. Odeberte nastavení bezpečné spuštění:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  Restartujte virtuální počítač. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Volitelné: Analýza protokolů s výpisem paměti ve výpisu stavu systému k chybě režimu

Analyzovat protokoly s výpisem paměti, postupujte podle těchto kroků:

1. Disk s operačním systémem připojte k virtuálnímu počítači pro obnovení.
2. Na disk s operačním systémem, který jste připojili, přejděte do **\windows\system32\config**. Zkopírujte všechny soubory jako záložní v případě, že vrácení zpět je povinný.
3. Spustit **Editor registru** (regedit.exe).
4. Vyberte **HKEY_LOCAL_MACHINE** klíč. V nabídce vyberte **souboru** > **načíst Hive**.
5. Přejděte **\windows\system32\config\SYSTEM** složky na disku s operačním systémem, který jste připojili. Název hive zadejte **BROKENSYSTEM**. Nový registr hive se zobrazuje v části **HKEY_LOCAL_MACHINE** klíč.
6. Přejděte do **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** a proveďte následující změny:

    AutoReboot = 0

    CrashDumpEnabled = 2
7.  Vyberte **BROKENSYSTEM**. V nabídce vyberte **souboru** > **Uvolnit podregistr**.
8.  Upravte nastavení konfiguračních dat spouštění a spusťte v režimu ladění. Spusťte následující příkazy z příkazového řádku se zvýšenými oprávněními:

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
9. [Odpojit disk s operačním systémem a znovu připojte disk s operačním systémem k virtuálnímu počítači ovlivněné](troubleshoot-recovery-disks-portal-windows.md).
10. Spuštění virtuálního počítače chcete zobrazit, pokud se zobrazí analýze výpisu stavu systému. Vyhledejte soubor, který se nepodaří načíst. Je třeba nahradit tento soubor se souborem funkčním virtuálním počítači. 

    Tady je ukázka analýzy výpisu stavu systému. Vidíte, že **selhání** na filecrypt.sys: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

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
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: http://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. Jakmile se virtuální počítač je práce a spustí běžným způsobem, odeberte nastavení výpisu paměti k chybě:

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