---
title: Nejde se vzdáleně připojit k Azure Virtual Machines, protože služba DHCP je zakázaná | Microsoft Docs
description: Přečtěte si, jak řešit potíže s protokolem RDP, které způsobuje služba klienta DHCP, je v Microsoft Azure zakázaná. | Microsoft Docs
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
ms.openlocfilehash: 945f8896a844e7a73107df44d03abc7290f4e3fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86999130"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Nejde RDP do Azure Virtual Machines, protože je zakázaná služba klienta DHCP.

Tento článek popisuje problém, kdy po zakázání služby klienta DHCP na virtuálním počítači nemůžete vzdálenou plochu do Azure Windows Virtual Machines (virtuální počítače).


## <a name="symptoms"></a>Příznaky
Nemůžete vytvořit připojení RDP k virtuálnímu počítači v Azure, protože služba klienta DHCP je ve virtuálním počítači zakázaná. Po kontrole snímku obrazovky v [diagnostice spouštění](../troubleshooting/boot-diagnostics.md) v Azure Portal vidíte, že se virtuální počítač spouští normálně a čeká na přihlašovací údaje na přihlašovací obrazovce. Můžete vzdáleně zobrazit protokoly událostí na virtuálním počítači pomocí Prohlížeč událostí. Vidíte, že služba klienta DHCP není spuštěná nebo se nepovede spustit. Následující vzorový protokol:

**Název protokolu**: systém </br>
**Zdroj**: Správce řízení služeb </br>
**Datum**: 12/16/2015 11:19:36 dop. </br>
**ID události**: 7022 </br>
**Kategorie úkolu**: žádné </br>
**Úroveň**: Chyba </br>
**Klíčová slova**: klasický</br>
**Uživatel**: není k dispozici </br>
**Počítač**: myvm.cosotos.com</br>
**Popis**: služba klienta DHCP při spuštění přestala reagovat.</br>

U Správce prostředků virtuálních počítačů můžete pomocí funkce Konzola sériového přístupu zadat dotaz na protokoly událostí 7022 pomocí následujícího příkazu:

```console
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

U klasických virtuálních počítačů budete muset pracovat v OFFLINE režimu a shromažďovat protokoly ručně.

## <a name="cause"></a>Příčina

Služba klienta DHCP není na virtuálním počítači spuštěná.

> [!NOTE]
> Tento článek se týká pouze služby klienta DHCP, nikoli serveru DHCP.

## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte si snímek disku s operačním systémem ovlivněného virtuálního počítače jako záložního. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

Pokud chcete tento problém vyřešit, pomocí sériového řízení povolte DHCP nebo [resetovat síťové rozhraní](reset-network-interface.md) pro virtuální počítač.

### <a name="use-serial-control"></a>Použití ovládacího prvku sériového portu

1. Připojte se ke [konzole sériového prostředí a otevřete instanci cmd](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Pokud není na vašem VIRTUÁLNÍm počítači povolená síťová konzola, přečtěte si téma [resetování síťového rozhraní](reset-network-interface.md).
2. Ověřte, jestli je na síťovém rozhraní zakázaný protokol DHCP:

    ```console
    sc query DHCP
    ```

3. Pokud je server DHCP zastavený, zkuste službu spustit.

    ```console
    sc start DHCP
    ```

4. Znovu spusťte dotaz na službu, abyste se ujistili, že byla služba úspěšně spuštěna.

    ```console
    sc query DHCP
    ```

    Zkuste se připojit k virtuálnímu počítači a zjistit, jestli se problém vyřešil.
5. Pokud se služba nespustí, použijte následující vhodné řešení na základě chybové zprávy, kterou jste dostali:

    | Chyba  |  Řešení |
    |---|---|
    | 5 – PŘÍSTUP BYL ODEPŘEN.  | V části [klientská služba DHCP se zastavila kvůli chybě odepření přístupu](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 – ERROR_SERVICE_REQUEST_TIMEOUT   | Přečtěte si téma [zhroucení nebo zablokování služby klienta DHCP](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 – ERROR_SERVICE_DISABLED  | Podívejte [se na téma klientská služba DHCP je zakázaná](#dhcp-client-service-is-disabled).  |
    | 1059 – ERROR_CIRCULAR_DEPENDENCY  |[Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu, abyste mohli rychle vyřešit váš problém.   |
    | 1067 – ERROR_PROCESS_ABORTED |Přečtěte si téma [zhroucení nebo zablokování služby klienta DHCP](#dhcp-client-service-crashes-or-hangs).   |
    |1068 – ERROR_SERVICE_DEPENDENCY_FAIL   | [Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu, abyste mohli rychle vyřešit váš problém.  |
    |1069 – ERROR_SERVICE_LOGON_FAILED   |  Pokud [nedošlo k chybě přihlášení, podívejte se na téma služba klienta DHCP selže](#dhcp-client-service-fails-because-of-logon-failure) . |
    | 1070 – ERROR_SERVICE_START_HANG  | Přečtěte si téma [zhroucení nebo zablokování služby klienta DHCP](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 – ERROR_SERVICE_NEVER_STARTED  | Podívejte [se na téma klientská služba DHCP je zakázaná](#dhcp-client-service-is-disabled).  |
    |1079 – ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu, abyste mohli rychle vyřešit váš problém.  |
    |1053 | [Obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se na podporu, abyste mohli rychle vyřešit váš problém.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>Služba klienta DHCP se zastavila kvůli chybě odepření přístupu.

1. Připojte se ke [konzole sériového](serial-console-windows.md) prostředí a otevřete instanci prostředí PowerShell.
2. Spusťte následující skript a Stáhněte si Nástroj Process monitor:

   ```powershell
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
4. Reprodukování problému spuštěním služby, která generuje zprávu o **odepření přístupu** :

   ```
   sc start DHCP
   ```

   Pokud dojde k chybě, ukončete trasování sledování procesu:

   ```
   procmon /Terminate
   ```
