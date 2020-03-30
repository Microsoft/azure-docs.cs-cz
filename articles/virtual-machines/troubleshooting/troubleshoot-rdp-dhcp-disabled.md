---
title: Nelze vzdáleně připojit k virtuálním počítačům Azure, protože služba DHCP je zakázána| Dokumenty společnosti Microsoft
description: Zjistěte, jak vyřešit problém protokolu RDP způsobený klientskou službou DHCP, který je v Microsoft Azure zakázán.| Dokumenty společnosti Microsoft
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
ms.openlocfilehash: 2c5b0556554d280e57b2df51875e1b057b5fb4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749889"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Nelze rdp do virtuálních počítačů Azure, protože služba klienta DHCP je zakázána

Tento článek popisuje problém, ve kterém nelze vzdálenou plochu na Virtuální počítače Azure Windows (VMs) po zakázání služby klienta DHCP ve virtuálním počítači.


## <a name="symptoms"></a>Příznaky
Připojení RDP nelze vytvořit jako virtuální počítač v Azure, protože služba klienta DHCP je ve virtuálním počítači zakázána. Když zkontrolujete snímek obrazovky v [diagnostice spouštění](../troubleshooting/boot-diagnostics.md) na webu Azure Portal, uvidíte spuštění virtuálního počítače normálně a čeká na přihlašovací údaje na přihlašovací obrazovce. Protokoly událostí ve virtuálním ms můžete vzdáleně zobrazit pomocí Prohlížeče událostí. Uvidíte, že klientská služba DHCP není spuštěna nebo se nespustí. Následující ukázkový protokol:

**Název protokolu**: Systém </br>
**Zdroj**: Správce řízení služeb </br>
**Datum**: 12/16/2015 11:19:36 </br>
**ID události**: 7022 </br>
**Kategorie úkolu**: Žádná </br>
**Úroveň**: Chyba </br>
**Klíčová slova**: Classic</br>
**Uživatel**: Není k msti </br>
**Počítač**: myvm.cosotos.com</br>
**Popis**: Služba klienta DHCP byla spuštěna při spuštění.</br>

Pro virtuální zařízení Správce prostředků můžete pomocí funkce Konzoly pro sériový přístup dotazovat se na protokoly událostí 7022 pomocí následujícího příkazu:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

Pro klasické virtuální chod, budete muset pracovat v režimu OFFLINE a shromažďovat protokoly ručně.

## <a name="cause"></a>Příčina

Klientská služba DHCP není spuštěna na virtuálním počítači.

> [!NOTE]
> Tento článek se vztahuje pouze na službu klienta DHCP, nikoli na server DHCP.

## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte snímek disku operačního systému ovlivněného virtuálního počítače jako zálohu. Další informace naleznete [v tématu Snímek disku](../windows/snapshot-copy-managed-disk.md).

Chcete-li tento problém vyřešit, použijte sériové řízení k povolení DHCP nebo [obnovení síťového rozhraní](reset-network-interface.md) pro virtuální hod.

### <a name="use-serial-control"></a>Použití sériového řízení

