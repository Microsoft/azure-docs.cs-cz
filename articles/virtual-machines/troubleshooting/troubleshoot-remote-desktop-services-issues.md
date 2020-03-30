---
title: Služba Vzdálená plocha nezačíná na virtuálním počítači Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit problémy se službou Vzdálená plocha při připojení k virtuálnímu počítači | Dokumenty společnosti Microsoft
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
ms.openlocfilehash: 4b314fbdb9cbc0c0b797cbee8e92ee4702bbea81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919460"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Služba Vzdálená plocha nezačíná na virtuálním počítači Azure

Tento článek popisuje, jak řešit problémy, když se připojíte k virtuálnímu počítači (VM) a vzdálená plocha nebo TermService, nespustí nebo se nezdaří spuštění.


## <a name="symptoms"></a>Příznaky

Při pokusu o připojení k virtuálnímu virtuálnímu zařízení dochází k následujícím scénářům:

- Snímek obrazovky virtuálního aplikace ukazuje, že operační systém je plně načtený a čeká na přihlašovací údaje.

    ![Snímek obrazovky se stavem virtuálního soudu](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Protokoly událostí ve virtuálním ms můžete vzdáleně zobrazit pomocí Prohlížeče událostí. Uvidíte, že vzdálená plocha, TermService, není spuštění nebo selhání spuštění. Následující protokol je ukázka:

    **Název protokolu**: Systém </br>
    **Zdroj**: Správce řízení služeb </br>
    **Datum**: 12/16/2017 11:19:36</br>
    **ID události**: 7022</br>
    **Kategorie úkolu**: Žádná</br>
    **Úroveň**: Chyba</br>
    **Klíčová slova**: Classic</br>
    **Uživatel**: Není k msti</br>
    **Počítač**: vm.contoso.com</br>
    **Popis**: Služba Vzdálená plocha byla na spuštění zavěšena. 

    Pomocí funkce Konzola pro sériový přístup můžete také vyhledat tyto chyby spuštěním následujícího dotazu: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Příčina
 
K tomuto problému dochází, protože vzdálená plocha není spuštěna na virtuálním počítači. Příčina může záviset na následujících scénářích: 

- Služba TermService je nastavena na **deaktivována**. 
- Služba TermService se zhroutí nebo neodpovídá. 
- TermService není spuštění z důvodu nesprávné konfigurace.

## <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, použijte konzolu Serial Console. Nebo jiný [opravit virtuální ho virtuálního počítače offline](#repair-the-vm-offline) připojením os y disku virtuálního počítače k virtuálnímu virtuálnímu počítače pro obnovení.

### <a name="use-serial-console"></a>Použití sériové konzoly

1. Přístup k [konzole Sériové konzole](serial-console-windows.md) výběrem **možnosti Podpora & poradce při potížích se sériovým** > **konzolí**. Pokud je tato funkce povolená na virtuálním počítači, můžete úspěšně připojit virtuální počítač.

2. Vytvořte nový kanál pro instanci CMD. Zadejte **CMD** pro spuštění kanálu a získání názvu kanálu.

3. Přepněte na kanál, na který běží instance CMD. V tomto případě by měl být kanál 1:

   ```
   ch -si 1
   ```

4. **Vyberte** zadat znovu a zadejte platné uživatelské jméno a heslo, místní nebo doménové ID, pro virtuální počítače.

5. Dotaz na stav služby TermService:

   ```
   sc query TermService
   ```

6. Pokud se ve stavu služby zobrazuje **Zastaveno**, zkuste službu spustit:

    ```
    sc start TermService
     ``` 

7. Dotaz na službu znovu a ujistěte se, že služba byla úspěšně spuštěna:

   ```
   sc query TermService
   ```
8. Pokud se služba nespustí, postupujte podle řešení na základě chyby, kterou jste obdrželi:

    |  Chyba |  Návrh |
    |---|---|
    |5 - PŘÍSTUP ODEPŘEN |Viz [TermService služba je zastavena z důvodu chyby odepřen přístup](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Viz [TermService služba je zakázána](#termservice-service-is-disabled).  |  
    |1058 - ERROR_SERVICE_DISABLED  |Viz [TermService service crashes or hangs](#termservice-service-crashes-or-hangs).  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu a rychle se obraťte na problém.|
    |1067 - ERROR_PROCESS_ABORTED  |Viz [TermService service crashes or hangs](#termservice-service-crashes-or-hangs).  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu a rychle se obraťte na problém.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |Viz [TermService service fails because of logon failure See TermService service fails of logon](#termservice-service-fails-because-of-logon-failure) |
    |1070 - ERROR_SERVICE_START_HANG   | Viz [TermService service crashes or hangs](#termservice-service-crashes-or-hangs). |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Viz [TermService služba je zakázána](#termservice-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu a rychle se obraťte na problém. |
    |1753   |[Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu a rychle se obraťte na problém.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>Služba TermService je zastavena z důvodu problému odepření přístupu.

1. Připojte se k [konzoli Serial Console](serial-console-windows.md) a otevřete instanci prostředí PowerShell.
2. Stáhněte nástroj Sledování procesů spuštěním následujícího skriptu:

   ```
   remove-module psreadline  
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
   $destination = "c:\temp\ProcessMonitor.zip" 
   $wc = New-Object System.Net.WebClient 
   $wc.DownloadFile($source,$destination) 
   ```

3. Nyní spusťte **procmon** stopu:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
   ```

4. Reprodukovat problém spuštěním služby, která je udělení **přístup odepřen**: 

   ```
   sc start TermService 
   ```

   Pokud se nezdaří, ukončete trasování sledování procesů:

   ```   
   procmon /Terminate 
   ```

5. Shromážděte soubor **c:\temp\ProcMonTrace.PML**:

    1. [Připojení datového disku k virtuálnímu počítače](../windows/attach-managed-disk-portal.md
).
    2. Pomocí konzoly Sériové konzole můžete soubor zkopírovat na novou jednotku. Například, `copy C:\temp\ProcMonTrace.PML F:\`. V tomto příkazu f je písmeno ovladače připojeného datového disku.
    3. Odpojte datovou jednotku a připojte ji k funkčnímu virtuálnímu počítači, který má nainstalovaný umulovací monitor procesu.

6. Otevřete **ProcMonTrace.PML** pomocí monitorování procesů pracovního virtuálního počítači. Pak filtr podle **výsledek je přístup odepřen**, jak je znázorněno na následujícím snímku obrazovky:

    ![Filtrovat podle výsledku v procesu monitoru](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Opravte klíče registru, složky nebo soubory, které jsou na výstupu. Obvykle tento problém je způsoben, když přihlašovací účet, který se používá ve službě nemá oprávnění ACL pro přístup k těmto objektům. Chcete-li znát správné oprávnění acl pro přihlašovací účet, můžete zkontrolovat na virtuálním počítači v pořádku. 

#### <a name="termservice-service-is-disabled"></a>Služba TermService je zakázána.

1. Obnovení výchozí hodnoty při spuštění služby:

   ```
   sc config TermService start= demand 
   ```

2. Spuštění služby:

   ```
   sc start TermService
   ```

3. Chcete-li se ujistit, že je služba spuštěna, znovu se dotazovat na jeho stav:

   ```
   sc query TermService 
   ```

4. Zkuste se připojit k virtuálnímu počítači pomocí vzdálené plochy.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>Služba TermService se nezdaří z důvodu selhání přihlášení.

1. K tomuto problému dochází, pokud byl změněn spouštěcí účet této služby. To to to to zpět na výchozí: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Spuštění služby:

        sc start TermService
3. Zkuste se připojit k virtuálnímu počítači pomocí vzdálené plochy.

#### <a name="termservice-service-crashes-or-hangs"></a>TermService služba dojde k chybě nebo zablokování
1. Pokud se stav služby zasekl v **okně Spuštění** nebo **Zastavení**, zkuste službu zastavit: 

        sc stop TermService
2. Izolujte službu na vlastním kontejneru "svchost":

        sc config TermService type= own
3. Spuštění služby:

        sc start TermService
4. Pokud se službě stále nedaří spustit, [kontaktujte podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Oprava virtuálního virtuálního montovadova offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojení disku operačního systému k virtuálnímu virtuálnímu počítače pro obnovení

1. [Připojte disk operačního systému k virtuálnímu virtuálnímu počítače pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
2. Spusťte připojení vzdálené plochy k virtuálnímu počítači pro obnovení. Zkontrolujte, zda je připojený disk v konzole pro správu disků označen jako **online.** Všimněte si písmena jednotky, které je přiřazeno k připojenému disku operačního systému.
3. Otevřete instanci příkazového řádku se zvýšenými oprávněními **(Spustit jako správce**). Potom spusťte následující skript. Předpokládáme, že písmeno jednotky, které je přiřazeno k připojenému disku operačního **systému,** je F . Nahraďte ji příslušnou hodnotou ve virtuálním počítači. 

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

4. [Odpojte disk operačního systému a znovu vytvořte virtuální hod](../windows/troubleshoot-recovery-disks-portal.md). Potom zkontrolujte, zda je problém vyřešen.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, obraťte se na [podporu,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) abyste problém vyřešili.
