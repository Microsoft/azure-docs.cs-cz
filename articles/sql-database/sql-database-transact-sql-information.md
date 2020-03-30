---
title: Řešení rozdílů T-SQL-migrace
description: Příkazy jazyka Transact-SQL, které služba Azure SQL Database plně nepodporuje
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: e0870ac9dc818ca07e149421b486136c76dd61a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208818"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Řešení rozdílů transakt-SQL během migrace do databáze SQL

Při [migraci databáze](sql-database-single-database-migrate.md) z SQL Server na Azure SQL Server, můžete zjistit, že vaše databáze vyžaduje některé re-engineering před SQL Server lze migrovat. Tento článek obsahuje pokyny, které vám pomohou při provádění této re-engineering a pochopení základní důvody, proč je nutné přepracovat. Chcete-li zjistit nekompatibility, použijte [Pomocníkpro migraci dat (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Přehled

Většina funkcí Transact-SQL, které aplikace používají, je plně podporována v Microsoft SQL Serveru i Azure SQL Database. Například základní součásti SQL, jako jsou datové typy, operátory, řetězec, aritmetické, logické a kurzorové funkce, fungují stejně v SQL Serveru a databázi SQL. Existuje však několik T-SQL rozdíly v DDL (jazyk definice dat) a DML (jazyk pro manipulaci s daty) prvky, které mají za následek T-SQL příkazy a dotazy, které jsou podporovány pouze částečně (které budeme diskutovat dále v tomto článku).

Kromě toho existují některé funkce a syntaxe, která není podporována vůbec, protože Azure SQL Database je navržen tak, aby izolovat funkce od závislostí na hlavní databázi a operační systém. Jako takové většina aktivit na úrovni serveru jsou nevhodné pro sql database. Příkazy a možnosti T-SQL nejsou k dispozici, pokud konfigurují možnosti na úrovni serveru, součásti operačního systému nebo určují konfiguraci systému souborů. Pokud jsou takové funkce požadovány, je vhodná alternativa často dostupná jiným způsobem z databáze SQL nebo z jiné funkce nebo služby Azure.

Vysoká dostupnost je například integrovaná do databáze Azure SQL pomocí technologie podobné [skupinám dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Příkazy T-SQL související se skupinami dostupnosti nejsou podporovány databází SQL a zobrazení dynamické správy související se skupinami dostupnosti always on také nejsou podporována.

Seznam funkcí, které jsou podporovány a nepodporované databází SQL, naleznete v tématu [porovnání funkcí databáze Azure SQL](sql-database-features.md)Database . Seznam na této stránce doplňuje, že pokyny a funkce článku a zaměřuje se na příkazy Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Příkazy syntaxe Transact-SQL s částečnými rozdíly

Základní Příkazy DDL (data definition language) jsou k dispozici, ale některé příkazy DDL mají rozšíření týkající se umístění disku a nepodporovaných funkcí.

- PŘÍKAZY CREATE a ALTER DATABASE mají více než tři desítky možností. Příkazy zahrnují možnosti umístění souboru, FILESTREAM a zprostředkovatele služeb, které se vztahují pouze na SQL Server. To nemusí záležet, pokud vytvoříte databáze před migrací, ale pokud migrujete t-SQL kód, který vytváří databáze, měli byste porovnat [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) se syntaxí SERVERU SQL server na [CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) a ujistěte se, že jsou podporovány všechny možnosti, které používáte. CREATE DATABASE pro Azure SQL Database má také možnosti cíle služby a elastického škálování, které se vztahují jenom pro databázi SQL.
- Příkazy CREATE a ALTER TABLE mají možnosti FileTable, které nelze použít v databázi SQL, protože FILESTREAM není podporován.
- PŘÍKAZY přihlášení CREATE a ALTER jsou podporovány, ale databáze SQL nenabízí všechny možnosti. Chcete-li, aby databáze byla přenosnější, sql database doporučuje používat obsažené uživatele databáze namísto přihlášení, kdykoli je to možné. Další informace naleznete v tématu [CREATE/ALTER LOGIN](https://docs.microsoft.com/sql/t-sql/statements/alter-login-transact-sql) and [Manage logins and users](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Syntaxe Transact-SQL není v Azure SQL Database podporovaná

Kromě příkazů Transact-SQL souvisejících s nepodporovanými funkcemi popsanými v [porovnání funkcí Azure SQL Database](sql-database-features.md)nejsou podporovány následující příkazy a skupiny příkazů. V důsledku toho, pokud databáze, která má být migrována používá některou z následujících funkcí, re-inženýr T-SQL k odstranění těchto funkcí T-SQL a příkazy.

- Kolace systémových objektů
- Související s připojením: Příkazy koncového bodu. Služba SQL Database nepodporuje ověřování Windows, ale podporuje podobné ověřování Azure Active Directory. Některé typy ověřování vyžadují nejnovější verzi SQL Server Management Studia (SSMS). Další informace najdete v tématu [Připojení k SQL Database nebo SQL Data Warehouse pomocí ověřování služby Azure Active Directory](sql-database-aad-authentication.md).
- Mezidatabázové dotazy, které používají tři nebo čtyři názvy částí (mezidatabázové dotazy jen pro čtení jsou podporované prostřednictvím [dotazů do Elastic Database](sql-database-elastic-query-overview.md)).
- Mezidatabázové řetězení vlastnictví, nastavení `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Místo toho použijte EXECUTE AS USER.
- Šifrování je podporované s výjimkou služby EKM (extensible key management).
- Události: Události, oznámení událostí, oznámení o dotazu
- Umístění souborů: Syntaxe související s umístěním, velikostí a soubory databáze, které jsou automaticky spravovány Microsoft Azure.
- Vysoká dostupnost: Syntaxe související s vysokou dostupností, která se spravuje prostřednictvím vašeho účtu Microsoft Azure. Patří sem syntaxe zálohování, obnovení, Always On, zrcadlení databáze, přesouvání protokolu a režimů obnovení.
- Čtečka protokolů: Syntaxe, která závisí na čtečce protokolů, která není k dispozici v databázi SQL: Nabízená replikace, Změna sběru dat. SQL Database může být odběratelem článku nabízené replikace.
- Funkce: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: Syntaxe související s nastavením serveru souvisejícís hardwarem: například paměť, pracovní vlákna, spřažení procesoru, příznaky trasování. Místo toho použijte úrovně služeb a výpočetní velikosti.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET` `OPENDATASOURCE`, a čtyřdílné názvy
- Rozhraní .NET Framework: Integrace CLR se serverem SQL Server
- Sémantické vyhledávání
- Pověření serveru: Místo toho použijte [pověření s vymezenou oborem databáze.](https://msdn.microsoft.com/library/mt270260.aspx)
- Položky na úrovni serveru: Role serveru, `sys.login_token`. K dispozici nejsou příkazy `GRANT`, `REVOKE` a `DENY` serverových oprávnění, i když některé z nich jsou nahrazené oprávněními na úrovni databáze. Některá praktická serverová zobrazení dynamických zpráv (DMV) mají odpovídající databázová zobrazení dynamických zpráv.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Možnosti `sp_configure` a `RECONFIGURE`. Některé možnosti jsou dostupné prostřednictvím příkazu [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Agent serveru SQL Server: Syntaxe, která závisí na agentovi serveru SQL Server nebo databázi MSDB: výstrahy, operátoři, servery centrální správy. Použijte raději skriptování, například v Azure PowerShellu.
- Sql Server audit: Místo toho použijte auditování databáze SQL.
- Trasování SQL Serveru
- Trasování příznaky: Některé položky příznak trasování byly přesunuty do režimů kompatibility.
- Ladění Transact-SQL
- Aktivační události: serverové nebo přihlašovací aktivační události
- Příkaz `USE`: Pokud chcete změnit kontext databáze na jinou databázi, musíte k nové databázi vytvořit nové připojení.

## <a name="full-transact-sql-reference"></a>Kompletní reference k jazyku Transact-SQL

Další informace o gramatice Transact-SQL, použití a příklady naleznete v [tématu Transact-SQL Reference (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx) v SQL Server Books Online.

### <a name="about-the-applies-to-tags"></a>Informace o značkách „Platí pro“

Odkaz Transact-SQL obsahuje články související s verzemi serveru SQL Server 2008 do současnosti. Pod názvem článku je panel ikon, seznam čtyř platforem SQL Server a označující použitelnost. Například skupiny dostupnosti byly zavedeny v SQL Serveru 2012. Vytvoření [skupiny](https://msdn.microsoft.com/library/ff878399.aspx) dostupnosti článek označuje, že příkaz se vztahuje na **SQL Server (počínaje 2012).** Příkaz neplatí pro SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure SQL Data Warehouse ani pro Parallel Data Warehouse.

V některých případech může být obecný předmět výrobku použit v produktu, ale mezi produkty existují drobné rozdíly. Rozdíly jsou uvedeny v polovině bodu v článku podle potřeby. V některých případech může být obecný předmět výrobku použit v produktu, ale mezi produkty existují drobné rozdíly. Rozdíly jsou uvedeny v polovině bodu v článku podle potřeby. Například vytvořit aktivační událost článek je k dispozici v databázi SQL. Ale **možnost ALL SERVER** pro aktivační události na úrovni serveru označuje, že aktivační události na úrovni serveru nelze použít v databázi SQL. Místo toho použijte aktivační události na úrovni databáze.

## <a name="next-steps"></a>Další kroky

Seznam funkcí, které jsou podporovány a nepodporované databází SQL, naleznete v tématu [porovnání funkcí databáze Azure SQL](sql-database-features.md)Database . Seznam na této stránce doplňuje, že pokyny a funkce článku a zaměřuje se na příkazy Transact-SQL.
