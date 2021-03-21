---
title: Automatizace úloh pomocí úloh agenta SQL ve spravované instanci SQL Azure
description: Možnosti automatizace pro spouštění skriptů jazyka Transact-SQL (T-SQL) ve spravované instanci Azure SQL
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
dev_langs:
- TSQL
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 02/01/2021
ms.openlocfilehash: 3be01c304a40317e0d21baf6789ef1376cd89b80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608062"
---
# <a name="automate-management-tasks-using-sql-agent-jobs-in-azure-sql-managed-instance"></a>Automatizace úloh správy pomocí úloh agenta SQL ve spravované instanci SQL Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Pomocí [agenta SQL Server](/sql/ssms/agent/sql-server-agent) v SQL Server a [spravované instanci SQL](../../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)můžete vytvářet a plánovat úlohy, které se můžou pravidelně spouštět v jedné nebo mnoha databázích, aby se daly spouštět dotazy jazyka Transact-SQL (T-SQL) a provádět úlohy údržby. Tento článek představil agenta SQL pro spravovanou instanci SQL.

> [!Note]
> Agent SQL není k dispozici ve službě Azure SQL Database nebo Azure synapse Analytics. Místo toho doporučujeme [automatizaci úloh s elastickými úlohami](job-automation-overview.md).

### <a name="sql-agent-job-limitations-in-azure-sql-managed-instance"></a>Omezení úloh agenta SQL ve spravované instanci Azure SQL

Je potřeba poznamenat rozdíly mezi agentem SQL dostupnými v SQL Server a jako součást spravované instance SQL. Další informace o podporovaných rozdílech mezi funkcemi SQL Server a SQL Managed instance najdete v tématu [rozdíly v T-SQL spravované instance Azure SQL z SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent). 

Některé funkce agenta SQL, které jsou dostupné v SQL Server, nejsou ve spravované instanci SQL podporované:

- Nastavení agenta SQL jsou jen pro čtení. 
    - Systémová uložená procedura není `sp_set_agent_properties` ve spravované instanci SQL podporována.
- Povolení nebo zakázání agenta SQL není aktuálně podporováno ve spravované instanci SQL. Agent SQL je vždy spuštěný.
- Oznámení jsou částečně podporovaná:
  - Pager není podporován.
  - NetSend se nepodporuje.
  - Výstrahy nejsou podporovány.
- Proxy servery nejsou podporovány.
- Protokol událostí není podporován.
- Aktivační událost plánovače úloh založená na nečinném procesoru není podporována.

## <a name="when-to-use-sql-agent-jobs"></a>Kdy použít úlohy agenta SQL 

K dispozici je několik scénářů, kdy můžete použít úlohy agenta SQL:

- Automatizujte úlohy správy a naplánujte, aby se spouštěly každý den v týdnu, a to po hodinách atd.
  - Nasazování změn schématu, správa přihlašovacích údajů, shromažďování dat o výkonu nebo shromažďování telemetrických dat tenantů (zákazníků).
  - Aktualizujte referenční data (společné informace napříč všemi databázemi), načtěte data z úložiště objektů BLOB v Azure.
  - Běžné úlohy údržby, včetně příkazu DBCC CHECKDB, aby se zajistila integrita dat nebo údržba indexu pro zlepšení výkonu dotazů. Konfigurace spouštění úloh pro kolekci databází nebo jejich opakovaného spouštění, například v době mimo špičku.
  - Průběžné shromažďování výsledků dotazů ze sady databází do centrální tabulky. Výkonové dotazy je možné spouštět průběžně a nakonfigurovat tak, aby aktivovaly další úlohy, které se mají provést.
- Shromažďování dat pro účely generování sestav
  - Agregovaná data z kolekce databází do jedné cílové tabulky.
  - Spouštění dlouhotrvajících dotazů na zpracování dat pro velkou sadu databází, například shromažďování telemetrických dat uživatelů. Výsledky se pro účely další analýzy shromažďují do jedné cílové tabulky.
- Přesuny dat
  - Vytvářejte úlohy, které replikují změny provedené ve vašich databázích do jiných databází nebo shromažďují aktualizace provedené ve vzdálených databázích a aplikují změny v databázi.
  - Vytvářejte úlohy, které načítají data z databáze nebo do databází pomocí služba SSIS (SQL Server Integration Services) (SSIS).

## <a name="sql-agent-jobs-in-azure-sql-managed-instance"></a>Úlohy agenta SQL ve spravované instanci Azure SQL

