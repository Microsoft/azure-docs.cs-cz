---
title: Synchronizace dat agenta pro synchronizaci dat Azure SQL | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat a spustit agenta synchronizace dat pro synchronizaci dat SQL Azure k synchronizaci dat s místní databází SQL serveru
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 11/08/2018
ms.openlocfilehash: 9e873de5899f0cf84fe76b70ffb70b38638055ef
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51299890"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Agent synchronizace dat pro synchronizaci dat Azure SQL

Synchronizace dat s místní databází SQL serveru z hlediska instalace a konfigurace agenta synchronizace dat pro synchronizaci dat SQL Azure. Další informace o synchronizaci dat SQL najdete v tématu [synchronizaci dat napříč několika cloudu a místními databázemi pomocí synchronizace dat SQL](sql-database-sync-data.md).

## <a name="download-and-install"></a>Stažení a instalace

Chcete-li stáhnout agenta synchronizace dat, přejděte na [agenta synchronizace dat SQL Azure](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Bezobslužná instalace

Pro tichou instalaci agenta synchronizace dat z příkazového řádku, zadejte příkaz podobně jako v následujícím příkladu. Zkontrolujte název souboru .msi staženého souboru a zadejte vlastní hodnoty **TARGETDIR** a **SERVICEACCOUNT** argumenty.

```cmd
msiexec /i SQLDataSyncAgent-2.0--ENU.msi TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn 
```

## <a name="sync-data-with-sql-server-on-premises"></a>Synchronizace dat s místním SQL serverem

Konfiguraci agenta synchronizace dat tak můžete synchronizovat data pomocí jednoho nebo více místních databází SQL serveru najdete v tématu [přidat databázi systému SQL Server v místním](sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="agent-faq"></a> Nejčastější dotazy k agentovi synchronizace dat

### <a name="why-do-i-need-a-client-agent"></a>Proč potřebuji agenta klienta

Služba synchronizace dat SQL komunikuje s databází systému SQL Server prostřednictvím agenta klienta. Tuto funkci zabezpečení znemožňuje přímé komunikaci s databází za bránou firewall. Při synchronizaci dat SQL service komunikuje s agentem, pomocí provádí šifrované připojení a token jedinečný nebo *klíč agenta*. Databáze systému SQL Server ověření agenta pomocí připojovací řetězec a agent klíč. Tento návrh poskytuje vysokou úroveň zabezpečení pro vaše data.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Kolik instancí místní agent uživatelského rozhraní můžete spustit.

Lze spustit pouze jednu instanci uživatelského rozhraní.

### <a name="how-can-i-change-my-service-account"></a>Jak mohu změnit Můj účet

Po instalaci agenta klienta je jediný způsob, jak změnit účet služby odinstalujte ho a nainstalujte nového agenta klienta pomocí nového účtu služby.

### <a name="how-do-i-change-my-agent-key"></a>Jak mohu změnit můj klíč agenta

Klíč agenta lze použít pouze jednou agentem. Se nesmí znovu použít při odebrání pak ji znovu nainstalujte nového agenta ani mohou využívat více agentů. Pokud je potřeba vytvořit nový klíč pro existujícího agenta, musí být jisti, že stejný klíč je zaznamenán s agenta klienta a synchronizaci dat SQL service.

### <a name="how-do-i-retire-a-client-agent"></a>Jak se vyřadit Klientský agent

Okamžitě zrušení platnosti nebo vyřazení agenta, znovu vygenerovat své klíče na portálu, ale neodešlou v Uživatelském rozhraní služby agentů. Obnovuje se klíč zruší platnost předchozí klíč bez ohledu, pokud je odpovídající agent online nebo offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Jak můžu přesunout klientského agenta do jiného počítače

Pokud chcete spustit místní agent z jiného počítače, než je aktuálně v, proveďte následující akce:

1. Nainstalujte agenta na požadovaný počítač.
2. Připojte se k portálu pro synchronizaci dat SQL a znovu vygenerovat klíč agenta pro nového agenta.
3. Použijte uživatelské rozhraní nového agenta odeslat nový klíč agenta.
4. Počkejte, dokud agent klienta stáhne seznam místních databází, které jste zaregistrovali dříve.
5. Zadejte přihlašovací údaje databáze pro všechny databáze, které zobrazují jako nedostupné. Tyto databáze musí být dosažitelná z nového počítače, na kterém je nainstalovaný agent.

## <a name="agent-tshoot"></a> Řešení potíží s agenta synchronizace dat

- [Agent klienta nainstalovat, odinstalujete nebo opravíte selže](#agent-install)

- [Klientský agent nebude fungovat po můžu zrušit odinstalaci](#agent-uninstall)

- [Moje databáze není uvedená v seznamu agenta](#agent-list)

- [Klientský agent aktualizace softwaru nelze spustit (Chyba 1069)](#agent-start)

- [Nemůžu Odeslat klíč agenta](#agent-key)

- [Klientský agent nelze odstranit z portálu, pokud jeho přidružené místní databáze nedostupný](#agent-delete)

- [Místní Agent synchronizace aplikace se nemůže připojit k místní synchronizační služby](#agent-connect)

### <a name="agent-install"></a> Agent klienta nainstalovat, odinstalujete nebo opravíte selže

- **Příčina**. Mnoho scénářů může vést k této chybě. Chcete-li zjistit, konkrétní příčinu této chyby, podívejte se na protokoly.

- **Rozlišení**. Pokud chcete najít konkrétní příčinu selhání, generovat a podívejte se na protokoly Instalační služby systému Windows. Můžete zapnout protokolování na příkazovém řádku. Například pokud stažený soubor AgentServiceSetup.msi LocalAgentHost.msi, generovat a prozkoumejte soubory protokolu s použitím následujících příkazových řádků:

    -   Pro instalace: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
    -   Pro odinstaluje: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

    Můžete také zapnout protokolování pro všechna zařízení, které se provádí pomocí Instalační služby systému Windows. Článek znalostní báze Microsoft [jak povolit protokolování Instalační služby systému Windows](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) poskytuje řešení jedním kliknutím k zapnutí protokolování pro Instalační služby systému Windows. Poskytuje také umístění protokolů.

### <a name="agent-uninstall"></a> Klientský agent nebude fungovat po můžu zrušit odinstalaci

Klientský agent nebude fungovat, i když můžete zrušit jeho odinstalaci.

- **Příčina**. K tomu dochází, protože klientský agent synchronizace dat SQL nejsou uloženy přihlašovací údaje.

- **Rozlišení**. Můžete vyzkoušet tyto dvě řešení:

    -   Zadejte znovu přihlašovací údaje pro agenta klienta pomocí modulu services.msc.
    -   Odinstalujte tohoto agenta klienta a pak nainstalovat novou. Stáhněte a nainstalujte nejnovějšího agenta klienta [Download Center](https://go.microsoft.com/fwlink/?linkid=221479).

### <a name="agent-list"></a> Moje databáze není uvedená v seznamu agenta

Při pokusu o přidání existující databáze SQL serveru do skupiny synchronizace databáze nezobrazí v seznamu agentů.

Tyto scénáře mohou způsobit potíže:

- **Příčina**. Skupina synchronizace a agent klienta jsou v různých datových centrech.

- **Rozlišení**. Klientský agent a skupinu synchronizace musí být ve stejném datacentru. Chcete-li toto nastavení, máte dvě možnosti:

    -   Vytvořte nového agenta v datovém centru, kde se nachází skupina synchronizace. Databáze potom zaregistrujte tohoto agenta.
    -   Odstraňte aktuální skupinu synchronizace. Potom znovu vytvořte skupinu synchronizace v datacentru, kde se nachází agent.

- **Příčina**. Klientský agent seznamu databází, které není aktuální.

- **Rozlišení**. Zastavte a restartujte službu agenta klienta.

    Místní agent přitom stáhne seznam přidružených databázích pouze při prvním odeslání klíč agenta. To se nestahuje seznam přidružených databázích v odesílání následných agenta klíče. Databáze, které jsou registrovány během jako přesunutí na agenta nezobrazují v původní instance agenta.

### <a name="agent-start"></a> Klientský agent aktualizace softwaru nelze spustit (Chyba 1069)

Zjistíte, zda není spuštěn agent na počítači, který je hostitelem systému SQL Server. Při pokusu o ruční spuštění agenta, se zobrazí dialogové okno, které se zobrazí zpráva "chyby 1069: Služba se nespustila kvůli selhání přihlášení."

![Dialogové okno chyby 1069 synchronizace dat](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Příčina**. Pravděpodobnou příčinou této chyby je, že heslo na místním serveru změnila od jeho vytvoření agenta a agent heslo.

- **Rozlišení**. Aktualizace agenta hesla pro vaše aktuální heslo serveru:

  1. Vyhledejte službu agenta klienta synchronizace dat SQL.  
    a. Vyberte **Start**.  
    b. Do vyhledávacího pole zadejte **services.msc**.  
    c. Ve výsledcích hledání vyberte **služby**.  
    d. V **služby** okno, přejděte na položku pro **agenta synchronizace dat SQL**.  
  1. Klikněte pravým tlačítkem na **agenta synchronizace dat SQL**a pak vyberte **Zastavit**.
  1. Klikněte pravým tlačítkem na **agenta synchronizace dat SQL**a pak vyberte **vlastnosti**.
  1. Na **vlastnosti agenta synchronizace dat SQL**, vyberte **přihlášení** kartu.
  1. V **heslo** pole, zadejte své heslo.
  1. V **Potvrdit heslo** pole, zadejte znovu své heslo.
  1. Vyberte **Apply** (Použít) a pak vyberte **OK**.
  1. V **služby** okna, klikněte pravým tlačítkem na **agenta synchronizace dat SQL** služby a potom klikněte na tlačítko **Start**.
  1. Zavřít **služby** okna.

### <a name="agent-key"></a> Nemůžu Odeslat klíč agenta

Po vytvoření nebo znovu vytvořit klíč pro agenta, pokusu o odeslání klíč prostřednictvím SqlAzureDataSyncAgent aplikace. Odeslání nepodaří dokončit.

![Dialogové okno chyby synchronizace – nejde odeslat klíč agenta](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Požadavky**. Než budete pokračovat, zkontrolujte následující požadavky:

  - Se službou Windows synchronizace dat SQL.

  - Účet služby pro službu Windows synchronizace dat SQL má přístup k síti.

  - Odchozí port 1433 je otevřen v místní bráně firewall pravidla.

  - Místní IP adresa se přidá na server nebo pravidlo brány firewall databáze pro databázi metadat synchronizace.

- **Příčina**. Klíč agenta jednoznačně identifikuje každého místního agenta. Klíč musí splňovat dvě podmínky:

  -   Klíč agenta klienta v synchronizaci dat SQL serveru a místní počítač musí být identické.
  -   Klíč agenta klienta lze použít pouze jednou.

- **Rozlišení**. Pokud agenta není funkční, bude to, že jedna nebo obě tyto podmínky nejsou splněné. Pokud chcete získat agenta znovu pracovat:

  1. Generovat nový klíč.
  1. Nový klíč platí pro agenta.

  Chcete-li použít nový klíč k agentovi:

  1. V Průzkumníku souborů přejděte do instalační adresář agenta. Výchozí adresář instalace je C:\\Program Files (x86)\\synchronizace dat Microsoft SQL.
  1. Dvakrát klikněte na panel podadresáře bin.
  1. Otevřete aplikaci SqlAzureDataSyncAgent.
  1. Vyberte **odešlete klíč agenta**.
  1. V poskytnutém prostoru vložte klíč ze schránky.
  1. Vyberte **OK**.
  1. Program ukončíte.

### <a name="agent-delete"></a> Klientský agent nelze odstranit z portálu, pokud jeho přidružené místní databáze nedostupný

Pokud místní koncový bod (to znamená, že databáze), který je registrovaný pomocí synchronizace dat SQL klientského agenta se nestane nedostupnou, nelze odstranit agenta klienta.

- **Příčina**. Místní agent nejde odstranit, protože do nedostupný databáze pořád bude registrovaný s agentem. Při pokusu o odstranění agenta proces odstranění se pokusí kontaktovat databázi, která se nezdaří.

- **Rozlišení**. Použití "Vynutit odstranění" do nedostupný databázi odstranit.

> [!NOTE]
> Pokud tabulky metadat synchronizace zůstanou po "Vynutit odstranění", použijte `deprovisioningutil.exe` ho PROČISTIT.

### <a name="agent-connect"></a> Místní Agent synchronizace aplikace se nemůže připojit k místní synchronizační služby

- **Rozlišení**. Vyzkoušejte následující kroky:

  1. Ukončete aplikaci.  
  1. Otevřete Panel služeb součástí.  
    a. Do vyhledávacího pole na hlavním panelu, zadejte **services.msc**.  
    b. Ve výsledcích hledání poklikejte na **služby**.  
  1. Zastavit **synchronizace dat SQL** služby.
  1. Restartujte **synchronizace dat SQL** služby.  
  1. Znovu tuto aplikaci otevřít.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Spusťte agenta synchronizace dat z příkazového řádku

Spuštěním následujících příkazů agenta synchronizace dat z příkazového řádku:

### <a name="ping-the-service"></a>Odešlete zprávu ping služby

#### <a name="usage"></a>Využití

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Příklad:

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Zobrazit registrované databází

#### <a name="usage"></a>Využití

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Příklad:

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>Odešlete klíč agenta

#### <a name="usage"></a>Využití

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Příklad:

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Registrace do databáze

#### <a name="usage"></a>Využití

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Příklady

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Zrušit registraci databáze

#### <a name="usage"></a>Využití

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>Příklad:

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

## <a name="next-steps"></a>Další postup

Další informace o synchronizaci dat SQL najdete v následujících článcích:

- [Kurz: Nastavení synchronizace dat SQL, synchronizaci dat mezi Azure SQL Database a místním SQL serverem](sql-database-get-started-sql-data-sync.md)

- [Synchronizace dat napříč několika cloudu a místními databázemi pomocí synchronizace dat SQL](sql-database-sync-data.md)