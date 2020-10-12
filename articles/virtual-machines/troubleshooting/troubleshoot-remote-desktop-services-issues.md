---
title: Služba Vzdálená plocha se nespouští na virtuálním počítači Azure | Microsoft Docs
description: Naučte se řešit problémy se službou Vzdálená plocha při připojení k virtuálnímu počítači | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 0b00785fed7708986885e9da9102e8f1b4fd4539
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86508878"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Služba Vzdálená plocha se nespouští na virtuálním počítači Azure.

Tento článek popisuje, jak řešit problémy při připojení k virtuálnímu počítači Azure a službě Vzdálená plocha, nebo TermService, nespouští se nebo nespustí.


## <a name="symptoms"></a>Příznaky

Při pokusu o připojení k virtuálnímu počítači se setkáte s následujícími scénáři:

- Snímek obrazovky virtuálního počítače ukazuje, že je operační systém plně načtený a čeká na přihlašovací údaje.

    ![Snímek obrazovky se stavem virtuálního počítače](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Můžete vzdáleně zobrazit protokoly událostí na virtuálním počítači pomocí Prohlížeč událostí. Uvidíte, že Vzdálená plocha, TermService, nespouští se nebo ji nespustí. Následující protokol je příkladem:

    **Název protokolu**: systém </br>
    **Zdroj**: Správce řízení služeb </br>
    **Datum**: 12/16/2017 11:19:36 dop.</br>
    **ID události**: 7022</br>
    **Kategorie úkolu**: žádné</br>
    **Úroveň**: Chyba</br>
    **Klíčová slova**: klasický</br>
    **Uživatel**: není k dispozici</br>
    **Počítač**: VM.contoso.com</br>
    **Popis**: Služba Vzdálená plocha při spuštění přestala reagovat. 

    Pomocí funkce Konzola sériového přístupu můžete také vyhledat tyto chyby spuštěním následujícího dotazu: 

    ```console
   wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
    ```

## <a name="cause"></a>Příčina
 
K tomuto problému dochází, protože na virtuálním počítači není spuštěná služba Vzdálená plocha. Příčina může záviset na následujících scénářích: 

- Služba TermService je nastavená na **disabled (zakázáno**). 
- Služba TermService se zhroutí nebo neodpovídá. 
- TermService se nespouští z důvodu nesprávné konfigurace.

## <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, použijte konzolu sériového portu. Nebo případně [opravte virtuální počítač offline](#repair-the-vm-offline) připojením disku operačního systému virtuálního počítače k virtuálnímu počítači pro obnovení.

### <a name="use-serial-console"></a>Použití sériové konzoly

1. Přístup ke [konzole sériového portu](serial-console-windows.md) výběrem možnosti **Podpora & řešení potíží**  >  **sériová konzola**. Pokud je funkce na virtuálním počítači povolená, můžete virtuální počítač úspěšně připojit.

2. Vytvoří nový kanál pro instanci CMD. Zadáním **příkazu cmd** spusťte kanál a získejte název kanálu.

3. Přepněte na kanál, který spouští instanci CMD. V takovém případě by měl být kanál 1:

   ```
   ch -si 1
   ```

4. Znovu vyberte **ENTER** a zadejte platné uživatelské jméno a heslo, místní nebo ID domény pro virtuální počítač.

5. Dotaz na stav služby TermService:

   ```
   sc query TermService
   ```

6. Pokud se stav služby zobrazí jako **Zastaveno**, pokuste se spustit službu:

    ```
    sc start TermService
     ``` 

7. Znovu spusťte dotaz na službu, abyste se ujistili, že je služba úspěšně spuštěná:

   ```
   sc query TermService
   ```
8. Pokud se služba nespustí, postupujte podle toho, co jste dostali k chybě:

    |  Chyba |  Návrh |
    |---|---|
    |5 – PŘÍSTUP BYL ODEPŘEN. |V tématu [Služba TermService je zastavena z důvodu chyby odepření přístupu](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 – ERROR_SERVICE_REQUEST_TIMEOUT  |Viz [Služba TermService je zakázaná](#termservice-service-is-disabled).  |  
    |1058 – ERROR_SERVICE_DISABLED  |Přečtěte si téma [zhroucení nebo zablokování služby TermService](#termservice-service-crashes-or-hangs).  |
    |1059 – ERROR_CIRCULAR_DEPENDENCY |[Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu, abyste mohli rychle vyřešit problém.|
    |1067 – ERROR_PROCESS_ABORTED  |Přečtěte si téma [zhroucení nebo zablokování služby TermService](#termservice-service-crashes-or-hangs).  |
    |1068 – ERROR_SERVICE_DEPENDENCY_FAIL|[Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu, abyste mohli rychle vyřešit problém.|
    |1069 – ERROR_SERVICE_LOGON_FAILED  |Neúspěšné přihlášení najdete v tématu [Služba TermService](#termservice-service-fails-because-of-logon-failure) |
    |1070 – ERROR_SERVICE_START_HANG   | Přečtěte si téma [zhroucení nebo zablokování služby TermService](#termservice-service-crashes-or-hangs). |
    |1077 – ERROR_SERVICE_NEVER_STARTED   | Viz [Služba TermService je zakázaná](#termservice-service-is-disabled).  |
    |1079 – ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu, abyste mohli rychle vyřešit problém. |
    |1753   |[Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu, abyste mohli rychle vyřešit problém.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>Služba TermService se zastavila kvůli problému s odepřením přístupu.

1. Připojte se ke [konzole sériového](serial-console-windows.md) prostředí a otevřete instanci prostředí PowerShell.
2. Spusťte následující skript a Stáhněte si Nástroj Process monitor:

   ```
   remove-module psreadline  
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
   $destination = "c:\temp\ProcessMonitor.zip" 
   $wc = New-Object System.Net.WebClient 
   $wc.DownloadFile($source,$destination) 
   ```

3. Nyní spusťte trasování **procmon** :

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
   ```

4. Reprodukování problému spuštěním služby, která poskytuje **přístup odepřen**: 

   ```
   sc start TermService 
   ```

   Pokud dojde k chybě, ukončete trasování sledování procesu:

   ```   
   procmon /Terminate 
   ```

5. Shromáždění souboru **c:\temp\ProcMonTrace.PML**:

    1. [Připojte datový disk k virtuálnímu počítači](../windows/attach-managed-disk-portal.md
).
    2. Pomocí sériové konzoly můžete soubor zkopírovat na novou jednotku. Například, `copy C:\temp\ProcMonTrace.PML F:\`. V tomto příkazu je F písmeno ovladače připojeného datového disku.
    3. Odpojte datovou jednotku a připojte ji k pracovnímu virtuálnímu počítači, na kterém je nainstalovaný ubstakke monitor procesu.

6. Otevřete **ProcMonTrace. PML** pomocí procesu sledovat pracovní virtuální počítač. Pak je filtr podle **výsledku odepřen přístup**, jak je znázorněno na následujícím snímku obrazovky:

    ![Filtrovat podle výsledku v monitorování procesu](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Opravte klíče registru, složky nebo soubory, které jsou ve výstupu. K tomuto problému obvykle dochází, když přihlašovací účet, který se používá ve službě, nemá oprávnění seznamu ACL pro přístup k těmto objektům. Pokud chcete zjistit správné oprávnění seznamu ACL pro přihlašovací účet, můžete se podívat na zdravý virtuální počítač. 

#### <a name="termservice-service-is-disabled"></a>Služba TermService je zakázaná.

1. Obnovte službu na výchozí spouštěcí hodnotu:

   ```
   sc config TermService start= demand 
   ```

2. Spusťte službu:

   ```
   sc start TermService
   ```

3. Znovu spusťte dotaz na svůj stav, abyste se ujistili, že je služba spuštěná:

   ```
   sc query TermService 
   ```

4. Zkuste se připojit k virtuálnímu počítači pomocí vzdálené plochy.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>Služba TermService selže kvůli chybě přihlášení.

1. K tomuto problému dochází, pokud došlo ke změně spouštěcího účtu této služby. Změna zpět na výchozí: 

    ```console
    sc config TermService obj= 'NT Authority\NetworkService'
    ```

2. Spusťte službu:

    ```console
    sc start TermService
    ```

3. Zkuste se připojit k virtuálnímu počítači pomocí vzdálené plochy.

#### <a name="termservice-service-crashes-or-hangs"></a>Zhroucení nebo zablokování služby TermService
1. Pokud je stav služby zablokovaný při **spuštění** nebo **zastavení**, zkuste službu zastavit: 

    ```console
    sc stop TermService
    ```

2. Izolujte službu na svém vlastním kontejneru Svchost:

    ```console
    sc config TermService type= own
    ```

3. Spusťte službu:

    ```console
    sc start TermService
    ```

4. Pokud se služba stále nedaří spustit, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Oprava virtuálního počítače v režimu offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojení disku s operačním systémem k virtuálnímu počítači pro obnovení

1. [Připojte disk s operačním systémem k virtuálnímu počítači pro obnovení](./troubleshoot-recovery-disks-portal-windows.md).
2. Spusťte připojení ke vzdálené ploše virtuálního počítače pro obnovení. Ujistěte se, že připojený disk je označen jako **online** v konzole pro správu disků. Poznamenejte si písmeno jednotky přiřazené k připojenému disku s operačním systémem.
3. Otevřete instanci příkazového řádku se zvýšenými oprávněními (**Spustit jako správce**). Pak spusťte následující skript. Předpokládáme, že písmeno jednotky přiřazené k připojenému disku s operačním systémem je **F**. Nahraďte ji odpovídající hodnotou ve vašem VIRTUÁLNÍm počítači. 

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
   REM Set default values back on the broken service 
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
   ```

4. [Odpojte disk s operačním systémem a znovu vytvořte virtuální počítač](./troubleshoot-recovery-disks-portal-windows.md). Potom zkontrolujte, zda byl problém vyřešen.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , abyste mohli problém vyřešit.
