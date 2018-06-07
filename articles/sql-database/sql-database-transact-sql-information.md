---
title: Řešení T-SQL rozdíly. migrace Azure SQL Database | Microsoft Docs
description: Příkazy jazyka Transact-SQL, které služba Azure SQL Database plně nepodporuje
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: e89c863ac50a8b906b388c505f444cd60fdbaad3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649070"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Řešení rozdílů Transact-SQL během migrace do databáze SQL   
Když [migrace vaší databáze](sql-database-cloud-migrate.md) ze serveru SQL Server SQL Azure, může se stát, že vaše databáze vyžaduje některé znovu technici před migrací serveru SQL Server. Tento článek obsahuje pokyny, které vám pomohou při provádění technici znovu i pochopení základní důvody, proč je nutné znovu technici. Chcete-li zjistit problémům s kompatibilitou, použijte [pomocníka pro migraci dat (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Přehled
Většinu funkcí jazyka Transact-SQL, které používají aplikace jsou plně podporovaný v systému Microsoft SQL Server a databáze SQL Azure. Například základní součásti SQL například datové typy, operátory, řetězec, aritmetické logické a funkce kurzoru, fungují stejně jako v systému SQL Server a SQL Database. Existují, ale několik rozdílů T-SQL v DDL (data definition language) a elementy DML (jazyk pro manipulaci dat), které jsou výsledkem příkazů T-SQL a dotazy, které jsou podporovány pouze částečně (který probereme později v tomto článku).

Kromě toho existují některé funkce a syntaxi, která není podporována na všech, protože databáze SQL Azure je slouží k oddělení funkcí závislostí na hlavní databázi a operační systém. Jako takový většinu aktivit na úrovni serveru nejsou vhodná pro databáze SQL. Příkazů T-SQL a možnosti nejsou k dispozici, pokud konfigurovat možnosti úrovni serveru, komponent operačního systému, nebo zadejte konfiguraci systému souborů. Pokud například tyto funkce je potřeba, alternativou odpovídající je často k dispozici jiným způsobem z databáze SQL nebo z jiného Azure funkce nebo služba. 

Například vysoké dostupnosti je integrovaná do Azure, takže konfigurace Always On není nutné (i když můžete chtít nakonfigurovat aktivní geografickou replikací pro rychlejší obnovení v případě havárie). Ano související se skupinami dostupnosti příkazů T-SQL nejsou podporovány SQL Database a zobrazení dynamické správy související s Always On také nejsou podporovány.

Seznam funkcí, které jsou podporované a nepodporované SQL Database, najdete v části [porovnání funkcí Azure SQL Database](sql-database-features.md). V seznamu na této stránce doplňují tohoto článku pokyny a funkce a se zaměřuje na příkazy jazyka Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Příkazy Syntaxe Transact-SQL s částečné rozdíly
Základní příkazy DDL (data definition language) jsou k dispozici, ale některé příkazy DDL mít rozšíření související s umístění disku a nepodporované funkce. 

- Příkazy CREATE a ALTER DATABASE mít více než tucet tři možnosti. Příkazy zahrnují umístění souboru FILESTREAM a možnosti zprostředkovatele služby, které se vztahují pouze na serveru SQL Server. To není důležité, zda vytváření databází před migrací, ale pokud provádíte migraci kód T-SQL, který vytvoří databáze byste měli porovnat [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) se syntaxí SQL Server v [vytvořit DATABÁZE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) a ujistěte se, jsou podporovány všechny možnosti, které používáte. CREATE DATABASE pro databázi SQL Azure má také cíl služby a elastické škálování možnosti, které se vztahují pouze k databázi SQL.
- Příkazy CREATE a ALTER TABLE mají FileTable možností, které nelze použít v databázi SQL, protože FILESTREAM není podporována.
- Příkazy CREATE a ALTER login jsou podporována, ale databáze SQL nenabízí všechny možnosti. Chcete-li víc přenosného vaší databáze, databáze SQL podporuje použití uživatele databáze s omezením místo přihlášení. kdykoli je to možné. Další informace najdete v tématu [CREATE nebo ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) a [řízení a udělení přístupu k databázi](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Syntaxe Transact-SQL není podporována ve službě Azure SQL Database   
Kromě příkazy jazyka Transact-SQL související se Nepodporovaná funkce popsané v [porovnání funkcí Azure SQL Database](sql-database-features.md), následující příkazy a skupiny příkazy, nejsou podporované. Jako takový Pokud vaše databáze má být provedena migrace používá některý z následujících funkcí, znovu analyzovat vaše T-SQL omezit tyto funkce T-SQL a příkazy.

- Kolace systémových objektů
- Připojení, související: příkazy koncový bod. Služba SQL Database nepodporuje ověřování Windows, ale podporuje podobné ověřování Azure Active Directory. Některé typy ověřování vyžadují nejnovější verzi SQL Server Management Studia (SSMS). Další informace najdete v tématu [Připojení k SQL Database nebo SQL Data Warehouse pomocí ověřování služby Azure Active Directory](sql-database-aad-authentication.md).
- Mezidatabázové dotazy, které používají tři nebo čtyři názvy částí (mezidatabázové dotazy jen pro čtení jsou podporované prostřednictvím [dotazů do Elastic Database](sql-database-elastic-query-overview.md)).
- Mezidatabázové řetězení vlastnictví, nastavení `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Místo toho použijte EXECUTE AS USER.
- Šifrování je podporované s výjimkou služby EKM (extensible key management).
- Zpracování událostí: Události, události oznámení, oznámení dotazů
- Umístění souboru: syntaxe týkající se umístění souboru databáze, velikost a databázové soubory, které jsou automaticky spravovány Microsoft Azure.
- Vysoká dostupnost: syntaxe související s vysokou dostupností, který je spravován pomocí účtu Microsoft Azure. Patří sem syntaxe zálohování, obnovení, Always On, zrcadlení databáze, přesouvání protokolu a režimů obnovení.
- Čtečky protokolu: syntaxi, která závisí na protokolu čtečka, která není k dispozici pro službu SQL Database: Push replikace, Change Data Capture. SQL Database může být odběratelem článku nabízené replikace.
- Funkce: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: Syntaxe související s nastavení související s hardwarem serveru: například paměť, pracovních vláken, spřažení procesoru, označí příznakem trasování. Místo ní použijte úrovně služby.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`a sestávající ze čtyř částí názvy
- Rozhraní .NET framework: Integrace modulu CLR s SQL serverem
- Sémantické vyhledávání
- Přihlašovací údaje serveru: použití [přihlašovací údaje na obor definovaný databází](https://msdn.microsoft.com/library/mt270260.aspx) místo.
- Položky na úrovni serveru: role serveru, `sys.login_token`. K dispozici nejsou příkazy `GRANT`, `REVOKE` a `DENY` serverových oprávnění, i když některé z nich jsou nahrazené oprávněními na úrovni databáze. Některá praktická serverová zobrazení dynamických zpráv (DMV) mají odpovídající databázová zobrazení dynamických zpráv.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Možnosti `sp_configure` a `RECONFIGURE`. Některé možnosti jsou dostupné prostřednictvím příkazu [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Agent systému SQL Server: Syntaxi, která závisí na Agent serveru SQL Server nebo databázi MSDB: výstrahy, operátory, servery pro centrální správu. Použijte raději skriptování, například v Azure PowerShellu.
- SQL Server audit: auditování databáze SQL použijte místo toho.
- Trasování SQL Serveru
- Příznaky trasování: některé položky příznak trasování, byly přesunuty do režimy kompatibility.
- Ladění Transact-SQL
- Aktivační události: serverové nebo přihlašovací aktivační události
- Příkaz `USE`: Pokud chcete změnit kontext databáze na jinou databázi, musíte k nové databázi vytvořit nové připojení.

## <a name="full-transact-sql-reference"></a>Kompletní reference k jazyku Transact-SQL
Další informace o syntaxi a používání jazyka Transact-SQL, včetně příkladů, najdete v tématu [Reference k jazyku Transact-SQL (databázový stroj)](https://msdn.microsoft.com/library/bb510741.aspx) v dokumentaci SQL Server Books Online. 

### <a name="about-the-applies-to-tags"></a>Informace o značkách „Platí pro“
Referenční dokumentace jazyka Transact-SQL obsahuje články související s verzí systému SQL Server 2008 tohoto. Pod nadpisem článku článek existuje je ikonu panelu výpis čtyři platformy systému SQL Server a označující použitelnosti. Například skupiny dostupnosti byly zavedeny v SQL Serveru 2012. [Vytvořit SKUPINU AVAILABILTY](https://msdn.microsoft.com/library/ff878399.aspx) článku naznačuje, že příkaz používá k **systému SQL Server (počínaje 2012)**. Příkaz neplatí pro SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure SQL Data Warehouse ani pro Parallel Data Warehouse.

V některých případech obecné téma článku na lze použít v produktu, ale existují malé rozdíly mezi produkty. Rozdíly jsou vypsány v středních bodů v článku podle potřeby. V některých případech obecné téma článku na lze použít v produktu, ale existují malé rozdíly mezi produkty. Rozdíly jsou vypsány v středních bodů v článku podle potřeby. Například článek vytvořit aktivační událost je k dispozici v databázi SQL. Ale **všechny SERVER** možnost pro aktivační procedury úrovni serveru, znamená, že úrovni serveru aktivační události nelze použít v databázi SQL. Místo toho použijte úroveň databáze aktivační události.

## <a name="next-steps"></a>Další postup

Seznam funkcí, které jsou podporované a nepodporované SQL Database, najdete v části [porovnání funkcí Azure SQL Database](sql-database-features.md). V seznamu na této stránce doplňují tohoto článku pokyny a funkce a se zaměřuje na příkazy jazyka Transact-SQL.

