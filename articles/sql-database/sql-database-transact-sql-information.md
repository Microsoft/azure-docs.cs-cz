---
title: Řešení T-SQL rozdíly. migrace Azure SQL Database | Dokumentace Microsoftu
description: Příkazy jazyka Transact-SQL, které služba Azure SQL Database plně nepodporuje
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: 90c8af9b09a5050cc090725665501d60091c9ae5
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509163"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Řešení rozdílů jazyka Transact-SQL během migrace do služby SQL Database

Když [migrace vaší databáze](sql-database-cloud-migrate.md) z SQL serveru do Azure SQL serveru, pravděpodobně zjistíte, že vaše databáze vyžaduje úpravy kódu před migrací systému SQL Server. Tento článek obsahuje pokyny, které vám pomohou při provádění této předělávání i pochopení základní důvody, proč je nutné znovu inženýrství. K zjišťování nekompatibilit služby, použijte [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Přehled

Většina funkcí jazyka Transact-SQL, které používají aplikace jsou plně podporovány v systému Microsoft SQL Server a Azure SQL Database. Například hlavní součásti SQL, jako je například datové typy, operátory, řetězce, aritmetické, logické a ukazatel funkce, fungují stejně jako v systému SQL Server a SQL Database. Existují, ale několik rozdílů jazyka T-SQL v DDL (data definition language) a prvky DML (jazyk pro manipulaci dat), výsledkem je příkazy jazyka T-SQL a dotazy, které jsou podporovány jen částečně (která probereme později v tomto článku).

Kromě toho existují některé funkce a syntaxi, která není podporována na všech, protože Azure SQL Database je určena izolovala funkce od závislostí na hlavní databázi a operačním systému. V důsledku toho se většina aktivit na úrovni serveru pro SQL Database nevhodná. Příkazy jazyka T-SQL a možnosti nejsou k dispozici, je-li nakonfigurovat možnosti na úrovni serveru, součásti operačního systému nebo určují konfiguraci systému souborů. Když tyto možnosti jsou povinné, je často odpovídající alternativu k dispozici jiným způsobem ze služby SQL Database nebo z jiného Azure funkci nebo službu.

Například vysoká dostupnost je integrovaná do Azure SQL Database pomocí technologie podobný [skupin dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Služba SQL Database nepodporuje související se skupinami dostupnosti příkazy jazyka T-SQL a také zobrazení dynamické správy související se skupinami dostupnosti Always On nepodporují.

Seznam funkcí, které jsou podporované a nepodporované službou SQL Database najdete v tématu [porovnání funkcí Azure SQL Database](sql-database-features.md). V seznamu na této stránce doplňuje článku pokyny a funkce a se zaměřuje na příkazů jazyka Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Syntaxe příkazů jazyka Transact-SQL s částečnou rozdíly

Základní příkazy DDL (data definition language) jsou k dispozici, ale některé příkazy jazyka DDL mít rozšíření související s umístění disku a nepodporované funkce.

- Příkazy CREATE a ALTER DATABASE k dispozici více než deseti tři možnosti. Příkazy zahrnují umístění souboru FILESTREAM a služby Service broker možnosti, které se vztahují jenom na serveru SQL Server. To nemusí vadit, pokud vytváříte databáze před migrací, ale pokud migrujete kód T-SQL, který vytvoří databáze porovnejte [vytvoření databáze (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) syntaxí SQL serveru na [CREATE DATABÁZE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) k Ujistěte se, že se podporují všechny možnosti, které používáte. VYTVOŘIT databázi Azure SQL Database má také cíle služby a možnosti pružné škálování, které platí jenom pro SQL Database.
- Příkazy CREATE a ALTER TABLE mají objektu FileTable volby, které nelze použít pro službu SQL Database, protože FILESTREAM není podporován.
- Příkazy CREATE a ALTER login nejsou podporovány, ale databáze SQL se nebude poskytovat všechny požadované možnosti. Přenositelnosti databáze, SQL Database podporuje uživatele databáze s omezením místo přihlášení, kdykoli je to možné. Další informace najdete v tématu [CREATE/ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) a [řízení a udělování přístupu k databázi](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Syntaxe jazyka Transact-SQL není podporováno ve službě Azure SQL Database

Kromě příkazů jazyka Transact-SQL příkazy související s nepodporovanými funkcemi popsanými v [porovnání funkcí Azure SQL Database](sql-database-features.md), následující příkazy a skupiny příkazů, nejsou podporované. Proto pokud vaše databáze k migraci používá některou z následujících funkcí, znovu zpětnou analýzu T-SQL k odstranění těchto funkcí T-SQL a příkazy.

- Kolace systémových objektů
- Související s připojením: Koncový bod příkazů. Služba SQL Database nepodporuje ověřování Windows, ale podporuje podobné ověřování Azure Active Directory. Některé typy ověřování vyžadují nejnovější verzi SQL Server Management Studia (SSMS). Další informace najdete v tématu [Připojení k SQL Database nebo SQL Data Warehouse pomocí ověřování služby Azure Active Directory](sql-database-aad-authentication.md).
- Mezidatabázové dotazy, které používají tři nebo čtyři názvy částí (mezidatabázové dotazy jen pro čtení jsou podporované prostřednictvím [dotazů do Elastic Database](sql-database-elastic-query-overview.md)).
- Mezidatabázové řetězení vlastnictví, nastavení `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Místo toho použijte EXECUTE AS USER.
- Šifrování je podporované s výjimkou služby EKM (extensible key management).
- Zpracování událostí: Události, události oznámení, oznámení dotazů
- Umístění souboru: Syntaxe spojená s umístěním databázových souborů, jejich velikostí a databázovými soubory, které automaticky spravuje Microsoft Azure.
- Vysoká dostupnost: Syntaxe týkající se vysoké dostupnosti, která je spravovaná prostřednictvím účtu Microsoft Azure. Patří sem syntaxe zálohování, obnovení, Always On, zrcadlení databáze, přesouvání protokolu a režimů obnovení.
- Čtenář log: Syntaxe, která závisí na protokolů Čtenář, která není k dispozici v SQL Database: Nabízená oznámení replikace, Change Data Capture. SQL Database může být odběratelem článku nabízené replikace.
- Funkce: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: Syntaxe spojená s nastavením serverového hardwaru: například paměť, pracovní vlákna, spřažení procesorů, příznaků trasování. Použijte úrovně služby a místo toho výpočetní velikosti.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`a názvy složené ze čtyř částí
- .NET Framework: Integrace modulu CLR SQL serveru
- Sémantické vyhledávání
- Přihlašovací údaje serveru: Použití [přihlašovací údaje v oboru databáze](https://msdn.microsoft.com/library/mt270260.aspx) místo.
- Serverové položky: Role serveru, `sys.login_token`. K dispozici nejsou příkazy `GRANT`, `REVOKE` a `DENY` serverových oprávnění, i když některé z nich jsou nahrazené oprávněními na úrovni databáze. Některá praktická serverová zobrazení dynamických zpráv (DMV) mají odpovídající databázová zobrazení dynamických zpráv.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Možnosti `sp_configure` a `RECONFIGURE`. Některé možnosti jsou dostupné prostřednictvím příkazu [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Agent systému SQL Server: Syntaxe, která závisí na službě SQL Server Agent nebo na databázi MSDB: výstrahy, operátory a servery centrální správy. Použijte raději skriptování, například v Azure PowerShellu.
- Auditování SQL serveru: Místo něho použijte auditování SQL Database.
- Trasování SQL Serveru
- Příznaky trasování: Některé příznaky trasování byly přesunuty do kompatibilních režimů.
- Ladění Transact-SQL
- Aktivační události: Serverové nebo přihlašovací aktivační události
- `USE` příkaz: Pokud chcete změnit kontext databáze na jinou databázi, je třeba nové připojení k nové databázi.

## <a name="full-transact-sql-reference"></a>Kompletní reference k jazyku Transact-SQL

Další informace o gramatice jazyka Transact-SQL, použití a příkladů, najdete v části [odkaz Transact-SQL (databázový stroj)](https://msdn.microsoft.com/library/bb510741.aspx) v SQL Server Books Online.

### <a name="about-the-applies-to-tags"></a>Informace o značkách „Platí pro“

Referenční dokumentace jazyka Transact-SQL obsahuje články související s verzí SQL serveru 2008 až po současnost. Pod nadpisem článku existuje je ikona řádku, výpis čtyřmi platformami SQL serveru a vyznačenou. Například skupiny dostupnosti byly zavedeny v SQL Serveru 2012.  [CREATE AVAILABILITY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) článku označuje, že příkaz platí pro systému  **SQL Server (počínaje verzí 2012)**. Příkaz neplatí pro SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure SQL Data Warehouse ani pro Parallel Data Warehouse.

V některých případech se v produktu můžete použít obecný předmět článku, ale existují malé rozdíly mezi produkty. Rozdíly jsou uvedené u středových bodů v článku znalostní báze podle potřeby. V některých případech se v produktu můžete použít obecný předmět článku, ale existují malé rozdíly mezi produkty. Rozdíly jsou uvedené u středových bodů v článku znalostní báze podle potřeby. Například je k dispozici ve službě SQL Database článku vytvořit aktivační událost. Ale **všechny SERVER** možnost pro triggery na úrovni serveru, označuje, že úroveň serveru triggery nejde použít ve službě SQL Database. Místo toho použití aktivačních procedur úrovni databáze.

## <a name="next-steps"></a>Další postup

Seznam funkcí, které jsou podporované a nepodporované službou SQL Database najdete v tématu [porovnání funkcí Azure SQL Database](sql-database-features.md). V seznamu na této stránce doplňuje článku pokyny a funkce a se zaměřuje na příkazů jazyka Transact-SQL.
