---
title: Vzdálená plocha nespouští na Virtuálním počítači Azure | Dokumentace Microsoftu
description: Zjistěte, jak při řešení problémů se vzdálenou plochou při připojení k virtuálnímu počítači | Dokumentace Microsoftu
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
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: a9967aec61aaab5bc6b4517407f36e2a6c7342c8
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238858"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Vzdálená plocha nespouští na Virtuálním počítači Azure

Tento článek popisuje postup řešení potíží při služby Vzdálená plocha (inicializace) se nepouští nebo se nepodaří spustit připojování do virtuálního počítače (virtuální počítač Azure).

>[!NOTE]
>Azure nabízí dva různé modely nasazení pro vytváření a práci s prostředky: [nástroj Resource Manager a klasický režim](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek popisuje pomocí modelu nasazení Resource Manager. Doporučujeme použít tento model pro nových nasazení namísto pomocí modelu nasazení classic.

## <a name="symptoms"></a>Příznaky

Při pokusu o připojení k virtuálnímu počítači se vyskytnout následující scénáře:

- Snímek obrazovky virtuálního počítače ukazuje, že je operační systém plně načtený a čeká se přihlašovací údaje.

    ![Snímek obrazovky se stav tohoto virtuálního počítače](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Můžete vzdáleně Zkontrolujte protokoly událostí ve virtuálním počítači s použitím prohlížeče události, uvidíte, že není od nebo selhání spuštění služby Vzdálená plocha (TermServ). Následuje ukázkový protokol:

    **Název protokolu**: systému </br>
    **Zdroj**: správce řízení služeb </br>
    **Datum**: 12/16/2017 11:19:36 AM</br>
    **ID události**: 7022</br>
    **Úloha kategorie**: žádné</br>
    **Úroveň**: Chyba</br>
    **Klíčová slova**: Classic</br>
    **Uživatel**: není k dispozici</br>
    **Počítač**: vm.contoso.com</br>
    **Popis**: služby Vzdálená plocha služby přestala během spouštění reagovat. 

    Můžete také použít funkci konzoly sériového portu přístup k vyhledání těchto chyb pomocí následujícího dotazu: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Příčina
 
K tomuto problému dochází, protože na virtuálním počítači není spuštěná služba Vzdálená plocha. Příčiny mohou záviset na následující scénáře: 

- Inicializace služby je nastaven na **zakázané**. 
- Služba inicializace padá nebo přestanou reagovat. 

## <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, použijte konzole sériového portu nebo [opravte virtuální počítač v režimu offline](#repair-the-vm-offline) připojením disku s operačním systémem virtuálního počítače na virtuální počítač pro obnovení.

### <a name="use-serial-console"></a>Použití konzoly sériového portu

1. Přístup [konzoly sériového portu](serial-console-windows.md) tak, že vyberete **podpora a řešení potíží** > **konzoly sériového portu**. Pokud je povolená funkce, na virtuálním počítači, může úspěšně připojit virtuální počítač.

2. Vytvořte nový kanál pro instanci CMD. Typ **CMD** spuštění kanálu se získat název kanálu.

3. Přepněte na kanál spuštěnou instanci CMD. V tomto případě je třeba kanál 1.

   ```
   ch -si 1
   ```

4. Stisknutím klávesy **Enter** znovu a zadejte platné uživatelské jméno a heslo (místní nebo doménový ID) pro virtuální počítač.

5. Dotaz na stav služby inicializace.

   ```
   sc query TermService
   ```

6. Pokud se zobrazí stav služby **Zastaveno**, pokuste se spustit službu.

    ```
    sc start TermService
     ``` 

7. Dotazování na službu znovu, abyste měli jistotu, že je služba úspěšně spuštěna.

   ```
   sc query TermService
   ```
    Pokud se službu nepodaří spustit, postupujte podle řešení založené na chybu, kterou jste obdrželi:

    |  Chyba |  Návrh |
    |---|---|
    |5 - PŘÍSTUP BYL ODEPŘEN |Zobrazit [inicializace je zastavena kvůli chybě přístup odepřen](#termService-service-is-stopped-because-of-access-denied-error) |
    |1058 - ERROR_SERVICE_DISABLED  |Zobrazit [inicializace služba je zakázána.](#termService-service-is-disabled)  |
    |. 1059 - ERROR_CIRCULAR_DEPENDENCY |[Obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému|
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |[Obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému    |
    |1070 - ERROR_SERVICE_START_HANG   | [Obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému  |
    |1077. - ERROR_SERVICE_NEVER_STARTED   | Zobrazit [inicializace služba je zakázána.](#termService-service-is-disabled)  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému |
    |1753   |[Obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému   |

#### <a name="termservice-service-is-stopped-because-of-access-denied-error"></a>Inicializace je zastavena kvůli chybě přístup odepřen

1. Připojte se k [konzoly sériového portu](serial-console-windows.md#) a otevřete instance prostředí PowerShell.
2. Stáhněte si nástroj pro monitorování procesu spuštěním následujícího skriptu:

        remove-module psreadline  
        $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
        $destination = "c:\temp\ProcessMonitor.zip" 
        $wc = New-Object System.Net.WebClient 
        $wc.DownloadFile($source,$destination) 
3. Nyní spusťte procmon trasování:

        procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
4. Reprodukci problému spuštěním služba, která poskytuje přístup k odepření: 

        sc start TermService 
        
    Pokud došlo k selhání, pokračujte a ukončit monitorování procesu trasování:

        procmon /Terminate 
5. Shromáždit soubor **c:\temp\ProcMonTrace.PML**, otevřete ho pomocí procmon a potom vyfiltrovat **výsledkem je přístup ODEPŘEN** jako na následujícím snímku obrazovky se zobrazí:

    ![Filtrovat podle výsledku v monitorování procesu](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Opravte klíče registru, složky nebo soubory, které jsou na výstupu. Tento problém je obvykle způsobeno protokolu na účet používá ve službě nemáte příslušná oprávnění seznamu ACL pro přístup k těmto objektům. Vědět správné oprávnění seznamu ACL pro přihlašovací účet, můžete zkontrolovat na virtuálním počítači v pořádku. 

#### <a name="termservice-service-is-disabled"></a>Inicializace služba je zakázána.

1.  Služba obnovíte jeho výchozí hodnota při spuštění:

        sc config TermService start= demand 
        
2.  Spusťte službu:

        sc start TermService 
3.  Dotaz na její stav znovu zajistit se službou: sc dotazu inicializace 
4.  Zkuste conntet k virtuálnímu počítači pomocí vzdálené plochy.


### <a name="repair-the-vm-offline"></a>Opravte virtuální počítač v režimu offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Připojte disk s operačním systémem pro virtuální počítač pro obnovení

1. [Připojte disk s operačním systémem pro virtuální počítač pro obnovení](../windows/troubleshoot-recovery-disks-portal.md).
2. Spusťte připojení ke vzdálené ploše pro virtuální počítač pro obnovení. Ujistěte se, že je připojený disk označený jako **Online** v konzole Správa disků. Poznamenejte si písmeno jednotky, která je přiřazena připojeném disku s operačním systémem.
3.  Otevřete příkazový řádek se zvýšenými oprávněními instance (**spustit jako správce**), a pak spusťte následující skript. Předpokládáme, že je písmeno jednotky, která je přiřazena připojeném disku s operačním systémem F. nahradit ji odpovídající hodnotou ve virtuálním počítači. 

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
        REM Set default values back on the broken service 
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
4. [Odpojit disk s operačním systémem a znovu vytvořte virtuální počítač](../windows/troubleshoot-recovery-disks-portal.md)a potom zkontrolujte, zda byl problém vyřešen.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
