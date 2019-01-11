---
title: Nelze vzdáleně připojit k Azure Virtual Machines vzhledem k tomu, že je zakázané DHCP | Dokumentace Microsoftu
description: Informace o řešení potíží s RDP problém, který je způsobeno tím, služba Klient DHCP je zakázaný ve službě Microsoft Azure. | Dokumentace Microsoftu
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 5842c5edd0402d61f564ab15e34e8f69c0e718d7
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213446"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Nelze RDP na virtuálních počítačích Azure, protože služba Klient DHCP je zakázána.

Tento článek popisuje problém, který se po zakázání služby Klient DHCP ve virtuálním počítači je nelze vzdálené plochy Azure Windows Virtual Machines (VM).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="symptoms"></a>Příznaky

Nemůžete provádět připojení ke vzdálené ploše virtuálního počítače v Azure protože ve virtuálním počítači je zakázána služba Klient DHCP. Když vrátíte se změnami na snímku obrazovky [Diagnostika spouštění](../troubleshooting/boot-diagnostics.md) na webu Azure Portal, uvidíte, že virtuální počítač se spustí normálně a čeká se přihlašovací údaje na přihlašovací obrazovce. Vzdáleně Zkontrolujte protokoly událostí ve virtuálním počítači s použitím prohlížeče události. Uvidíte, že služba Klient DHCP není spuštěná nebo nepodaří spustit. Následující ukázka protokolu:

**Název protokolu**: Systémový </br>
**Zdroj**: Správce řízení služeb </br>
**Datum**: 12/16/2015 11:19:36 AM </br>
**ID události**: 7022 </br>
**Úloha kategorie**: Žádný </br>
**Úroveň**: Chyba </br>
**Klíčová slova**: Classic</br>
**Uživatel**: neuvedeno </br>
**Počítač**: myvm.cosotos.com</br>
**Popis**: Služba Klient DHCP přestala během spouštění reagovat.</br>

Pro virtuální počítače Resource Manageru můžete použít funkce konzoly sériového portu přístup k dotazu pro protokoluje událost 7022 pomocí následujícího příkazu:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

Klasické virtuální počítače musíte pracovat v OFFLINE režimu a shromažďovat protokoly ručně.

## <a name="cause"></a>Příčina

Služba klienta DHCP není spuštěná na virtuálním počítači.

> [!NOTE]
> Tento článek se týká pouze pro služby Klient DHCP a ne Server DHCP.

## <a name="solution"></a>Řešení

Předtím, než budete postupovat podle těchto kroků, vytvořte snímek disku s operačním systémem virtuálního počítače ovlivněný jako záložní. Další informace najdete v tématu [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).

Chcete-li tento problém vyřešit, povolte protokol DHCP pomocí sériového portu ovládací prvek nebo [resetování síťové rozhraní](reset-network-interface.md) pro virtuální počítač.

### <a name="use-serial-control"></a>Použití sériového portu ovládacího prvku

