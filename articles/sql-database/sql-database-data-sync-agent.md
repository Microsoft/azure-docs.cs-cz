---
title: Agent synchronizace dat pro Azure Synchronizace dat SQL
description: Přečtěte si, jak nainstalovat a spustit agenta synchronizace dat pro Azure Synchronizace dat SQL k synchronizaci dat s místními SQL Server databázemi.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 097c8547093a13f73f1ae5facdc0f7e6c75c071d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690786"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Agent synchronizace dat pro Azure Synchronizace dat SQL

Synchronizujte data s místními SQL Server databáze tak, že nainstalujete a nakonfigurujete agenta synchronizace dat pro Azure Synchronizace dat SQL. Další informace o Synchronizace dat SQL najdete v tématu [synchronizace dat napříč více cloudy a místními databázemi pomocí synchronizace dat SQL](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure Synchronizace dat SQL v tuto **chvíli nepodporuje spravovanou** instanci Azure SQL Database.

## <a name="download-and-install"></a>Stáhnout a nainstalovat

Chcete-li stáhnout agenta synchronizace dat, klikněte na [SQL Azure agenta synchronizace dat](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Bezobslužná instalace

K tiché instalaci agenta synchronizace dat z příkazového řádku zadejte příkaz podobný následujícímu příkladu. Ověřte název souboru staženého souboru. msi a zadejte vlastní hodnoty pro argumenty **targetDir** a **SERVICEACCOUNT** .

- Pokud nezadáte hodnotu pro **targetDir**, výchozí hodnota je `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`.

- Pokud zadáte `LocalSystem` jako hodnotu **SERVICEACCOUNT**, použijte ověřování SQL Server, když nakonfigurujete agenta pro připojení k místnímu SQL Server.

- Pokud jako hodnotu **SERVICEACCOUNT**zadáte účet uživatele domény nebo místní uživatelský účet, musíte zadat také heslo s argumentem **SERVICEPASSWORD** . například `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Synchronizace dat s SQL Server v místním prostředí

Pokud chcete nakonfigurovat agenta pro synchronizaci dat, abyste mohli synchronizovat data s jednou nebo více místními databázemi SQL Server, přečtěte si téma [Přidání místní databáze SQL Server](sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="agent-faq"></a>Nejčastější dotazy k agentovi synchronizace dat

### <a name="why-do-i-need-a-client-agent"></a>Proč potřebuji klientského agenta

Služba Synchronizace dat SQL komunikuje s SQL Server databázemi prostřednictvím klientského agenta. Tato funkce zabezpečení zabraňuje přímé komunikaci s databázemi za bránou firewall. Když služba Synchronizace dat SQL komunikuje s agentem, použije šifrované připojení a jedinečný token nebo *klíč agenta*. Databáze SQL Server ověřují agenta pomocí připojovacího řetězce a klíče agenta. Tento návrh poskytuje pro vaše data vysokou úroveň zabezpečení.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Kolik instancí uživatelského rozhraní místního agenta lze spustit

Lze spustit pouze jednu instanci uživatelského rozhraní.

### <a name="how-can-i-change-my-service-account"></a>Jak můžu změnit účet služby

Když nainstalujete klientského agenta, jediný způsob, jak změnit účet služby, je odinstalování a instalace nového agenta klienta s novým účtem služby.

### <a name="how-do-i-change-my-agent-key"></a>Návody změnit svůj klíč agenta

Klíč agenta může použít jenom jeden agent. Nejde ho znovu použít, když odeberete a pak znovu nainstalujete nového agenta, ani ho nebude moct používat víc agentů. Pokud potřebujete vytvořit nový klíč pro existujícího agenta, je nutné zajistit, aby byl stejný klíč zaznamenán pomocí agenta klienta a služby Synchronizace dat SQL.

### <a name="how-do-i-retire-a-client-agent"></a>Vyřazení klientského agenta Návody

Pro okamžité zrušení platnosti nebo vyřazení agenta, znovu vygenerujte svůj klíč na portálu, ale neodešlete ho v uživatelském rozhraní agenta. Opětovné generování klíče zruší platnost předchozího klíče bez ohledu na to, jestli je odpovídající agent online nebo offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Návody přesun agenta klienta na jiný počítač

Pokud chcete spustit místního agenta z jiného počítače, než který je v současné době zapnutý, proveďte následující akce:

1. Nainstalujte agenta na požadovaný počítač.
2. Přihlaste se k portálu Synchronizace dat SQL a znovu vygenerujte klíč agenta pro nového agenta.
3. K odeslání nového klíče agenta použijte uživatelské rozhraní nového agenta.
4. Počkejte, než klientský Agent stáhne seznam místních databází, které byly zaregistrovány dříve.
5. Zadejte přihlašovací údaje databáze pro všechny databáze, které se zobrazují jako nedostupné. Tyto databáze musí být dosažitelné z nového počítače, na kterém je nainstalovaný agent.

## <a name="agent-tshoot"></a>Řešení potíží s agentem synchronizace dat

- [Instalace, odinstalace nebo oprava klientského agenta se nezdařila](#agent-install)

- [Po zrušení odinstalace nefunguje agent klienta](#agent-uninstall)

- [Moje databáze není uvedená v seznamu agentů](#agent-list)

- [Agent klienta se nespustí (chyba 1069)](#agent-start)

- [Nemůžu odeslat klíč agenta](#agent-key)

- [Agenta klienta nelze odstranit z portálu, pokud je přidružená místní databáze nedosažitelná.](#agent-delete)

- [Místní aplikace agenta synchronizace se nemůže připojit k místní službě synchronizace.](#agent-connect)

### <a name="agent-install"></a>Instalace, odinstalace nebo oprava klientského agenta se nezdařila

- **Příčina**: Tato chyba může způsobovat mnoho scénářů. Pokud chcete zjistit konkrétní příčinu této chyby, podívejte se do protokolů.

- **Řešení**. Chcete-li zjistit konkrétní příčinu selhání, vygenerujte a podívejte se na protokoly Instalační služba systému Windows. Protokolování můžete zapnout na příkazovém řádku. Pokud je například stažený instalační soubor `SQLDataSyncAgent-2.0-x86-ENU.msi`, vygenerujte a prověřte soubory protokolu pomocí následujících příkazových řádků:

  - Pro instalaci: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Pro odinstalaci: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Můžete také zapnout protokolování pro všechny instalace, které provádí Instalační služba systému Windows. Článek znalostní báze Microsoft Knowledge Base, [Jak povolit protokolování Instalační služba systému Windows](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) , nabízí řešení jedním kliknutím pro zapnutí protokolování pro instalační služba systému Windows. Poskytuje také umístění protokolů.

### <a name="agent-uninstall"></a>Po zrušení odinstalace nefunguje agent klienta

Agent klienta nefunguje, i když zrušíte odinstalaci.

- **Příčina**: K tomu dochází, protože agent klienta Synchronizace dat SQL neukládá přihlašovací údaje.

- **Řešení**. Můžete si vyzkoušet tato dvě řešení:

    -   Pomocí služby Services. msc znovu zadejte přihlašovací údaje pro klientského agenta.
    -   Odinstalujte tohoto agenta klienta a pak nainstalujte nové. Stáhněte si a nainstalujte nejnovějšího klientského agenta z [webu Download Center](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="agent-list"></a>Moje databáze není uvedená v seznamu agentů

Při pokusu o přidání existující databáze SQL Server do skupiny synchronizace se databáze nezobrazí v seznamu agentů.

Příčinou těchto scénářů může být tento problém:

- **Příčina**: Agent klienta a skupina synchronizace jsou v různých datových centrech.

- **Řešení**. Agent klienta a skupina synchronizace musí být ve stejném datovém centru. Chcete-li nastavit tuto možnost, máte dvě možnosti:

    -   Vytvořte nového agenta v datacentru, kde se nachází skupina synchronizace. Pak zaregistrujte databázi pomocí tohoto agenta.
    -   Odstraní aktuální skupinu synchronizace. Pak znovu vytvořte skupinu synchronizace v datacentru, kde se nachází agent.

- **Příčina**: Seznam databází agenta klienta není aktuální.

- **Řešení**. Zastavte a poté restartujte službu agenta klienta.

    Místní agent stáhne seznam přidružených databází pouze při prvním odeslání klíče agenta. Nestahuje seznam přidružených databází u dalších odeslání klíčů agenta. Databáze, které jsou zaregistrované během přesunu agenta, se nezobrazují v původní instanci agenta.

### <a name="agent-start"></a>Agent klienta se nespustí (chyba 1069)

Zjistíte, že agent neběží na počítači, který je hostitelem SQL Server. Při pokusu o ruční spuštění agenta se zobrazí dialogové okno, ve kterém se zobrazí zpráva "Error 1069: služba nebyla spuštěna z důvodu neúspěšného přihlášení".

![Dialogové okno Chyba synchronizace dat 1069](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Příčina**: Pravděpodobnou příčinou této chyby je, že se heslo na místním serveru od vytvoření agenta a hesla agenta změnilo.

- **Řešení**. Aktualizujte heslo agenta na aktuální heslo serveru:

  1. Vyhledejte Synchronizace dat SQL službu agenta klienta.  
    a. Vyberte **Spustit**.  
    b. Do vyhledávacího pole zadejte **Services. msc**.  
    c. Ve výsledcích hledání vyberte **služby**.  
    d. V okně **služby** se posuňte na položku **synchronizace dat SQL agenta**.  
  1. Pravým tlačítkem myši klikněte na **synchronizace dat SQL Agent**a pak vyberte **zastavit**.
  1. Pravým tlačítkem myši klikněte na **synchronizace dat SQL Agent**a pak vyberte **vlastnosti**.
  1. Na **synchronizace dat SQL vlastnosti agenta**vyberte kartu **Přihlásit** se.
  1. Do pole **heslo** zadejte své heslo.
  1. Do pole **Potvrdit heslo** zadejte znovu heslo.
  1. Vyberte **Apply** (Použít) a pak vyberte **OK**.
  1. V okně **služby** klikněte pravým tlačítkem na službu **agenta synchronizace dat SQL** a potom klikněte na **Spustit**.
  1. Zavřete okno **služby** .

### <a name="agent-key"></a>Nemůžu odeslat klíč agenta

Po vytvoření nebo opětovném vytvoření klíče pro agenta se pokusíte odeslat klíč prostřednictvím aplikace SqlAzureDataSyncAgent. Dokončení odesílání se nezdařilo.

![Dialogové okno chyby synchronizace – nepovedlo se odeslat klíč agenta.](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Požadavky**: Než budete pokračovat, ověřte následující požadavky:

  - Služba Synchronizace dat SQL Windows je spuštěná.

  - Účet služby pro Synchronizace dat SQL služba systému Windows má přístup k síti.

  - Port pro odchozí 1433 je v místním pravidle brány firewall otevřený.

  - Místní IP adresa se přidá do pravidla brány firewall serveru nebo databáze pro databázi metadat synchronizace.

- **Příčina**: Klíč agenta jednoznačně identifikuje každého místního agenta. Klíč musí splňovat dvě podmínky:

  -   Klíč agenta klienta na serveru Synchronizace dat SQL a v místním počítači musí být identický.
  -   Klíč agenta klienta lze použít pouze jednou.

- **Řešení**. Pokud Váš agent nefunguje, je to proto, že jedna nebo obě tyto podmínky nejsou splněné. Postup opětovného fungování agenta:

  1. Vygenerujte nový klíč.
  1. Použijte nový klíč pro agenta.

  Postup použití nového klíče pro agenta:

  1. V Průzkumníku souborů přejdete do instalačního adresáře agenta. Výchozí instalační adresář je C:\\Program Files (x86)\\Synchronizace dat SQL Microsoftu.
  1. Dvakrát klikněte na podadresář bin.
  1. Otevřete aplikaci SqlAzureDataSyncAgent.
  1. Vyberte **Odeslat klíč agenta**.
  1. V zadaném prostoru vložte klíč ze schránky.
  1. Vyberte **OK**.
  1. Ukončete program.

### <a name="agent-delete"></a>Agenta klienta nelze odstranit z portálu, pokud je přidružená místní databáze nedosažitelná.

Pokud je místní koncový bod (tj. databáze), který je zaregistrován u agenta Synchronizace dat SQL klienta, nedosažitelný, nelze klientský Agent odstranit.

- **Příčina**: Místního agenta nelze odstranit, protože nedosažitelná databáze je stále registrována v agentovi. Při pokusu o odstranění agenta se proces odstranění pokusí připojit k databázi, která se nezdařila.

- **Řešení**. K odstranění nedostupné databáze použijte možnost vynutit odstranění.

> [!NOTE]
> Pokud jsou tabulky metadat synchronizace po "vynutit odstranění", použijte `deprovisioningutil.exe` k jejich vyčištění.

### <a name="agent-connect"></a>Místní aplikace agenta synchronizace se nemůže připojit k místní službě synchronizace.

- **Řešení**. Vyzkoušejte následující kroky:

  1. Ukončete aplikaci.  
  1. Otevřete panel služby komponent.  
    a. Do vyhledávacího pole na hlavním panelu zadejte **Services. msc**.  
    b. Ve výsledcích hledání poklikejte na **služby**.  
  1. Zastavte službu **synchronizace dat SQL** .
  1. Restartujte službu **synchronizace dat SQL** .  
  1. Znovu tuto aplikaci otevřít.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Spuštění agenta synchronizace dat z příkazového řádku

Z příkazového řádku můžete spustit následující příkazy agenta synchronizace dat:

### <a name="ping-the-service"></a>Odeslání služby testu na službu

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

Když použijete tento příkaz k zrušení registrace databáze, odklade databázi zcela. Pokud se databáze účastní jiných skupin synchronizace, tato operace zruší ostatní skupiny synchronizace.

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

Další informace o Synchronizace dat SQL najdete v následujících článcích:

-   Přehled – [synchronizace dat napříč několika cloudy a místními databázemi pomocí Azure synchronizace dat SQL](sql-database-sync-data.md)
-   Nastavení synchronizace dat
    - Na portálu – [kurz: nastavení synchronizace dat SQL pro synchronizaci dat mezi Azure SQL Database a SQL Server místním](sql-database-get-started-sql-data-sync.md) prostředím
    - S využitím PowerShellu
        -  [Synchronizace mezi několika databázemi Azure SQL pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Osvědčené postupy – [osvědčené postupy pro Azure synchronizace dat SQL](sql-database-best-practices-data-sync.md)
-   Monitorování – [monitorování synchronizace dat SQL pomocí protokolů Azure monitor](sql-database-sync-monitor-oms.md)
-   Řešení potíží – [řešení potíží s Azure synchronizace dat SQL](sql-database-troubleshoot-data-sync.md)
-   Aktualizace schématu synchronizace
    -   Pomocí jazyka Transact-SQL – [Automatizace replikace změn schématu v Azure synchronizace dat SQL](sql-database-update-sync-schema.md)
    -   Prostředí PowerShell – [použití PowerShellu k aktualizaci schématu synchronizace v existující skupině synchronizace](scripts/sql-database-sync-update-schema.md)
