---
title: Řešení rozdílů v T-SQL – migrace Azure SQL Database | Microsoft Docs
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
ms.openlocfilehash: 0f64642d04504770415c0d2243ec77b44bde05f2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566300"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Řešení rozdílů v jazyce Transact-SQL během migrace na SQL Database

Při [migraci databáze](sql-database-single-database-migrate.md) z SQL Server do Azure SQL Server můžete zjistit, že vaše databáze vyžaduje před migrací SQL Server nějaké nové strojírenství. V tomto článku najdete pokyny, které vám pomůžou při provádění tohoto přetváření a porozumění základním důvodům, proč je nutné je znovu inženýrovat. K detekci nekompatibility použijte [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Přehled

Většina funkcí Transact-SQL, které aplikace používá, je plně podporovaná v Microsoft SQL Server i Azure SQL Database. Například základní komponenty SQL, jako jsou datové typy, operátory, řetězce, aritmetické funkce, logické a kurzorové funkce, fungují stejně v SQL Server a SQL Database. Existuje však několik rozdílů v jazyce T-SQL v prvcích DDL (Data-Definition Language) a DML (data pro manipulaci s daty), které jsou výsledkem příkazů T-SQL a dotazů, které jsou pouze částečně podporovány (což se zabývá dále v tomto článku).

Kromě toho existují některé funkce a syntaxe, které nejsou vůbec podporovány, protože Azure SQL Database slouží k izolaci funkcí ze závislostí v hlavní databázi a v operačním systému. Proto většina aktivit na úrovni serveru není pro SQL Database vhodná. Příkazy T-SQL a možnosti nejsou k dispozici, pokud konfigurují možnosti na úrovni serveru, součásti operačního systému nebo určují konfiguraci systému souborů. Pokud jsou tyto možnosti požadovány, je vhodná alternativa často k dispozici jiným způsobem než SQL Database nebo z jiné funkce nebo služby Azure.

Například vysoká dostupnost je integrována do Azure SQL Database s využitím technologie podobně jako u [skupin dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Příkazy T-SQL související se skupinami dostupnosti nejsou podporovány nástrojem SQL Database a zobrazení dynamické správy související se skupinami dostupnosti Always On nejsou také podporována.

Seznam funkcí, které jsou v SQL Database podporované a nepodporované, najdete v článku [porovnání funkcí Azure SQL Database](sql-database-features.md). Seznam na této stránce doplňuje pokyny a funkce článku a zaměřuje se na příkazy jazyka Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Příkazy syntaxe jazyka Transact-SQL s částečnými rozdíly

Základní příkazy DDL (Data Definition Language) jsou k dispozici, ale některé příkazy DDL mají rozšíření související s umístěním na disku a nepodporovanými funkcemi.

- Příkazy CREATE a ALTER databáze mají více než tři desítkové možnosti. Příkazy zahrnují možnosti umístění souborů, FILESTREAM a Service Broker, které platí pouze pro SQL Server. To může být bez ohledu na to, jestli vytváříte databáze před migrací, ale pokud migrujete kód T-SQL, který vytváří databáze, měli byste porovnat [databázi CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) se syntaxí SQL Server v [create Database (SQL Server Transact-SQL). ](https://msdn.microsoft.com/library/ms176061.aspx)aby se zajistilo, že se podporují všechny možnosti, které používáte. Vytvoření databáze pro Azure SQL Database má také možnosti cíle služby a elastického škálování, které se vztahují pouze na SQL Database.
- Příkazy CREATE a ALTER TABLE mají možnosti FileTable, které nelze použít na SQL Database, protože FILESTREAM není podporován.
- Příkazy CREATE a ALTER LOGIN jsou podporovány, ale SQL Database nenabízí všechny možnosti. Aby byla databáze lépe přenosná, SQL Database doporučuje používat uživatele databáze s omezením místo přihlášení, kdykoli je to možné. Další informace najdete v tématech [Vytvoření a změna přihlášení](https://msdn.microsoft.com/library/ms189828.aspx) a [řízení a udělení přístupu k databázi](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>V Azure SQL Database není podporovaná syntaxe jazyka Transact-SQL.

Kromě příkazů jazyka Transact-SQL, které souvisejí s nepodporovanými funkcemi popsanými v [Azure SQL Database porovnání funkcí](sql-database-features.md), nejsou podporovány následující příkazy a skupiny příkazů. Pokud vaše databáze, která má být migrována, používá některou z následujících funkcí, proveďte znovu analýzu jazyka T-SQL pro odstranění těchto funkcí a příkazů T-SQL.

- Kolace systémových objektů
- Související s připojením: Příkazy Endpoint. Služba SQL Database nepodporuje ověřování Windows, ale podporuje podobné ověřování Azure Active Directory. Některé typy ověřování vyžadují nejnovější verzi SQL Server Management Studia (SSMS). Další informace najdete v tématu [Připojení k SQL Database nebo SQL Data Warehouse pomocí ověřování služby Azure Active Directory](sql-database-aad-authentication.md).
- Mezidatabázové dotazy, které používají tři nebo čtyři názvy částí (mezidatabázové dotazy jen pro čtení jsou podporované prostřednictvím [dotazů do Elastic Database](sql-database-elastic-query-overview.md)).
- Mezidatabázové řetězení vlastnictví, nastavení `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Místo toho použijte EXECUTE AS USER.
- Šifrování je podporované s výjimkou služby EKM (extensible key management).
- Eventing Události, oznámení událostí, oznámení dotazů
- Umístění souboru: Syntaxe spojená s umístěním databázových souborů, jejich velikostí a databázovými soubory, které automaticky spravuje Microsoft Azure.
- Vysoká dostupnost: Syntaxe týkající se vysoké dostupnosti, která je spravovaná prostřednictvím účtu Microsoft Azure. Patří sem syntaxe zálohování, obnovení, Always On, zrcadlení databáze, přesouvání protokolu a režimů obnovení.
- Čtečka protokolů: Syntaxe, která spoléhá na čtečku protokolů, která není k dispozici na SQL Database: Nabízená replikace, Change Data Capture. SQL Database může být odběratelem článku nabízené replikace.
- Funkce: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Zařízení Syntaxe týkající se nastavení serveru souvisejících s hardwarem: například paměť, pracovní vlákna, spřažení procesoru, příznaky trasování. Místo toho používejte úrovně služeb a výpočetní velikosti.
- `KILL STATS JOB`
- `OPENQUERY`názvy `OPENROWSET`čtyř `OPENDATASOURCE`částí,, a
- .NET Framework: Integrace CLR s SQL Server
- Sémantické vyhledávání
- Přihlašovací údaje serveru: Místo toho použijte [přihlašovací údaje v oboru databáze](https://msdn.microsoft.com/library/mt270260.aspx) .
- Položky na úrovni serveru: Role serveru, `sys.login_token`. K dispozici nejsou příkazy `GRANT`, `REVOKE` a `DENY` serverových oprávnění, i když některé z nich jsou nahrazené oprávněními na úrovni databáze. Některá praktická serverová zobrazení dynamických zpráv (DMV) mají odpovídající databázová zobrazení dynamických zpráv.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Možnosti `sp_configure` a `RECONFIGURE`. Některé možnosti jsou dostupné prostřednictvím příkazu [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Agent SQL Server: Syntaxe, která závisí na službě SQL Server Agent nebo na databázi MSDB: výstrahy, operátory a servery centrální správy. Použijte raději skriptování, například v Azure PowerShellu.
- SQL Server Audit: Místo něho použijte auditování SQL Database.
- Trasování SQL Serveru
- Příznaky trasování: Některé příznaky trasování byly přesunuty do kompatibilních režimů.
- Ladění Transact-SQL
- Zpráv Aktivační události v rozsahu serveru nebo přihlášení
- `USE`vydá Chcete-li změnit kontext databáze na jinou databázi, je nutné vytvořit nové připojení k nové databázi.

## <a name="full-transact-sql-reference"></a>Kompletní reference k jazyku Transact-SQL

Další informace o gramatikě, použití a příkladech jazyka Transact-SQL najdete v tématu [Referenční dokumentace jazyka Transact-SQL (databázový stroj)](https://msdn.microsoft.com/library/bb510741.aspx) v SQL Server Books Online.

### <a name="about-the-applies-to-tags"></a>Informace o značkách „Platí pro“

Odkaz v jazyce Transact-SQL zahrnuje články týkající se SQL Server verzí 2008 pro stávající. Pod názvem článku je panel ikon, seznam čtyř SQL Server platforem a jejich použitelnost. Například skupiny dostupnosti byly zavedeny v SQL Serveru 2012.  [CREATE AVAILABILITY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) článku označuje, že příkaz platí pro systému  **SQL Server (počínaje verzí 2012)** . Příkaz neplatí pro SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure SQL Data Warehouse ani pro Parallel Data Warehouse.

V některých případech je možné v produktu použít obecný předmět článku, ale existují drobné rozdíly mezi produkty. Rozdíly jsou podle potřeby uvedeny v různých umístěních v článku. V některých případech je možné v produktu použít obecný předmět článku, ale existují drobné rozdíly mezi produkty. Rozdíly jsou podle potřeby uvedeny v různých umístěních v článku. Například článek vytvořit aktivační událost je k dispozici v SQL Database. Ale možnost **všechny servery** pro aktivační události na úrovni serveru označuje, že triggery na úrovni serveru nejde v SQL Database použít. Místo toho použijte triggery na úrovni databáze.

## <a name="next-steps"></a>Další postup

Seznam funkcí, které jsou v SQL Database podporované a nepodporované, najdete v článku [porovnání funkcí Azure SQL Database](sql-database-features.md). Seznam na této stránce doplňuje pokyny a funkce článku a zaměřuje se na příkazy jazyka Transact-SQL.
