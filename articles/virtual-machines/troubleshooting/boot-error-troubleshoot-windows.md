---
title: Služba Azure Virtual Machines Shutdown se zablokuje při restartování, vypínání nebo zastavování služeb | Microsoft Docs
description: Tento článek vám pomůže při řešení chyb služby v Azure Windows Virtual Machines.
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
ms.openlocfilehash: e5ab1262def78da4971ea6e5535f3ac915a38ec8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526754"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Vypnutí virtuálního počítače Azure s Windows se zablokuje při restartování, vypínání nebo zastavování služeb.

Tento článek popisuje kroky pro řešení potíží se zprávami "restartování", "vypnutí" nebo "zastavení služeb", se kterými se můžete setkat při restartování virtuálního počítače s Windows v Microsoft Azure.

## <a name="symptoms"></a>Příznaky

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky virtuálního počítače, může se zobrazit, že snímek obrazovky zobrazuje zprávu "restartování", "vypínání" nebo "zastavení služeb".

![Restart, vypínání a zastavování obrazovek služby](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Příčina

Systém Windows pomocí procesu vypnutí provádí operace údržby systému a zpracovává změny, jako jsou například aktualizace, role a funkce. Nedoporučujeme tento kritický proces přerušit, dokud se nedokončí. V závislosti na počtu aktualizací/změn a velikosti virtuálního počítače může proces trvat dlouhou dobu. Pokud je proces zastavený, je možné, že operační systém bude poškozený. Proces bude přerušen pouze v případě, že trvá příliš dlouho.

## <a name="solution"></a>Řešení

### <a name="collect-a-process-memory-dump"></a>Shromažďování výpisu paměti procesu

1. Stáhněte si [Nástroj ProcDump](http://download.sysinternals.com/files/Procdump.zip) do nového nebo existujícího datového disku, který je připojený k PRACOVNÍmu virtuálnímu počítači ze stejné oblasti.

2. Odpojte disk obsahující soubory potřebné z pracovního virtuálního počítače a připojte tento disk k poškozenému VIRTUÁLNÍmu počítači. Tento disk voláme na **disk s nástrojem**.

Pomocí [konzoly sériového portu](./serial-console-windows.md) proveďte následující kroky:

1. Otevřete modul PowerShell pro správu a podívejte se na službu, která při zastavování přestane reagovat.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. V nástroji pro správu nástroje se Získejte PID nereagující služby.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Získat ukázku výpisu paměti z nereagující procesu <STOPPING SERVICE> .

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Nyní ukončí nereagující proces, aby se proces vypnutí odemkl.

   ``
   taskkill /PID <PID> /t /f
   ``

Po opětovném spuštění operačního systému, pokud se spustí normálně, stačí, abyste zajistili konzistenci OS v pořádku. Pokud je oznámeno poškození, spusťte následující příkaz, dokud disk nedosáhne poškozeného:

``
dism /online /cleanup-image /restorehealth
``

Pokud nemůžete shromáždit výpis paměti procesu, nebo je tento problém rekurzivní a vyžadujete analýzu původní příčiny, pokračujte v shromažďování výpisu paměti operačního systému níže, přejděte k otevření žádosti o podporu.

### <a name="collect-an-os-memory-dump"></a>Shromažďování výpisu paměti operačního systému

Pokud se problém nevyřeší po čekání na zpracování změn, budete potřebovat shromáždit soubor s výpisem paměti a kontaktovat podporu. Chcete-li shromáždit soubor s výpisem paměti, postupujte podle následujících kroků:

**Připojení disku s operačním systémem k virtuálnímu počítači pro obnovení**

1. Pořídit snímek disku s operačním systémem ovlivněného virtuálního počítače jako zálohy. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

2. [Připojte disk s operačním systémem k virtuálnímu počítači pro obnovení](./troubleshoot-recovery-disks-portal-windows.md).

3. Vzdálená plocha do virtuálního počítače pro obnovení.

4. Pokud je disk s operačním systémem zašifrovaný, musíte před přechodem k dalšímu kroku vypnout šifrování. Další informace najdete v tématu [dešifrujte zašifrovaný disk s operačním systémem ve virtuálním počítači, který se nedá spustit](./troubleshoot-bitlocker-boot-error.md#solution).

**Vyhledat soubor s výpisem paměti a odeslat lístek podpory**

1. Na virtuálním počítači pro obnovení otevřete složku Windows na připojeném disku s operačním systémem. Pokud je písmeno přiřazené k připojenému disku s operačním systémem F, je nutné přejít na F:\Windows.

2. Vyhledejte soubor Memory. dmp a pak [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem paměti.

Pokud soubor s výpisem paměti nemůžete najít, přejděte k dalšímu kroku a Povolte protokol výpisu a sériovou konzolu.

**Povolit protokol výpisu paměti a sériová konzola**

Pokud chcete povolit protokol výpisu a sériovou konzolu, spusťte následující skript.

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).

2. Spusťte tento skript:

   V tomto skriptu předpokládáme, že písmeno jednotky přiřazené k připojenému disku s operačním systémem je F. nahraďte ho příslušnou hodnotou ve vašem VIRTUÁLNÍm počítači.

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

3. Ověřte, zda je na disku dostatek místa pro přidělení tolika paměti jako paměti RAM, což závisí na velikosti, kterou vybíráte pro tento virtuální počítač.

4. Pokud není dostatek místa nebo je virtuální počítač velký (G, GS nebo E-series), můžete změnit umístění, kde bude tento soubor vytvořen, a odkazovat na jakýkoli jiný datový disk, který je připojen k virtuálnímu počítači. Chcete-li změnit umístění, je nutné změnit následující klíč:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Odpojte disk s operačním systémem a pak znovu připojte disk s operačním systémem k ovlivněnému virtuálnímu počítači](./troubleshoot-recovery-disks-portal-windows.md).

6. Spusťte virtuální počítač a otevřete konzolu sériového portu.

7. Chcete-li aktivovat výpis paměti, vyberte možnost Odeslat nemaskovatelné přerušení (NMI).

   ![Odeslat nemaskované přerušení](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Znovu připojte disk s operačním systémem k virtuálnímu počítači pro obnovení, shromážděte soubor výpisu paměti.

## <a name="contact-microsoft-support"></a>Obraťte se na podporu Microsoftu

Po shromáždění souboru s výpisem paměti kontaktujte podporu Microsoftu, abyste zjistili hlavní příčinu.