Úlohy agenta SQL jsou spouštěny službou SQL Agent, která je nadále používána pro automatizaci úloh v SQL Server a spravované instanci SQL. 

Úlohy agenta SQL jsou zadané řady skriptů T-SQL pro vaši databázi. Použijte úlohy k definování úlohy správy, kterou je možné spustit jednou nebo vícekrát a monitorovat pro úspěch nebo neúspěch. 

Úlohu můžete spustit na jednom místním serveru nebo na několika vzdálených serverech. Úlohy agenta SQL jsou interní součástí databázového stroje, který je spuštěn v rámci služby SQL Managed instance.

V úlohách agenta SQL je několik klíčových konceptů:

- **Kroky úlohy** v jednom nebo několika krocích, které by měly být provedeny v rámci úlohy. Pro každý krok úlohy můžete definovat strategii opakování a akci, která se má provést, když se krok úlohy zdaří nebo selže.
- **Plány** definují, kdy se má úloha spustit.
- **Oznámení** umožňují definovat pravidla, která budou sloužit k oznamování operátorů prostřednictvím e-mailu po dokončení úlohy.

### <a name="sql-agent-job-steps"></a>Kroky úlohy agenta SQL

Kroky úlohy agenta SQL jsou sekvence akcí, které by měl Agent SQL spustit. Každý krok má následující krok, který by měl být proveden, pokud je krok úspěšný nebo neúspěšný, počet opakovaných pokusů v případě selhání.

Agent SQL vám umožňuje vytvořit různé typy kroků úlohy, jako jsou třeba kroky úlohy Transact-SQL, které provádějí jednu dávku Transact-SQL v databázi, nebo kroky příkazu/PowerShellu pro operační systém, které mohou spouštět vlastní skript operačního systému, [kroky úlohy SSIS](../../data-factory/how-to-invoke-ssis-package-managed-instance-agent.md) , které umožňují načíst data pomocí modulu runtime SSIS, nebo kroky [replikace](../managed-instance/replication-transactional-overview.md) , které mohou publikovat změny z databáze do jiných databází.

> [!Note]
> Další informace o využití Integration Runtime Azure SSIS s SSISDB hostovaným pomocí spravované instance Azure SQL najdete v tématu [použití spravované instance Azure SQL s služba SSIS (SQL Server Integration Services) (SSIS) v Azure Data Factory](../../data-factory/how-to-use-sql-managed-instance-with-ir.md).

[Transakční replikace](../managed-instance/replication-transactional-overview.md) může replikovat změny z vašich tabulek do jiných databází ve spravované instanci Azure SQL, Azure SQL Database nebo SQL Server. Informace najdete v tématu [Konfigurace replikace ve spravované instanci Azure SQL](../../azure-sql/managed-instance/replication-between-two-instances-configure-tutorial.md). 

Jiné typy kroků úlohy nejsou aktuálně podporovány ve spravované instanci SQL, včetně:

- Krok úlohy sloučení replikace se nepodporuje.
- Čtečka fronty není podporována.
- Analysis Services se nepodporují.
 
### <a name="sql-agent-job-schedules"></a>Plány úloh agenta SQL

Plán určuje, kdy úloha běží. Ve stejném plánu může běžet víc než jedna úloha a u stejné úlohy se může použít víc než jeden plán.

Plán může definovat následující podmínky pro čas spuštění úlohy:

- Vždy, když je spuštěn Agent SQL Server. Úloha se aktivuje po každém převzetí služeb při selhání.
- Jednou, v konkrétní datum a čas, který je vhodný pro opožděné provádění některých úloh.
- Podle plánu opakování.

> [!Note]
> Spravovaná instance SQL aktuálně neumožňuje spustit úlohu, když je procesor nečinný.

### <a name="sql-agent-job-notifications"></a>Oznámení úlohy agenta SQL

Úlohy agenta SQL vám umožňují dostávat oznámení, když se úloha úspěšně dokončí nebo dojde k chybě. Oznámení můžete přijímat prostřednictvím e-mailu.

Pokud už není povolená, musíte nejdřív nakonfigurovat [funkci databázová pošta](/sql/relational-databases/database-mail/database-mail) na spravované instanci SQL Azure:

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