1. Připojte se k [konzoli Serial Console a otevřete instanci CMD](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Pokud konzola Sériové konzole není na vašem virtuálním počítači povolená, přečtěte si článek [Obnovení síťového rozhraní](reset-network-interface.md).
2. Zkontrolujte, zda je v síťovém rozhraní zakázánserver DHCP:

        sc query DHCP
3. Pokud je služba DHCP zastavena, pokuste se službu spustit

        sc start DHCP

4. Dotaz služby znovu a ujistěte se, že služba byla úspěšně spuštěna.

        sc query DHCP

    Zkuste se připojit k virtuálnímu virtuálnímu zařízení a zjistěte, jestli je problém vyřešen.
5. Pokud se služba nespustí, použijte následující vhodné řešení na základě chybové zprávy, kterou jste obdrželi:

    | Chyba  |  Řešení |
    |---|---|
    | 5 - PŘÍSTUP ODEPŘEN  | Viz [Služba klienta DHCP je zastavena z důvodu chyby odepření přístupu](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Viz [Selhání nebo zablokování služby klienta DHCP](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 - ERROR_SERVICE_DISABLED  | Viz [Služba klienta DHCP je zakázána](#dhcp-client-service-is-disabled).  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu a získejte problém rychle vyřešen.   |
    | 1067 - ERROR_PROCESS_ABORTED |Viz [Selhání nebo zablokování služby klienta DHCP](#dhcp-client-service-crashes-or-hangs).   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu a získejte problém rychle vyřešen.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Viz [Selhání služby klienta DHCP z důvodu selhání přihlášení](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Viz [Selhání nebo zablokování služby klienta DHCP](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Viz [Služba klienta DHCP je zakázána](#dhcp-client-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu a získejte problém rychle vyřešen.  |
    |1053 | [Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu a získejte problém rychle vyřešen.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>Klientská služba DHCP je zastavena z důvodu chyby odepření přístupu.

1. Připojte se k [konzoli Serial Console](serial-console-windows.md) a otevřete instanci prostředí PowerShell.
2. Stáhněte nástroj Sledování procesů spuštěním následujícího skriptu:

   ```powershell
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
4. Reprodukovat problém spuštěním služby, která je generování **zprávy odepřen přístup:**

   ```
   sc start DHCP
   ```

   Pokud se nezdaří, ukončete trasování sledování procesů:

   ```
   procmon /Terminate
   ```
5. Shromážděte soubor **c:\temp\ProcMonTrace.PML:**

    1. [Připojení datového disku k virtuálnímu počítače](../windows/attach-managed-disk-portal.md
).
    2. Pomocí konzoly Sériové konzole můžete soubor zkopírovat na novou jednotku. Například, `copy C:\temp\ProcMonTrace.PML F:\`. V tomto příkazu f je písmeno ovladače připojeného datového disku. Podle potřeby nahraďte písmeno správnou hodnotou.
    3. Odpojte datovou jednotku a pak ji připojte k funkčnímu virtuálnímu virtuálnímu mase, který má nainstalovaný umulovací monitor procesu.

6. Otevřete **ProcMonTrace.PML** pomocí process monitoru na pracovním virtuálním počítači. Pak filtr podle **výsledek je přístup odepřen**, jak je znázorněno na následujícím snímku obrazovky:

    ![Filtrovat podle výsledku v procesu monitoru](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Opravte klíče registru, složky nebo soubory, které jsou na výstupu. Obvykle tento problém je způsoben, když přihlašovací účet, který se používá ve službě nemá oprávnění ACL pro přístup k těmto objektům. Chcete-li zjistit správné oprávnění acl pro přihlašovací účet, můžete zkontrolovat na virtuálním počítači v pořádku.

#### <a name="dhcp-client-service-is-disabled"></a>Služba klienta DHCP je zakázána.

1. Obnovení výchozí hodnoty při spuštění služby:

   ```
   sc config DHCP start= auto
   ```

2. Spuštění služby:

   ```
   sc start DHCP
   ```

3. Chcete-li se ujistit, že je spuštěná, zadejte stav služby znovu:

   ```
   sc query DHCP
   ```

4. Zkuste se připojit k virtuálnímu počítači pomocí vzdálené plochy.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>Služba klienta DHCP se nezdaří z důvodu selhání přihlášení

1. Vzhledem k tomu, že k tomuto problému dochází, pokud byl změněn spouštěcí účet této služby, vraťte účet do výchozího stavu:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Spuštění služby:

        sc start DHCP
3. Zkuste se připojit k virtuálnímu počítači pomocí vzdálené plochy.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>Selhání nebo zablokování služby klienta DHCP

1. Pokud se stav služby zasekne ve stavu **Spuštění** nebo **Zastavení,** zkuste službu zastavit:

        sc stop DHCP
2. Izolujte službu na vlastním kontejneru "svchost":

        sc config DHCP type= own
3. Spuštění služby:

        sc start DHCP
4. Pokud se služba stále nespustí, [kontaktujte podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Oprava virtuálního virtuálního montovadova offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojení disku operačního systému k virtuálnímu virtuálnímu počítače pro obnovení

1. [Připojte disk operačního systému k virtuálnímu virtuálnímu počítače pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
2. Spusťte připojení vzdálené plochy k virtuálnímu počítači pro obnovení. Zkontrolujte, zda je připojený disk v konzole pro správu disků označen jako **online.** Všimněte si písmena jednotky, které je přiřazeno k připojenému disku operačního systému.
3.  Otevřete instanci příkazového řádku se zvýšenými oprávněními **(Spustit jako správce**). Potom spusťte následující skript. Tento skript předpokládá, že písmeno jednotky přiřazené k připojenému disku operačního systému je **F**. Podle potřeby nahraďte písmeno hodnotou ve vašem virtuálním počítači.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [Odpojte disk operačního systému a znovu vytvořte virtuální hod](../windows/troubleshoot-recovery-disks-portal.md). Potom zkontrolujte, zda je problém vyřešen.

## <a name="next-steps"></a>Další kroky

Pokud stále potřebujete pomoc, obraťte se na [podporu,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) abyste problém vyřešili.