---
title: Vypnutí virtuálních počítačů Azure se zaseklo při restartování, vypnutí nebo zastavení služeb | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže vyřešit chyby služby ve virtuálních počítačích Azure Windows.
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: 5d6396efc9ab25baa0d32e7c33c7715863516249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371356"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Vypnutí virtuálního počítače Azure windows se zaseklo na "Restartování", "Vypnutí" nebo "Zastavení služeb"

Tento článek obsahuje kroky k vyřešení problémů "Restartování", "Vypnutí" nebo "Zastavení služeb" zprávy, které se mohou vyskytnou při restartování virtuálního počítače (VM) Windows v Microsoft Azure.

## <a name="symptoms"></a>Příznaky

Při použití [boot diagnostiky](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) k zobrazení snímku obrazovky virtuálního virtuálního soudu, můžete vidět, že snímek obrazovky se zobrazí zpráva "Restartování", "Vypnutí" nebo "Zastavení služby".

![Restartování, vypnutí a zastavení obrazovek služeb](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Příčina

Systém Windows používá proces vypnutí k provádění operací údržby systému a ke změnám, jako jsou aktualizace, role a funkce. Nedoporučuje se přerušit tento kritický proces, dokud nebude dokončen. V závislosti na počtu aktualizací nebo změn a velikosti virtuálního počítače může proces trvat dlouhou dobu. Pokud je proces zastaven, je možné, že operační systém bude poškozen. Proces přerušte pouze v případě, že trvá příliš dlouho.

## <a name="solution"></a>Řešení

### <a name="collect-a-process-memory-dump"></a>Shromažďování výpisu stavu paměti procesu

1. Stáhněte [nástroj Procdump](http://download.sysinternals.com/files/Procdump.zip) do nového nebo existujícího datového disku, který je připojený k fungujícímu virtuálnímu počítače ze stejné oblasti.

2. Odpojte disk obsahující soubory potřebné z funkčního virtuálního počítače a připojte disk k poškozenému virtuálnímu počítači. Voláme tento disk **Utility disk**.

Pomocí [konzoly Serial Console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) proveďte následující kroky:

1. Otevřete prostředí Powershell pro správu a zkontrolujte službu, která je zavěšena při zastavení.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. Na administrativní CMD, získat PID zavěšené služby.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Získejte vzorek výpisu stavu <STOPPING SERVICE>paměti z procesu zavěšení .

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Nyní zabít zavěšený proces odemknout proces vypnutí.

   ``
   taskkill /PID <PID> /t /f
   ``

Jakmile operační spouštěč začne znovu, pokud se boty normálně, pak jen zajistit konzistence operačního serveru je v pořádku. Pokud je hlášeno poškození, spusťte následující příkaz, dokud není disk bez poškození:

``
dism /online /cleanup-image /restorehealth
``

Pokud se vám nedaří shromáždit výpis stavu paměti procesu nebo tento problém je rekurzivní a vyžadujete analýzu hlavní příčiny, pokračujte ve shromažďování výpisu stavu paměti operačního systému níže, pokračujte k otevření žádosti o podporu.

### <a name="collect-an-os-memory-dump"></a>Shromáždění výpisu stavu paměti operačního systému

Pokud se problém nevyřeší po čekání na změny procesu, budete muset shromáždit soubor s výpisem stavu paměti a podporu kontaktu. Chcete-li soubor s výpisem stavu paměti shromáždit, postupujte takto:

**Připojení disku operačního systému k virtuálnímu virtuálnímu počítače pro obnovení**

1. Pořiďte snímek disku operačního systému ovlivněného virtuálního počítače jako zálohu. Další informace naleznete [v tématu Snímek disku](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).

2. [Připojte disk operačního systému k virtuálnímu virtuálnímu počítače pro obnovení](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

3. Vzdálená plocha k virtuálnímu počítači pro obnovení.

4. Pokud je disk operačního systému zašifrován, musíte před přechodem k dalšímu kroku šifrování vypnout. Další informace naleznete [v tématu Dešifrování šifrovaného disku operačního systému ve virtuálním počítače, který nelze spustit](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution).

**Vyhledání souboru s výpisem stavu paměti a odeslání lístku podpory**

1. Na virtuálním počítači pro obnovení přejděte do složky Windows na připojeném disku operačního systému. Pokud je písmeno ovladače přiřazené k připojenému disku operačního systému F, musíte přejít na f:\Windows.

2. Vyhledejte soubor memory.dmp a [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem stavu paměti.

Pokud nemůžete najít soubor s výpisem stavu paměti, přesuňte další krok a povolte protokol výpisu a konzolu Serial Console.

**Povolení protokolu výpisu a sériové konzoly**

Chcete-li povolit protokol s výpisem stavu paměti a konzolu Serial Console, spusťte následující skript.

1. Otevřete relaci příkazu Se zvýšenými oprávněními (Spustit jako správce).

2. Spusťte tento skript:

   V tomto skriptu předpokládáme, že písmeno jednotky, které je přiřazeno k připojenému disku operačního systému, je F. Nahraďte ho příslušnou hodnotou ve vašem virtuálním počítači.

   ```
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
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. Ověřte, zda je na disku dostatek místa pro přidělení tolik paměti jako ram, což závisí na velikosti, kterou vybíráte pro tento virtuální počítač.

4. Pokud není dostatek místa nebo je velký virtuální modul (řada G, GS nebo E), můžete změnit umístění, kde bude tento soubor vytvořen, a odkazovat na jakýkoli jiný datový disk, který je připojený k virtuálnímu počítače. Chcete-li změnit umístění, je nutné změnit následující klíč:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Odpojte disk operačního systému a potom znovu připojte disk operačního systému k ovlivněnému virtuálnímu počítače](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

6. Spusťte virtuální ho spouštěč a získejte přístup k konzole Serial Console.

7. Chcete-li aktivovat výpis stavu paměti, vyberte možnost Odeslat nemaskovatelné přerušení (NMI).

   ![Odeslat nemaskovatelné přerušení](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Připojte disk operačního systému k virtuálnímu virtuálnímu počítače pro obnovení znovu, shromažďovat soubor s výpisem stavu paměti.

## <a name="contact-microsoft-support"></a>Obraťte se na podporu Microsoftu

Po shromáždění souboru s výpisem stavu paměti se obraťte na podporu společnosti Microsoft a zjistěte hlavní příčinu.
