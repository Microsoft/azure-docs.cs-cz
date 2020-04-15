---
title: Agent synchronizace dat pro synchronizaci dat SQL
description: Zjistěte, jak nainstalovat a spustit agenta synchronizace dat pro Azure SQL Data Sync pro synchronizaci dat s místními databázemi SQL Serveru
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 39471ebded6280e7d394ee69c2d732b779c9ea50
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380914"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Agent synchronizace dat pro synchronizaci dat Azure SQL

Synchronizujte data s místními databázemi SQL Serveru instalací a konfigurací agenta synchronizace dat pro Azure SQL Data Sync. Další informace o synchronizaci dat SQL najdete v [tématu Synchronizace dat ve více cloudových a místních databázích pomocí synchronizace dat SQL](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync v tuto chvíli **nepodporuje** spravovanou instanci Azure SQL Database.

## <a name="download-and-install"></a>Stažení a instalace

Pokud chcete agenta synchronizace dat stáhnout, přejděte na [SQL Azure Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Instalace tiše

Chcete-li agenta synchronizace dat nainstalovat tiše z příkazového řádku, zadejte příkaz podobný následujícímu příkladu. Zkontrolujte název staženého souboru MSI a zadejte vlastní hodnoty pro argumenty **TARGETDIR** a **SERVICEACCOUNT.**

- Pokud nezadáte hodnotu pro **TARGETDIR**, výchozí `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`hodnota je .

- Pokud zadáte `LocalSystem` jako hodnotu **SERVICEACCOUNT**, použijte sql server ověřování při konfiguraci agenta pro připojení k místnímu serveru SQL Server.

- Pokud zadáte uživatelský účet domény nebo místní uživatelský účet jako hodnotu **SERVICEACCOUNT**, budete muset také zadat heslo s **SERVICEPASSWORD** argument. Například, `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Synchronizace dat s místním SQL Serverem

Pokud chcete nakonfigurovat agenta synchronizace dat tak, abyste mohli synchronizovat data s jednou nebo více místními databázemi serveru SQL Server, přečtěte si informace [o přidání místní databáze serveru SQL Server](sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="data-sync-agent-faq"></a><a name="agent-faq"></a>Nejčastější dotazy k agentovi synchronizace dat

### <a name="why-do-i-need-a-client-agent"></a>Proč potřebuji klientského agenta

Služba SQL Data Sync komunikuje s databázemi serveru SQL Server prostřednictvím klientského agenta. Tato funkce zabezpečení zabraňuje přímé komunikaci s databázemi za bránou firewall. Když služba SQL Data Sync komunikuje s agentem, provádí tak pomocí šifrovaných připojení a jedinečného tokenu nebo *klíče agenta*. Databáze serveru SQL Server ověřují agenta pomocí připojovacího řetězce a klíče agenta. Tento návrh poskytuje vysokou úroveň zabezpečení dat.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Kolik instancí místního agenta ui lze spustit

Lze spustit pouze jednu instanci ui.

### <a name="how-can-i-change-my-service-account"></a>Jak mohu změnit svůj účet služby

Po instalaci klientského agenta je jediným způsobem, jak změnit účet služby, odinstalovat jej a nainstalovat nového agenta klienta s novým účtem služby.

### <a name="how-do-i-change-my-agent-key"></a>Jak změním klíč agenta

Klíč agenta může agent použít pouze jednou. Nelze jej znovu použít při odebrání a přeinstalaci nového agenta, ani jej nelze použít více agenty. Pokud potřebujete vytvořit nový klíč pro existujícího agenta, musíte si být jisti, že stejný klíč je zaznamenán s agentem klienta a se službou SQL Data Sync.

### <a name="how-do-i-retire-a-client-agent"></a>Jak lze odejít do důchodu klientského agenta

Chcete-li okamžitě zrušit platnost nebo vyřadit agenta, regenerovat jeho klíč na portálu, ale neodesílejte jej v agent ui. Obnovení klíče zruší platnost předchozího klíče bez ohledu na to, zda je odpovídající agent online nebo offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Jak lze přesunout agenta klienta do jiného počítače

Pokud chcete spustit místního agenta z jiného počítače, než je aktuálně zapnuto, proveďte následující akce:

1. Nainstalujte agenta do požadovaného počítače.
2. Přihlaste se k portálu synchronizace dat SQL a znovu vygenerujte klíč agenta pro nového agenta.
3. Pomocí nového agenta ui odeslat nový klíč agenta.
4. Počkejte, než agent klienta stáhne seznam místních databází, které byly zaregistrovány dříve.
5. Zadejte pověření databáze pro všechny databáze, které se zobrazí jako nedostupné. Tyto databáze musí být dosažitelné z nového počítače, ve kterém je agent nainstalován.

## <a name="troubleshoot-data-sync-agent-issues"></a><a name="agent-tshoot"></a>Poradce při potížích s agentem synchronizace dat

- [Instalace, odinstalace nebo oprava agenta klienta se nezdaří.](#agent-install)

- [Agent klienta po zrušení odinstalace nefunguje](#agent-uninstall)

- [Moje databáze není uvedena v seznamu agentů](#agent-list)

- [Agent klienta se nespustí (chyba 1069)](#agent-start)

- [Nemohu odeslat klíč agenta](#agent-key)

- [Agenta klienta nelze odstranit z portálu, pokud je jeho přidružená místní databáze nedostupná.](#agent-delete)

- [Aplikace Místní synchronizační agent se nemůže připojit k místní synchronizační službě](#agent-connect)

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a><a name="agent-install"></a>Instalace, odinstalace nebo oprava agenta klienta se nezdaří.

- **Příčina**. Mnoho scénářů může způsobit toto selhání. Chcete-li zjistit konkrétní příčinu této chyby, podívejte se na protokoly.

- **Rozlišení**. Chcete-li zjistit konkrétní příčinu selhání, vygenerujte a podívejte se na protokoly Instalační služby systému Windows. Protokolování můžete zapnout na příkazovém řádku. Pokud je `SQLDataSyncAgent-2.0-x86-ENU.msi`například stažený instalační soubor , vygenerujte a prohlédněte soubory protokolu pomocí následujících příkazových řádků:

  - Pro instalace:`msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Pro odinstalace:`msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Protokolování můžete zapnout také pro všechny instalace prováděné Instalační službou systému Windows. Článek znalostní báze Microsoft Knowledge Base [Jak povolit protokolování Instalační služby systému Windows](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) poskytuje řešení jedním kliknutím, které umožňuje zapnout protokolování pro Instalační službu systému Windows. Poskytuje také umístění protokolů.

### <a name="the-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a><a name="agent-uninstall"></a>Agent klienta po zrušení odinstalace nefunguje

Agent klienta nefunguje, a to i po zrušení jeho odinstalace.

- **Příčina**. K tomu dochází, protože agent klienta SQL Data Sync neukládá pověření.

- **Rozlišení**. Můžete vyzkoušet tyto dvě řešení:

    -   Pomocí souboru services.msc znovu zadejte pověření pro agenta klienta.
    -   Odinstalujte tohoto klientského agenta a nainstalujte nového. Stáhněte a nainstalujte nejnovějšího klientského agenta ze [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="my-database-isnt-listed-in-the-agent-list"></a><a name="agent-list"></a>Moje databáze není uvedena v seznamu agentů

Při pokusu o přidání existující databáze serveru SQL Server do skupiny synchronizace databáze se nezobrazí v seznamu agentů.

Tyto scénáře může způsobit tento problém:

- **Příčina**. Agent klienta a skupina synchronizace jsou v různých datových centrech.

- **Rozlišení**. Agent klienta a skupina synchronizace musí být ve stejném datovém centru. Chcete-li toto nastavení nastavit, máte dvě možnosti:

    -   Vytvořte nového agenta v datovém centru, kde se nachází skupina synchronizace. Potom zaregistrujte databázi s tímto agentem.
    -   Odstraňte aktuální skupinu synchronizace. Potom znovu vytvořte skupinu synchronizace v datovém centru, kde se nachází agent.

- **Příčina**. Seznam databází agenta klienta není aktuální.

- **Rozlišení**. Zastavte a restartujte službu agenta klienta.

    Místní agent stáhne seznam přidružených databází pouze při prvním odeslání klíče agenta. Nestahuje seznam přidružených databází na následné odeslání klíče agenta. Databáze, které jsou registrovány během přesunu agenta, se nezobrazují v původní instanci agenta.

### <a name="client-agent-doesnt-start-error-1069"></a><a name="agent-start"></a>Agent klienta se nespustí (chyba 1069)

Zjistíte, že agent není spuštěn v počítači, který je hostitelem SQL Server. Při pokusu o ruční spuštění agenta se zobrazí dialogové okno se zprávou "Chyba 1069: Služba nebyla zahájena z důvodu selhání přihlášení."

![Dialogové okno Chyba synchronizace dat 1069](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Příčina**. Pravděpodobnou příčinou této chyby je, že heslo na místním serveru se změnilo od vytvoření hesla agenta a agenta.

- **Rozlišení**. Aktualizujte heslo agenta na aktuální heslo serveru:

  1. Vyhledejte službu agenta klienta SYNCHRONIZACE DAT SQL.  
    a. Vyberte **Spustit**.  
    b. Do vyhledávacího pole zadejte **services.msc**.  
    c. Ve výsledcích hledání vyberte **položku Služby**.  
    d. V okně **Služby** přejděte k položce **agenta synchronizace dat SQL**.  
  1. Klepněte pravým tlačítkem myši na **příkaz Agent synchronizace dat SQL**a vyberte příkaz **Zastavit**.
  1. Klepněte pravým tlačítkem myši na **příkaz Agent synchronizace dat SQL**a vyberte příkaz **Vlastnosti**.
  1. Ve **službě Vlastnosti agenta synchronizace dat SQL**vyberte kartu **Přihlásit.**
  1. Do pole **Heslo** zadejte heslo.
  1. Do pole **Potvrdit heslo** znovu zadejte heslo.
  1. Vyberte **Apply** (Použít) a pak vyberte **OK**.
  1. V okně **Služby** klikněte pravým tlačítkem myši na službu **AGENT SYNCHRONIZACE DAT SQL** a potom klikněte na tlačítko **Start**.
  1. Zavřete okno **Služby.**

### <a name="i-cant-submit-the-agent-key"></a><a name="agent-key"></a>Nemohu odeslat klíč agenta

Po vytvoření nebo opětovném vytvoření klíče pro agenta se pokusíte odeslat klíč prostřednictvím aplikace SqlAzureDataSyncAgent. Odeslání se nedokončí.

![Dialogové okno Chyba synchronizace – nelze odeslat klíč agenta](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Požadavky**. Než budete pokračovat, zkontrolujte následující požadavky:

  - Je spuštěna služba SQL Data Sync Windows.

  - Účet služby pro službu SQL Data Sync Windows má přístup k síti.

  - V místním pravidle brány firewall je otevřen výstupní port 1433.

  - Místní adresa IP je přidána do pravidla brány firewall serveru nebo databáze pro databázi metadat synchronizace.

- **Příčina**. Klíč agenta jednoznačně identifikuje každého místního agenta. Klíč musí splňovat dvě podmínky:

  -   Klíč agenta klienta na serveru SQL Data Sync a místním počítači musí být shodný.
  -   Klíč agenta klienta lze použít pouze jednou.

- **Rozlišení**. Pokud váš agent nefunguje, je to proto, že jedna nebo obě tyto podmínky nejsou splněny. Chcete-li, aby váš agent opět pracoval:

  1. Vygenerujte nový klíč.
  1. Použijte nový klíč agenta.

  Použití nového klíče agenta:

  1. V Průzkumníkovi souborů přejděte do instalačního adresáře agenta. Výchozí instalační adresář je\\C: Program Files\\(x86) Microsoft SQL Data Sync.
  1. Poklepejte na podadresář přihrádky.
  1. Sem otevřete aplikaci SqlAzureDataSyncAgent.
  1. Vyberte **příkaz Odeslat klíč agenta**.
  1. Do poskytnutého místa vložte klíč ze schránky.
  1. Vyberte **OK**.
  1. Zavřete program.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a><a name="agent-delete"></a>Agenta klienta nelze odstranit z portálu, pokud je jeho přidružená místní databáze nedostupná.

Pokud se místní koncový bod (tj. databáze), který je registrován u klientského agenta synchronizace dat SQL, stane nedostupným, nelze odstranit agenta klienta.

- **Příčina**. Místního agenta nelze odstranit, protože nedostupná databáze je stále registrována u agenta. Při pokusu o odstranění agenta se proces odstranění pokusí dostat do databáze, která se nezdaří.

- **Rozlišení**. K odstranění nedostupné databáze použijte "vynucené odstranění".

> [!NOTE]
> Pokud synchronizační tabulky metadat zůstanou po `deprovisioningutil.exe` "vynuceném odstranění", použijte je k jejich vyčištění.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a><a name="agent-connect"></a>Aplikace Místní synchronizační agent se nemůže připojit k místní synchronizační službě

- **Rozlišení**. Vyzkoušejte následující kroky:

  1. Ukončete aplikaci.  
  1. Otevřete panel Služby komponent.  
    a. Do vyhledávacího pole na hlavním panelu zadejte **services.msc**.  
    b. Ve výsledcích hledání poklikejte na **položku Služby**.  
  1. Zastavte službu **synchronizace dat SQL.**
  1. Restartujte službu **SYNCHRONIZACE dat SQL.**  
  1. Znovu tuto aplikaci otevřít.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Spuštění agenta synchronizace dat z příkazového řádku

Z příkazového řádku můžete spustit následující příkazy Agent a agenta synchronizace dat:

### <a name="ping-the-service"></a>Příkaz ping na službu

#### <a name="usage"></a>Využití

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Příklad

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Zobrazit registrované databáze

#### <a name="usage"></a>Využití

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Příklad

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>Odeslat klíč agenta

#### <a name="usage"></a>Využití

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Příklad

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Registrace databáze

#### <a name="usage"></a>Využití

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Příklady

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Zrušení registrace databáze

Při použití tohoto příkazu zrušit registraci databáze, zruší zřízení databáze úplně. Pokud se databáze účastní jiných skupin synchronizace, tato operace přeruší ostatní skupiny synchronizace.

#### <a name="usage"></a>Využití

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>Příklad

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>Aktualizace přihlašovacích údajů

#### <a name="usage"></a>Využití

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Příklady

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>Další kroky

Další informace o synchronizaci dat SQL najdete v následujících článcích:

-   Přehled – [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Azure SQL Data Sync](sql-database-sync-data.md)
-   Nastavení synchronizace dat
    - Na portálu – [kurz: Nastavení synchronizace dat SQL pro synchronizaci dat mezi databází Azure SQL a SQL Server em i v místním prostředí](sql-database-get-started-sql-data-sync.md)
    - S využitím PowerShellu
        -  [Synchronizace mezi několika databázemi Azure SQL pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Doporučené postupy – [doporučené postupy pro Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Monitor – [monitorování synchronizace dat SQL pomocí protokolů Azure Monitoru](sql-database-sync-monitor-oms.md)
-   Poradce při potížích – [řešení problémů se synchronizací dat Azure SQL](sql-database-troubleshoot-data-sync.md)
-   Aktualizace schématu synchronizace
    -   S Transact-SQL – [automatizace replikace změn schématu v Synchronizaci dat Azure SQL](sql-database-update-sync-schema.md)
    -   S PowerShellem – [k aktualizaci schématu synchronizace v existující skupině synchronizace použijte PowerShell](scripts/sql-database-sync-update-schema.md)