5. Shromáždění souboru **c:\temp\ProcMonTrace.PML** :

    1. [Připojte datový disk k virtuálnímu počítači](../windows/attach-managed-disk-portal.md
).
    2. Pomocí sériové konzoly můžete soubor zkopírovat na novou jednotku. Například, `copy C:\temp\ProcMonTrace.PML F:\`. V tomto příkazu je F písmeno ovladače připojeného datového disku. Nahraďte písmeno odpovídající správnou hodnotou.
    3. Odpojte datovou jednotku a pak ji připojte k pracovnímu virtuálnímu počítači, na kterém je nainstalovaný ubstakke monitor procesu.

6. Otevřete **ProcMonTrace. PML** pomocí sledování procesu na pracovním virtuálním počítači. Pak je filtr podle **výsledku odepřen přístup**, jak je znázorněno na následujícím snímku obrazovky:

    ![Filtrovat podle výsledku v monitorování procesu](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Opravte klíče registru, složky nebo soubory, které jsou ve výstupu. K tomuto problému obvykle dochází, když přihlašovací účet, který se používá ve službě, nemá oprávnění seznamu ACL pro přístup k těmto objektům. Pokud chcete zjistit správné oprávnění seznamu ACL pro přihlašovací účet, můžete se podívat na zdravý virtuální počítač.

#### <a name="dhcp-client-service-is-disabled"></a>Služba klienta DHCP je zakázána.

1. Obnovte službu na výchozí spouštěcí hodnotu:

   ```
   sc config DHCP start= auto
   ```

2. Spusťte službu:

   ```
   sc start DHCP
   ```

3. Spusťte dotaz na stav služby znovu, abyste se ujistili, že je spuštěný:

   ```
   sc query DHCP
   ```

4. Zkuste se připojit k virtuálnímu počítači pomocí vzdálené plochy.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>Služba Klient DHCP selže kvůli chybě přihlášení.

1. Vzhledem k tomu, že k tomuto problému dochází v případě, že došlo ke změně spouštěcího účtu této služby, vraťte účet na jeho výchozí stav:

    ```console
    sc config DHCP obj= 'NT Authority\Localservice'
    ```

2. Spusťte službu:

    ```console
    sc start DHCP
    ```

3. Zkuste se připojit k virtuálnímu počítači pomocí vzdálené plochy.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>Dojde k chybě nebo zablokování služby klienta DHCP.

1. Pokud je stav služby zablokovaný ve stavu **spuštění** nebo **zastavení** , zkuste službu zastavit:

    ```console
    sc stop DHCP
    ```

2. Izolujte službu na svém vlastním kontejneru Svchost:

    ```console
    sc config DHCP type= own
    ```

3. Spusťte službu:

    ```console
    sc start DHCP
    ```

4. Pokud se služba ještě nespustí, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Oprava virtuálního počítače v režimu offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojení disku s operačním systémem k virtuálnímu počítači pro obnovení

1. [Připojte disk s operačním systémem k virtuálnímu počítači pro obnovení](./troubleshoot-recovery-disks-portal-windows.md).
2. Spusťte připojení ke vzdálené ploše virtuálního počítače pro obnovení. Ujistěte se, že připojený disk je označen jako **online** v konzole pro správu disků. Poznamenejte si písmeno jednotky přiřazené k připojenému disku s operačním systémem.
3.  Otevřete instanci příkazového řádku se zvýšenými oprávněními (**Spustit jako správce**). Pak spusťte následující skript. Tento skript předpokládá, že písmeno jednotky přiřazené k připojenému disku s operačním systémem je **F**. Nahraďte písmeno odpovídající hodnotou ve vašem VIRTUÁLNÍm počítači.

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

4. [Odpojte disk s operačním systémem a znovu vytvořte virtuální počítač](./troubleshoot-recovery-disks-portal-windows.md). Potom zkontrolujte, zda byl problém vyřešen.

## <a name="next-steps"></a>Další kroky

Pokud stále potřebujete pomoc, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby se problém vyřešil.