1. Připojte se k [sériové konzoly a otevřené instance CMD](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Pokud konzole sériového portu není povolená na virtuálním počítači, přečtěte si téma [resetování síťové rozhraní](reset-network-interface.md).
2. Zaškrtněte, pokud služba DHCP je zakázána na síťovém rozhraní:

        sc query DHCP
3. Pokud je služba DHCP zastavená, zkuste spustit službu

        sc start DHCP

4. Dotazování na službu znovu, abyste měli jistotu, že je služba úspěšně spuštěna.

        sc query DHCP

    Zkuste se připojit k virtuálnímu počítači a zobrazit, pokud se problém vyřeší.
5. Pokud služba nespustí, použijte následující vhodné řešení na základě chybové zprávy, který jste obdrželi:

    | Chyba  |  Řešení |
    |---|---|
    | 5 - PŘÍSTUP BYL ODEPŘEN  | Zobrazit [klientská služba DHCP zastavená chyba přístup odepřen](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Zobrazit [služba Klient DHCP selže nebo přestane reagovat](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 - ERROR_SERVICE_DISABLED  | Zobrazit [zakázána služba Klient DHCP](#dhcp-client-service-is-disabled).  |
    | . 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychlé vyřešení problému.   |
    | 1067 - ERROR_PROCESS_ABORTED |Zobrazit [služba Klient DHCP selže nebo přestane reagovat](#dhcp-client-service-crashes-or-hangs).   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychlé vyřešení problému.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Zobrazit [služba Klient DHCP selže z důvodu selhání přihlášení](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Zobrazit [služba Klient DHCP selže nebo přestane reagovat](#dhcp-client-service-crashes-or-hangs).  |
    | 1077. - ERROR_SERVICE_NEVER_STARTED  | Zobrazit [zakázána služba Klient DHCP](#dhcp-client-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychlé vyřešení problému.  |
    |1053 | [Obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychlé vyřešení problému.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>Klientská služba DHCP zastavená chyba přístup odepřen

1. Připojte se k [konzoly sériového portu](serial-console-windows.md) a otevřete instance prostředí PowerShell.
2. Stáhněte si nástroj pro monitorování procesu spuštěním následujícího skriptu:

   ```
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Začít **procmon** trasování:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Reprodukujte problém spuštěním služby, která generuje **přístup odepřen** zpráva:

   ```
   sc start DHCP
   ```

   Pokud se nezdaří, ukončete monitorování procesu trasování:

   ```
   procmon /Terminate
   ```
5. Shromažďování **c:\temp\ProcMonTrace.PML** souboru:

    1. [Připojení datového disku k virtuálnímu počítači](../windows/attach-managed-disk-portal.md
).
    2. Pomocí konzoly sériového portu můžete zkopírovat soubor na nový disk. Například, `copy C:\temp\ProcMonTrace.PML F:\`. V tomto příkazu F je ovladač písmeno přídavný datový disk. Písmeno podle potřeby nahraďte správnou hodnotu.
    3. Odpojit datový disk a připojit ho k funkční virtuální počítač, který má nainstalovaný ubstakke monitorování procesu.

6. Otevřít **ProcMonTrace.PML** pomocí monitorování procesu ve funkčním virtuálním počítači. Potom vyfiltrovat **výsledkem je přístup ODEPŘEN**, jak je znázorněno na následujícím snímku obrazovky:

    ![Filtrovat podle výsledku v monitorování procesu](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Opravte klíče registru, složky nebo soubory, které jsou na výstupu. Tento problém je obvykle nastává, když přihlašovací účet, který se používá ve službě nemá oprávnění seznamu ACL pro přístup k těmto objektům. K určení správné oprávnění seznamu ACL pro přihlašovací účet, můžete zkontrolovat na virtuálním počítači v pořádku.

#### <a name="dhcp-client-service-is-disabled"></a>Služba Klient DHCP je zakázána.

1. Služba obnovíte jeho výchozí hodnota při spuštění:

   ```
   sc config DHCP start= auto
   ```

2. Spusťte službu:

   ```
   sc start DHCP
   ```

3. Dotaz na stav služby znovu a ujistěte se, zda je spuštěná:

   ```
   sc query DHCP
   ```

4. Pokuste se připojit k virtuálnímu počítači pomocí vzdálené plochy.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>Služba Klient DHCP selže z důvodu selhání přihlášení

1. Protože k tomuto problému dochází, pokud byl změněn účet při spuštění této služby, vrácení účtu k jeho výchozí stav:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Spusťte službu:

        sc start DHCP
3. Pokuste se připojit k virtuálnímu počítači pomocí vzdálené plochy.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>Služba Klient DHCP selže nebo přestane reagovat
1. Pokud stav služby se zasekla v automatickém **počáteční** nebo **zastavení** stavu, zkuste zastavit službu:

        sc stop DHCP
2. Izolace službu na kontejneru "svchost":

        sc config DHCP type= own
3. Spusťte službu:

        sc start DHCP
4. Pokud službu stále nespustí, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Opravte virtuální počítač v režimu offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojte disk s operačním systémem pro virtuální počítač pro obnovení

1. [Připojte disk s operačním systémem pro virtuální počítač pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
2. Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení. Ujistěte se, že je připojený disk označený jako **Online** v konzole Správa disků. Poznamenejte si písmeno jednotky, která je přiřazena připojeném disku s operačním systémem.
3.  Otevřete příkazový řádek se zvýšenými oprávněními instance (**spustit jako správce**). Potom spusťte následující skript. Tento skript předpokládá, že je písmeno jednotky, která je přiřazena připojeném disku s operačním systémem **F**. Písmeno podle potřeby nahraďte hodnotou ve virtuálním počítači.

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

4. [Odpojit disk s operačním systémem a znovu vytvořte virtuální počítač](../windows/troubleshoot-recovery-disks-portal.md). Zkontrolujte, zda byl problém vyřešen.

## <a name="next-steps"></a>Další postup

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat váš problém vyřešit.