Jako příklad cvičení nastavte e-mailový účet, který se použije k odeslání e-mailových oznámení. Přiřaďte účet k e-mailovému profilu s názvem `AzureManagedInstance_dbmail_profile` . Pokud chcete odesílat e-maily pomocí úloh agenta SQL ve spravované instanci SQL, měl by existovat profil, který se musí volat `AzureManagedInstance_dbmail_profile` . V opačném případě spravovaná instance SQL nebude moci odesílat e-maily prostřednictvím agenta SQL. Viz následující ukázka:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)';

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.';

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

Otestujte konfiguraci Databázová pošta přes T-SQL pomocí uložené procedury [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) systému:

```sql
DECLARE @body VARCHAR(4000) = 'The email is sent from ' + @@SERVERNAME;
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'AzureManagedInstance_dbmail_profile',  
    @recipients = 'ADD YOUR EMAIL HERE',  
    @body = 'Add some text',  
    @subject = 'Azure SQL Instance - test email';  
```

Můžete upozornit operátora, že se něco stalo s vašimi úlohami agenta SQL. Operátor definuje kontaktní informace pro jednotlivce zodpovědného za údržbu jedné nebo více instancí ve spravované instanci SQL. V některých případech se odpovědnosti operátorů přiřazují jednomu jednotlivci.

V systémech s více instancemi spravované instance SQL nebo SQL Server může mnoho jednotlivců sdílet zodpovědnosti operátorů. Operátor neobsahuje informace o zabezpečení a nedefinuje objekt zabezpečení. V ideálním případě se operátor nejedná o jednotlivce, jehož zodpovědnosti se mohou změnit, ale e-mailová distribuční skupina.   

Můžete [vytvořit operátory](/sql/relational-databases/system-stored-procedures/sp-add-operator-transact-sql) pomocí SQL Server Management Studio (SSMS) nebo skript Transact-SQL, který je znázorněn v následujícím příkladu:

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'AzureSQLTeam',
    @enabled=1,
    @email_address=N'AzureSQLTeamn@contoso.com';
```

Potvrďte úspěch nebo neúspěch e-mailu prostřednictvím [databázová poštaho protokolu](/sql/relational-databases/database-mail/database-mail-log-and-audits) v SSMS.

Pak můžete [upravit libovolnou úlohu agenta SQL](/sql/relational-databases/system-stored-procedures/sp-update-job-transact-sql) a přiřadit operátory, které budou upozorňovány e-mailem, pokud se úloha dokončí, selže nebo bude úspěšná pomocí SSMS nebo následujícího skriptu Transact-SQL:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'AzureSQLTeam';
```

### <a name="sql-agent-job-history"></a>Historie úloh agenta SQL

Spravovaná instance Azure SQL aktuálně neumožňuje změnit žádné vlastnosti agenta SQL, protože jsou uložené v podkladových hodnotách registru. To znamená, že možnosti pro úpravu zásad uchovávání agentů pro záznamy historie úlohy jsou opraveny ve výchozím nastavení 1000 celkových záznamů a maximální počet záznamů historie 100 na úlohu.

### <a name="sql-agent-fixed-database-role-membership"></a>Členství v pevné databázové roli agenta SQL

Pokud jsou uživatelé propojení s přihlášeními bez sysadmin nástroje přidáni do žádné ze tří pevných databázových rolí služby SQL Agent v systémové databázi msdb, existuje problém, ve kterém je třeba udělit explicitní oprávnění ke spuštění hlavním uloženým procedurám, aby tato přihlášení fungovala. V případě výskytu tohoto problému se zobrazí chybová zpráva "oprávnění EXECUTE bylo odepřeno pro objekt <object_name> (Microsoft SQL Server, chyba: 229)". 

Po přidání uživatelů do pevné databázové role SQL agenta (SQLAgentUserRole, SQLAgentReaderRole nebo role SQLAgentOperatorRole) v databázi msdb pro každé přihlášení uživatele přidané k těmto rolím spusťte níže uvedený skript T-SQL, který explicitně udělí oprávnění k provedení systémových uložených procedur uvedených v seznamu. V tomto příkladu se předpokládá, že uživatelské jméno a přihlašovací jméno jsou stejné. 

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

## <a name="learn-more"></a>Další informace

- [Co je spravovaná instance Azure SQL?](../managed-instance/sql-managed-instance-paas-overview.md)
- [Co je nového v Azure SQL Database & spravované instance SQL?](../../azure-sql/database/doc-changes-updates-release-notes.md?tabs=managed-instance)
- [Rozdíly v jazyce T-SQL spravované instance Azure SQL z SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)
- [Porovnání funkcí: Azure SQL Database a Azure SQL Managed instance](../../azure-sql/database/features-comparison.md)
