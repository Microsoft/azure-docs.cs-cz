---
title: Vytváření a správa úloh Elastic Database (Preview) pomocí jazyka Transact-SQL (T-SQL)
description: Pomocí rutiny Transact-SQL (T-SQL) spouštějte pomocí rutiny Transact-SQL skripty mezi mnoha databázemi pomocí agenta Elastic Database.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 02/07/2020
ms.openlocfilehash: 76f9fb4ed5c3b88b3a1f69e352f50079586ec336
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663328"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs-preview"></a>Vytvoření a správa úloh Elastic Database pomocí jazyka Transact-SQL (T-SQL) (Preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek poskytuje mnoho ukázkových scénářů, které vám pomohou začít pracovat s elastickými úlohami pomocí jazyka T-SQL.

V příkladech se používají [uložené procedury](#job-stored-procedures) a [zobrazení](#job-views) , které jsou k dispozici v [*databázi úloh*](job-automation-overview.md#job-database).

Pomocí jazyka Transact-SQL (T-SQL) se dají vytvářet, konfigurovat, spouštět a spravovat úlohy. Vytvoření agenta elastické úlohy není v T-SQL podporované, takže musíte nejdřív vytvořit *agenta elastické úlohy* pomocí portálu nebo [PowerShellu](elastic-jobs-powershell-create.md#create-the-elastic-job-agent).

## <a name="create-a-credential-for-job-execution"></a>Vytvoření přihlašovacích údajů pro spuštění úlohy

Přihlašovací údaje se používají pro připojení k cílovým databázím pro spuštění skriptu. K úspěšnému provedení skriptu potřebuje přihlašovací údaje příslušná oprávnění v databázích určených cílovou skupinou. Při použití logického členu skupiny nebo cílového člena fondu [systému SQL Server](logical-servers.md) se důrazně doporučuje vytvořit hlavní přihlašovací údaje, které se použijí k aktualizaci přihlašovacích údajů před rozšířením serveru a/nebo fondu v době provádění úlohy. Přihlašovací údaje v oboru databáze se vytvoří v databázi agenta úloh. Stejné přihlašovací údaje se musí použít k *Vytvoření přihlášení* a *Vytvoření uživatele z přihlašovacího jména a udělení oprávnění přihlašovací databáze* na cílové databáze.

```sql
--Connect to the job database specified when creating the job agent

-- Create a db master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create a database scoped credential.  
CREATE DATABASE SCOPED CREDENTIAL myjobcred WITH IDENTITY = 'jobcred',
    SECRET = '<EnterStrongPasswordHere>';
GO

-- Create a database scoped credential for the master database of server1.
CREATE DATABASE SCOPED CREDENTIAL mymastercred WITH IDENTITY = 'mastercred',
    SECRET = '<EnterStrongPasswordHere>';
GO
```

## <a name="create-a-target-group-servers"></a>Vytvořit cílovou skupinu (servery)

Následující příklad ukazuje, jak spustit úlohu se všemi databázemi na serveru.  
Připojte se k [*databázi úloh*](job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name = 'mymastercred', --credential required to refresh the databases in a server
@server_name = 'server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```

## <a name="exclude-an-individual-database"></a>Vyloučení individuální databáze

Následující příklad ukazuje, jak spustit úlohu se všemi databázemi na serveru s výjimkou databáze s názvem *MappingDB*.  
Připojte se k [*databázi úloh*](job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name = N'mymastercred', --credential required to refresh the databases in a server
@server_name = N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name = N'mymastercred', --credential required to refresh the databases in a server
@server_name = 'server2.database.windows.net'
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name = N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```

## <a name="create-a-target-group-pools"></a>Vytvořit cílovou skupinu (fondy)

Následující příklad ukazuje, jak cílit na všechny databáze v jednom nebo více elastických fondech.  
Připojte se k [*databázi úloh*](job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name = 'mymastercred', --credential required to refresh the databases in a server
@server_name = 'server1.database.windows.net',
@elastic_pool_name = 'ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```

## <a name="deploy-new-schema-to-many-databases"></a>Nasazení nového schématu do mnoha databází

Následující příklad ukazuje, jak nasadit nové schéma do všech databází.  
Připojte se k [*databázi úloh*](job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name = 'CreateTableTest', @description = 'Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name = 'CreateTableTest',
@command = N'IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name = 'myjobcred',
@target_group_name = 'PoolGroup'
```

## <a name="data-collection-using-built-in-parameters"></a>Shromažďování dat pomocí integrovaných parametrů

V mnoha scénářích shromažďování dat může být užitečné zahrnout některé z těchto proměnných skriptování, které vám pomůžou při následném zpracování výsledků úlohy.

- $ (job_name)
- $ (job_id)
- $ (job_version)
- $ (step_id)
- $ (step_name)
- $ (job_execution_id)
- $ (job_execution_create_time)
- $ (target_group_name)

Chcete-li například seskupit všechny výsledky ze stejného spuštění úlohy společně, použijte *$ (job_execution_id)* , jak je znázorněno v následujícím příkazu:

```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```

## <a name="monitor-database-performance"></a>Monitorování výkonu databáze

Následující příklad vytvoří novou úlohu pro shromažďování údajů o výkonu z více databází.

Ve výchozím nastavení vytvoří Agent úlohy výstupní tabulku pro uložení vrácených výsledků. Proto musí objekt zabezpečení databáze přidružený k výstupnímu přihlašovacímu údaji splňovat alespoň následující oprávnění: `CREATE TABLE` v databázi,,, `ALTER` `SELECT` `INSERT` , `DELETE` ve výstupní tabulce nebo schématu a `SELECT` v zobrazení katalogu [Sys. indexs](/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql) .

Pokud chcete tabulku vytvořit znovu před časem, musí mít následující vlastnosti:

1. Sloupce se správným názvem a datovými typy pro sadu výsledků.
2. Další sloupec pro internal_execution_id s datovým typem uniqueidentifier.
3. Neclusterovaný index s názvem `IX_<TableName>_Internal_Execution_ID` ve sloupci internal_execution_id.
4. Všechna oprávnění uvedená výše s výjimkou `CREATE TABLE` oprávnění k databázi.

Připojte se k [*databázi úloh*](job-automation-overview.md#job-database) a spusťte následující příkazy:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name = 'ResultsJob',
@command = N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name = 'myjobcred',
@target_group_name = 'PoolGroup',
@output_type = 'SqlDatabase',
@output_credential_name = 'myjobcred',
@output_server_name = 'server1.database.windows.net',
@output_database_name = '<resultsdb>',
@output_table_name = '<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = 'MasterGroup',
@target_type = 'SqlDatabase',
@server_name = 'server1.database.windows.net',
@database_name = 'master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name = 'ResultsPoolsJob',
@description = 'Demo: Collection Performance data from all pools',
@schedule_interval_type = 'Minutes',
@schedule_interval_count = 15

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name = 'myjobcred',
@target_group_name = 'MasterGroup',
@output_type = 'SqlDatabase',
@output_credential_name = 'myjobcred',
@output_server_name = 'server1.database.windows.net',
@output_database_name = 'resultsdb',
@output_table_name = 'resutlstable'
```

## <a name="view-job-definitions"></a>Zobrazit definice úloh

Následující příklad ukazuje, jak zobrazit aktuální definice úloh.  
Připojte se k [*databázi úloh*](job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j
  ON j.job_id = js.job_id AND j.job_version = js.job_version

-- View the steps of all versions of all jobs
select * from jobs.jobsteps
```

## <a name="begin-unplanned-execution-of-a-job"></a>Zahájení neplánovaného provádění úlohy

Následující příklad ukazuje, jak okamžitě spustit úlohu.  
Připojte se k [*databázi úloh*](job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest'

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output
select @je

select * from jobs.job_executions where job_execution_id = @je

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1
```

## <a name="schedule-execution-of-a-job"></a>Plánování provádění úlohy

Následující příklad ukazuje, jak naplánovat úlohu pro pozdější spuštění.  
Připojte se k [*databázi úloh*](job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name = 'ResultsJob',
@enabled=1,
@schedule_interval_type = 'Minutes',
@schedule_interval_count = 15
```

## <a name="monitor-job-execution-status"></a>Monitorovat stav spuštění úlohy

Následující příklad ukazuje, jak zobrazit podrobnosti o stavu spuštění pro všechny úlohy.  
Připojte se k [*databázi úloh*](job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions
WHERE job_name = 'ResultsPoolsJob'
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions
WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="cancel-a-job"></a>Zrušení úlohy

Následující příklad ukazuje, jak zrušit úlohu.  
Připojte se k [*databázi úloh*](job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef'
```

## <a name="delete-old-job-history"></a>Odstranit starou historii úlohy

Následující příklad ukazuje, jak odstranit historii úlohy před určitým datem.  
Připojte se k [*databázi úloh*](job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Odstraní úlohu a celou její historii úloh.

Následující příklad ukazuje, jak odstranit úlohu a všechny související historie úloh.  
Připojte se k [*databázi úloh*](job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="job-stored-procedures"></a>Uložené procedury úlohy

Následující uložené procedury jsou v [databázi Jobs](job-automation-overview.md#job-database).

|Uložená procedura  |Description  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Přidá novou úlohu.    |
|[sp_update_job](#sp_update_job)    |      Aktualizuje existující úlohu.   |
|[sp_delete_job](#sp_delete_job)     |      Odstraní existující úlohu.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Přidá krok do úlohy.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Aktualizuje krok úlohy.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Odstraní krok úlohy.    |
|[sp_start_job](#sp_start_job)    |  Spustí provádění úlohy.       |
|[sp_stop_job](#sp_stop_job)     |     Zastaví provádění úlohy.   |
|[sp_add_target_group](#sp_add_target_group)    |     Přidá cílovou skupinu.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Odstraní cílovou skupinu.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Přidá databázi nebo skupinu databází do cílové skupiny.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     Odebere člena cílové skupiny z cílové skupiny.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Odebere záznamy historie pro úlohu.     |

### <a name="sp_add_job"></a><a name="sp_add_job"></a>sp_add_job

Přidá novou úlohu.
  
#### <a name="syntax"></a>Syntaxe  
  
```syntaxsql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
  [ , [ @description = ] 'description' ]
  [ , [ @enabled = ] enabled ]
  [ , [ @schedule_interval_type = ] schedule_interval_type ]  
  [ , [ @schedule_interval_count = ] schedule_interval_count ]
  [ , [ @schedule_start_time = ] schedule_start_time ]
  [ , [ @schedule_end_time = ] schedule_end_time ]
  [ , [ @job_id = ] job_id OUTPUT ]
```

#### <a name="arguments"></a>Argumenty  

[ **\@ job_name =** ] ' job_name '  
Název úlohy. Název musí být jedinečný a nesmí obsahovat procenta (%). optické. job_name je nvarchar (128) bez výchozího nastavení.

[ **\@ Description =** ] ' Description '  
Popis úlohy Description je nvarchar (512) s výchozí hodnotou NULL. Pokud je popis vynechán, je použit prázdný řetězec.

[ **\@ Enabled =** ] povoleno  
Určuje, jestli je plán úlohy povolený. Enabled je bit s výchozí hodnotou 0 (zakázáno). Pokud je 0, úloha není povolená a neběží podle jejího plánu. dá se ale spustit ručně. Pokud je nastavená na 1, úloha se spustí podle plánu a dá se spustit taky ručně.

[ **\@ schedule_interval_type =**] schedule_interval_type  
Hodnota označuje, kdy má být úloha spuštěna. schedule_interval_type je nvarchar (50), má výchozí hodnotu jednou a může to být jedna z následujících hodnot:

- ' Jednou ',
- ' Minuty ',
- ' Hodiny ',
- ' Days ',
- ' Týdny ',
- Měsíci

[ **\@ schedule_interval_count =** ] schedule_interval_count  
Počet schedule_interval_count období mezi jednotlivými spuštěními úlohy. schedule_interval_count je int, výchozí hodnota je 1. Hodnota musí být větší než nebo rovna 1.

[ **\@ schedule_start_time =** ] schedule_start_time  
Datum, kdy může spuštění úlohy začít. schedule_start_time je DATETIME2, výchozí hodnota je 0001-01-01 00:00:00.0000000.

[ **\@ schedule_end_time =** ] schedule_end_time  
Datum, kdy se může zastavit spuštění úlohy. schedule_end_time je DATETIME2, výchozí hodnota je 9999-12-31 11:59:59.0000000.

[ **\@ job_id =** ] výstup job_id  
Identifikační číslo úlohy přiřazené úloze, pokud bylo úspěšně vytvořeno. job_id je výstupní proměnná typu uniqueidentifier.

#### <a name="return-code-values"></a>Hodnoty návratového kódu

0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky

sp_add_job je třeba spustit z databáze agenta úlohy zadané při vytváření agenta úloh.
Po provedení sp_add_job pro přidání úlohy sp_add_jobstep lze použít k přidání kroků, které provádějí aktivity pro úlohu. Počáteční číslo verze úlohy je 0, což se při přidání prvního kroku zvýší na 1.

#### <a name="permissions"></a>Oprávnění

Ve výchozím nastavení mohou tuto uloženou proceduru spustit členové pevné role serveru sysadmin. Omezují uživatele jenom na to, že budou moct monitorovat úlohy, a vy můžete v databázi agenta úlohy zadat, aby se při vytváření agenta úloh účastnila Tato databázová role:

- jobs_reader

Podrobnosti o oprávnění těchto rolí najdete v části oprávnění v tomto dokumentu. Tuto uloženou proceduru mohou použít pouze členové sysadmin k úpravě atributů úloh, které vlastní jiní uživatelé.

### <a name="sp_update_job"></a><a name="sp_update_job"></a>sp_update_job

Aktualizuje existující úlohu.

#### <a name="syntax"></a>Syntaxe

```syntaxsql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
  [ , [ @new_name = ] 'new_name' ]
  [ , [ @description = ] 'description' ]
  [ , [ @enabled = ] enabled ]
  [ , [ @schedule_interval_type = ] schedule_interval_type ]  
  [ , [ @schedule_interval_count = ] schedule_interval_count ]
  [ , [ @schedule_start_time = ] schedule_start_time ]
  [ , [ @schedule_end_time = ] schedule_end_time ]
```

#### <a name="arguments"></a>Argumenty

[ **\@ job_name =** ] ' job_name '  
Název úlohy, která se má aktualizovat. job_name je nvarchar (128).

[ **\@ new_name =** ] ' new_name '  
Nový název úlohy. new_name je nvarchar (128).

[ **\@ Description =** ] ' Description '  
Popis úlohy Popis je nvarchar (512).

[ **\@ Enabled =** ] povoleno  
Určuje, jestli je plán úlohy povolený (1) nebo není povolený (0). Povoleno je bit.

[ **\@ schedule_interval_type =** ] schedule_interval_type  
Hodnota označuje, kdy má být úloha spuštěna. schedule_interval_type je nvarchar (50) a může to být jedna z následujících hodnot:

- ' Jednou ',
- ' Minuty ',
- ' Hodiny ',
- ' Days ',
- ' Týdny ',
- Měsíci

[ **\@ schedule_interval_count =** ] schedule_interval_count  
Počet schedule_interval_count období mezi jednotlivými spuštěními úlohy. schedule_interval_count je int, výchozí hodnota je 1. Hodnota musí být větší než nebo rovna 1.

[ **\@ schedule_start_time =** ] schedule_start_time  
Datum, kdy může spuštění úlohy začít. schedule_start_time je DATETIME2, výchozí hodnota je 0001-01-01 00:00:00.0000000.

[ **\@ schedule_end_time =** ] schedule_end_time  
Datum, kdy se může zastavit spuštění úlohy. schedule_end_time je DATETIME2, výchozí hodnota je 9999-12-31 11:59:59.0000000.

#### <a name="return-code-values"></a>Hodnoty návratového kódu

0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky

Po provedení sp_add_job pro přidání úlohy sp_add_jobstep lze použít k přidání kroků, které provádějí aktivity pro úlohu. Počáteční číslo verze úlohy je 0, což se při přidání prvního kroku zvýší na 1.

#### <a name="permissions"></a>Oprávnění

Ve výchozím nastavení mohou tuto uloženou proceduru spustit členové pevné role serveru sysadmin. Omezují uživatele jenom na to, že budou moct monitorovat úlohy, a vy můžete v databázi agenta úlohy zadat, aby se při vytváření agenta úloh účastnila Tato databázová role:

- jobs_reader

Podrobnosti o oprávnění těchto rolí najdete v části oprávnění v tomto dokumentu. Tuto uloženou proceduru mohou použít pouze členové sysadmin k úpravě atributů úloh, které vlastní jiní uživatelé.

### <a name="sp_delete_job"></a><a name="sp_delete_job"></a>sp_delete_job

Odstraní existující úlohu.

#### <a name="syntax"></a>Syntaxe

```syntaxsql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
  [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumenty

[ **\@ job_name =** ] ' job_name '  
Název úlohy, která se má odstranit job_name je nvarchar (128).

[ **\@ Force =** ] Force  
Určuje, jestli se má odstranit v případě, že úloha obsahuje nějaké probíhající úlohy, a zrušte všechny probíhající úlohy (1) nebo selže, pokud dojde k probíhajícímu provádění úloh (0). síla je bit.

#### <a name="return-code-values"></a>Hodnoty návratového kódu

0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky

Historie úlohy je automaticky odstraněna při odstranění úlohy.

#### <a name="permissions"></a>Oprávnění

Ve výchozím nastavení mohou tuto uloženou proceduru spustit členové pevné role serveru sysadmin. Omezují uživatele jenom na to, že budou moct monitorovat úlohy, a vy můžete v databázi agenta úlohy zadat, aby se při vytváření agenta úloh účastnila Tato databázová role:

- jobs_reader

Podrobnosti o oprávnění těchto rolí najdete v části oprávnění v tomto dokumentu. Tuto uloženou proceduru mohou použít pouze členové sysadmin k úpravě atributů úloh, které vlastní jiní uživatelé.

### <a name="sp_add_jobstep"></a><a name="sp_add_jobstep"></a>sp_add_jobstep

Přidá krok do úlohy.

#### <a name="syntax"></a>Syntaxe

```syntaxsql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] step_name ]
     [ , [ @command_type = ] 'command_type' ]
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]
     [ , [ @retry_attempts = ] retry_attempts ]
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]
     [ , [ @output_type = ] 'output_type' ]
     [ , [ @output_credential_name = ] 'output_credential_name' ]
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]
     [ , [ @output_server_name = ] 'output_server_name' ]
     [ , [ @output_database_name = ] 'output_database_name' ]
     [ , [ @output_schema_name = ] 'output_schema_name' ]
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumenty

[ **\@ job_name =** ] ' job_name '  
Název úlohy, do které chcete krok přidat. job_name je nvarchar (128).

[ **\@ step_id =** ] step_id  
Identifikační číslo sekvence pro krok úlohy Identifikační čísla kroků začínají 1 a se zvětšují bez mezer. Pokud již existuje stávající krok s tímto ID, pak tento krok a všechny následující kroky budou mít své ID zvýšeny, aby tento nový krok mohl být vložen do sekvence. Pokud není zadaný, step_id se v pořadí kroků automaticky přiřadí k poslednímu. step_id je int.

[ **\@ STEP_NAME =** ] STEP_NAME  
Název kroku. Musí být zadáno, s výjimkou prvního kroku úlohy, která (pro usnadnění) má výchozí název "JobStep". step_name je nvarchar (128).

[ **\@ command_type =** ] ' command_type '  
Typ příkazu, který je proveden tímto jobstep. command_type je nvarchar (50) s výchozí hodnotou TSql, což znamená, že hodnota @command_type parametru je skript T-SQL.

Je-li tento parametr zadán, musí být hodnota TSql.

[ **\@ command_source =** ] ' command_source '  
Typ umístění, kde je příkaz uložen. command_source je nvarchar (50) s výchozí hodnotou inline, což znamená, že hodnota @command_source parametru je literálový text příkazu.

Je-li tento parametr zadán, musí být vložena hodnota.

[ **\@ Command =** ] – příkaz  
Příkaz musí být platný skript T-SQL, který pak provede tento krok úlohy. příkaz má hodnotu nvarchar (max), která má výchozí hodnotu NULL.

[ **\@ credential_name =** ] ' credential_name '  
Název databáze s rozsahem přihlašovacích údajů uložených v této databázi řízení úloh, která se používá pro připojení ke každé z cílových databází v cílové skupině v případě provedení tohoto kroku. credential_name je nvarchar (128).

[ **\@ target_group_name =** ] ' Target-group_name '  
Název cílové skupiny, která obsahuje cílové databáze, na kterých se spustí krok úlohy. target_group_name je nvarchar (128).

[ **\@ initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Zpoždění před prvním pokusem, pokud se krok úlohy nezdaří při prvotním pokusu o spuštění. initial_retry_interval_seconds je int s výchozí hodnotou 1.

[ **\@ maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maximální prodleva mezi opakovanými pokusy. Pokud by zpoždění mezi opakovanými pokusy růst větší než tato hodnota, je místo toho omezené na tuto hodnotu. maximum_retry_interval_seconds je int s výchozí hodnotou 120.

[ **\@ retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Násobitel, který se má použít u zpoždění při opakování, pokud se několik pokusů o spuštění kroku úlohy nezdaří. Například pokud první pokus má zpoždění 5 sekund a násobitel omezení rychlosti je 2,0, pak druhé opakování bude mít zpoždění 10 sekund a třetí opakování bude mít zpoždění 20 sekund. retry_interval_backoff_multiplier je reálné, má výchozí hodnotu 2,0.

[ **\@ retry_attempts =** ] retry_attempts  
Počet pokusů o opakování provedení, pokud se počáteční pokus nezdaří Pokud má například retry_attempts hodnotu 10, bude se jednat o 1 počáteční pokus a 10 opakovaných pokusů, což bude mít celkem 11 pokusů. Pokud se poslední pokus o opakování nezdaří, spuštění úlohy skončí s neúspěšným životním cyklem. retry_attempts je int s výchozí hodnotou 10.

[ **\@ step_timeout_seconds =** ] step_timeout_seconds  
Maximální doba, po kterou je možné krok provést. V případě překročení tohoto času se provádění úlohy ukončí s časovým limitem vypršení časového limitu. step_timeout_seconds je int, přičemž výchozí hodnota je 43 200 sekund (12 hodin).

[ **\@ output_type =** ] ' output_type '  
Pokud hodnota není null, typ cíle, na který je první sada výsledků příkazu zapisována. output_type je nvarchar (50) s výchozí hodnotou NULL.

Je-li tento parametr zadán, musí být hodnota SqlDatabase.

[ **\@ output_credential_name =** ] ' output_credential_name '  
Pokud není null, název databáze s rozsahem pověření, která se používá pro připojení k výstupní cílové databázi. Je nutné zadat, pokud output_type rovná SqlDatabase. output_credential_name je nvarchar (128), přičemž výchozí hodnota je NULL.

[ **\@ output_subscription_id =** ] ' output_subscription_id '  
Vyžaduje popis.

[ **\@ output_resource_group_name =** ] ' output_resource_group_name '  
Vyžaduje popis.

[ **\@ output_server_name =** ] ' output_server_name '  
Pokud hodnota není null, plně kvalifikovaný název DNS serveru, který obsahuje výstupní cílovou databázi. Je nutné zadat, pokud output_type rovná SqlDatabase. output_server_name je nvarchar (256), má výchozí hodnotu NULL.

[ **\@ output_database_name =** ] ' output_database_name '  
Pokud hodnota není null, název databáze obsahující výstupní cílovou tabulku. Je nutné zadat, pokud output_type rovná SqlDatabase. output_database_name je nvarchar (128), přičemž výchozí hodnota je NULL.

[ **\@ output_schema_name =** ] ' output_schema_name '  
Pokud není null, název schématu SQL obsahující výstupní cílovou tabulku. Pokud output_type rovná SqlDatabase, výchozí hodnota je dbo. output_schema_name je nvarchar (128).

[ **\@ output_table_name =** ] ' output_table_name '  
Pokud hodnota není null, název tabulky, do které bude první sada výsledků příkazu zapisován. Pokud tabulka ještě neexistuje, vytvoří se na základě schématu vrácení sady výsledků. Je nutné zadat, pokud output_type rovná SqlDatabase. output_table_name je nvarchar (128), přičemž výchozí hodnota je NULL.

[ **\@ job_version =** ] výstup job_version  
Výstupní parametr, kterému bude přiřazeno nové číslo verze úlohy job_version je int.

[ **\@ max_parallelism =** ] výstup max_parallelism  
Maximální úroveň paralelismu na elastický fond. Když se nastaví, krok úlohy se omezí tak, aby se spouštěl jenom z maximálního počtu databází na elastický fond. To platí pro každý elastický fond, který je buď přímo součástí cílové skupiny, nebo se nachází uvnitř serveru, který je součástí cílové skupiny. max_parallelism je int.

#### <a name="return-code-values"></a>Hodnoty návratového kódu

0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky

Po úspěšném sp_add_jobstep se zvýší aktuální číslo verze úlohy. Při dalším spuštění úlohy se použije nová verze. Pokud se úloha právě provádí, toto spuštění nebude obsahovat nový krok.

#### <a name="permissions"></a>Oprávnění

Ve výchozím nastavení mohou tuto uloženou proceduru spustit členové pevné role serveru sysadmin. Omezují uživatele jenom na to, že budou moct monitorovat úlohy, a vy můžete v databázi agenta úlohy zadat, aby se při vytváření agenta úloh účastnila Tato databázová role:  

- jobs_reader

Podrobnosti o oprávnění těchto rolí najdete v části oprávnění v tomto dokumentu. Tuto uloženou proceduru mohou použít pouze členové sysadmin k úpravě atributů úloh, které vlastní jiní uživatelé.

### <a name="sp_update_jobstep"></a><a name="sp_update_jobstep"></a>sp_update_jobstep

Aktualizuje krok úlohy.

#### <a name="syntax"></a>Syntaxe

```syntaxsql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]
     [ , [ @new_id = ] new_id ]
     [ , [ @new_name = ] 'new_name' ]
     [ , [ @command_type = ] 'command_type' ]
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]
     [ , [ @retry_attempts = ] retry_attempts ]
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]
     [ , [ @output_type = ] 'output_type' ]
     [ , [ @output_credential_name = ] 'output_credential_name' ]
     [ , [ @output_server_name = ] 'output_server_name' ]
     [ , [ @output_database_name = ] 'output_database_name' ]
     [ , [ @output_schema_name = ] 'output_schema_name' ]
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumenty

[ **\@ job_name =** ] ' job_name '  
Název úlohy, ke které krok patří job_name je nvarchar (128).

[ **\@ step_id =** ] step_id  
Identifikační číslo pro krok úlohy, který se má upravit. Musí být zadán buď step_id, nebo step_name. step_id je int.

[ **\@ STEP_NAME =** ] ' STEP_NAME '  
Název kroku, který má být upraven. Musí být zadán buď step_id, nebo step_name. step_name je nvarchar (128).

[ **\@ new_id =** ] new_id  
Nové identifikační číslo sekvence pro krok úlohy. Identifikační čísla kroků začínají 1 a se zvětšují bez mezer. Pokud se změní pořadí kroků, automaticky se přečíslují i další kroky.

[ **\@ new_name =** ] ' new_name '  
Nový název kroku. new_name je nvarchar (128).

[ **\@ command_type =** ] ' command_type '  
Typ příkazu, který je proveden tímto jobstep. command_type je nvarchar (50) s výchozí hodnotou TSql, což znamená, že hodnota @command_type parametru je skript T-SQL.

Je-li tento parametr zadán, musí být hodnota TSql.

[ **\@ command_source =** ] ' command_source '  
Typ umístění, kde je příkaz uložen. command_source je nvarchar (50) s výchozí hodnotou inline, což znamená, že hodnota @command_source parametru je literálový text příkazu.

Je-li tento parametr zadán, musí být vložena hodnota.

[ **\@ Command =** ] – příkaz  
Příkazy musí být platný skript T-SQL a pak se spustí tímto krokem úlohy. příkaz má hodnotu nvarchar (max), která má výchozí hodnotu NULL.

[ **\@ credential_name =** ] ' credential_name '  
Název databáze s rozsahem přihlašovacích údajů uložených v této databázi řízení úloh, která se používá pro připojení ke každé z cílových databází v cílové skupině v případě provedení tohoto kroku. credential_name je nvarchar (128).

[ **\@ target_group_name =** ] ' Target-group_name '  
Název cílové skupiny, která obsahuje cílové databáze, na kterých se spustí krok úlohy. target_group_name je nvarchar (128).

[ **\@ initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Zpoždění před prvním pokusem, pokud se krok úlohy nezdaří při prvotním pokusu o spuštění. initial_retry_interval_seconds je int s výchozí hodnotou 1.

[ **\@ maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maximální prodleva mezi opakovanými pokusy. Pokud by zpoždění mezi opakovanými pokusy růst větší než tato hodnota, je místo toho omezené na tuto hodnotu. maximum_retry_interval_seconds je int s výchozí hodnotou 120.

[ **\@ retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Násobitel, který se má použít u zpoždění při opakování, pokud se několik pokusů o spuštění kroku úlohy nezdaří. Například pokud první pokus má zpoždění 5 sekund a násobitel omezení rychlosti je 2,0, pak druhé opakování bude mít zpoždění 10 sekund a třetí opakování bude mít zpoždění 20 sekund. retry_interval_backoff_multiplier je reálné, má výchozí hodnotu 2,0.

[ **\@ retry_attempts =** ] retry_attempts  
Počet pokusů o opakování provedení, pokud se počáteční pokus nezdaří Pokud má například retry_attempts hodnotu 10, bude se jednat o 1 počáteční pokus a 10 opakovaných pokusů, což bude mít celkem 11 pokusů. Pokud se poslední pokus o opakování nezdaří, spuštění úlohy skončí s neúspěšným životním cyklem. retry_attempts je int s výchozí hodnotou 10.

[ **\@ step_timeout_seconds =** ] step_timeout_seconds  
Maximální doba, po kterou je možné krok provést. V případě překročení tohoto času se provádění úlohy ukončí s časovým limitem vypršení časového limitu. step_timeout_seconds je int, přičemž výchozí hodnota je 43 200 sekund (12 hodin).

[ **\@ output_type =** ] ' output_type '  
Pokud hodnota není null, typ cíle, na který je první sada výsledků příkazu zapisována. Chcete-li obnovit hodnotu output_type zpět na hodnotu NULL, nastavte hodnotu parametru na ' ' (prázdný řetězec). output_type je nvarchar (50) s výchozí hodnotou NULL.

Je-li tento parametr zadán, musí být hodnota SqlDatabase.

[ **\@ output_credential_name =** ] ' output_credential_name '  
Pokud není null, název databáze s rozsahem pověření, která se používá pro připojení k výstupní cílové databázi. Je nutné zadat, pokud output_type rovná SqlDatabase. Chcete-li obnovit hodnotu output_credential_name zpět na hodnotu NULL, nastavte hodnotu parametru na ' ' (prázdný řetězec). output_credential_name je nvarchar (128), přičemž výchozí hodnota je NULL.

[ **\@ output_server_name =** ] ' output_server_name '  
Pokud hodnota není null, plně kvalifikovaný název DNS serveru, který obsahuje výstupní cílovou databázi. Je nutné zadat, pokud output_type rovná SqlDatabase. Chcete-li obnovit hodnotu output_server_name zpět na hodnotu NULL, nastavte hodnotu parametru na ' ' (prázdný řetězec). output_server_name je nvarchar (256), má výchozí hodnotu NULL.

[ **\@ output_database_name =** ] ' output_database_name '  
Pokud hodnota není null, název databáze obsahující výstupní cílovou tabulku. Je nutné zadat, pokud output_type rovná SqlDatabase. Chcete-li obnovit hodnotu output_database_name zpět na hodnotu NULL, nastavte hodnotu parametru na ' ' (prázdný řetězec). output_database_name je nvarchar (128), přičemž výchozí hodnota je NULL.

[ **\@ output_schema_name =** ] ' output_schema_name '  
Pokud není null, název schématu SQL obsahující výstupní cílovou tabulku. Pokud output_type rovná SqlDatabase, výchozí hodnota je dbo. Chcete-li obnovit hodnotu output_schema_name zpět na hodnotu NULL, nastavte hodnotu parametru na ' ' (prázdný řetězec). output_schema_name je nvarchar (128).

[ **\@ output_table_name =** ] ' output_table_name '  
Pokud hodnota není null, název tabulky, do které bude první sada výsledků příkazu zapisován. Pokud tabulka ještě neexistuje, vytvoří se na základě schématu vrácení sady výsledků. Je nutné zadat, pokud output_type rovná SqlDatabase. Chcete-li obnovit hodnotu output_server_name zpět na hodnotu NULL, nastavte hodnotu parametru na ' ' (prázdný řetězec). output_table_name je nvarchar (128), přičemž výchozí hodnota je NULL.

[ **\@ job_version =** ] výstup job_version  
Výstupní parametr, kterému bude přiřazeno nové číslo verze úlohy job_version je int.

[ **\@ max_parallelism =** ] výstup max_parallelism  
Maximální úroveň paralelismu na elastický fond. Když se nastaví, krok úlohy se omezí tak, aby se spouštěl jenom z maximálního počtu databází na elastický fond. To platí pro každý elastický fond, který je buď přímo součástí cílové skupiny, nebo se nachází uvnitř serveru, který je součástí cílové skupiny. Chcete-li obnovit hodnotu max_parallelism zpět na hodnotu null, nastavte tento parametr na hodnotu-1. max_parallelism je int.

#### <a name="return-code-values"></a>Hodnoty návratového kódu

0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky

Jakékoli probíhající spouštění úlohy nebudou ovlivněny. Po úspěšném sp_update_jobstep se zvýší číslo verze úlohy. Při dalším spuštění úlohy se použije nová verze.

#### <a name="permissions"></a>Oprávnění

Ve výchozím nastavení mohou tuto uloženou proceduru spustit členové pevné role serveru sysadmin. Omezují uživatele jenom na to, že budou moct monitorovat úlohy, a vy můžete v databázi agenta úlohy zadat, aby se při vytváření agenta úloh účastnila Tato databázová role:

- jobs_reader

Podrobnosti o oprávnění těchto rolí najdete v části oprávnění v tomto dokumentu. Tuto uloženou proceduru mohou použít pouze členové sysadmin k úpravě atributů úloh, které vlastní jiní uživatelé.

### <a name="sp_delete_jobstep"></a><a name="sp_delete_jobstep"></a>sp_delete_jobstep

Odebere krok úlohy z úlohy.

#### <a name="syntax"></a>Syntaxe

```syntaxsql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumenty

[ **\@ job_name =** ] ' job_name '  
Název úlohy, ze které se bude krok odebrat job_name je nvarchar (128) bez výchozího nastavení.

[ **\@ step_id =** ] step_id  
Identifikační číslo pro krok úlohy, který se má odstranit Musí být zadán buď step_id, nebo step_name. step_id je int.

[ **\@ STEP_NAME =** ] ' STEP_NAME '  
Název kroku, který se má odstranit Musí být zadán buď step_id, nebo step_name. step_name je nvarchar (128).

[ **\@ job_version =** ] výstup job_version  
Výstupní parametr, kterému bude přiřazeno nové číslo verze úlohy job_version je int.

#### <a name="return-code-values"></a>Hodnoty návratového kódu

0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky

Jakékoli probíhající spouštění úlohy nebudou ovlivněny. Po úspěšném sp_update_jobstep se zvýší číslo verze úlohy. Při dalším spuštění úlohy se použije nová verze.

Ostatní kroky úlohy budou automaticky přečíslované, aby vyplnila mezeru levou krok odstraněné úlohy.

#### <a name="permissions"></a>Oprávnění

Ve výchozím nastavení mohou tuto uloženou proceduru spustit členové pevné role serveru sysadmin. Omezují uživatele jenom na to, že budou moct monitorovat úlohy, a vy můžete v databázi agenta úlohy zadat, aby se při vytváření agenta úloh účastnila Tato databázová role:

- jobs_reader

Podrobnosti o oprávnění těchto rolí najdete v části oprávnění v tomto dokumentu. Tuto uloženou proceduru mohou použít pouze členové sysadmin k úpravě atributů úloh, které vlastní jiní uživatelé.

### <a name="sp_start_job"></a><a name="sp_start_job"></a>sp_start_job

Spustí provádění úlohy.

#### <a name="syntax"></a>Syntaxe

```syntaxsql
[jobs].sp_start_job [ @job_name = ] 'job_name'
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]
```

#### <a name="arguments"></a>Argumenty

[ **\@ job_name =** ] ' job_name '  
Název úlohy, ze které se bude krok odebrat job_name je nvarchar (128) bez výchozího nastavení.

[ **\@ job_execution_id =** ] výstup job_execution_id  
Výstupní parametr, kterému bude přiřazeno ID spuštění úlohy. job_version je typ uniqueidentifier.

#### <a name="return-code-values"></a>Hodnoty návratového kódu

0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky

Žádné

#### <a name="permissions"></a>Oprávnění

Ve výchozím nastavení mohou tuto uloženou proceduru spustit členové pevné role serveru sysadmin. Omezují uživatele jenom na to, že budou moct monitorovat úlohy, a vy můžete v databázi agenta úlohy zadat, aby se při vytváření agenta úloh účastnila Tato databázová role:

- jobs_reader

Podrobnosti o oprávnění těchto rolí najdete v části oprávnění v tomto dokumentu. Tuto uloženou proceduru mohou použít pouze členové sysadmin k úpravě atributů úloh, které vlastní jiní uživatelé.

### <a name="sp_stop_job"></a><a name="sp_stop_job"></a>sp_stop_job

Zastaví provádění úlohy.

#### <a name="syntax"></a>Syntaxe

```syntaxsql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```

#### <a name="arguments"></a>Argumenty

[ **\@ job_execution_id =** ] job_execution_id  
Identifikační číslo spuštění úlohy, které se má zastavit. job_execution_id je uniqueidentifier a má výchozí hodnotu NULL.

#### <a name="return-code-values"></a>Hodnoty návratového kódu

0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky

Žádné

#### <a name="permissions"></a>Oprávnění

Ve výchozím nastavení mohou tuto uloženou proceduru spustit členové pevné role serveru sysadmin. Omezují uživatele jenom na to, že budou moct monitorovat úlohy, a vy můžete v databázi agenta úlohy zadat, aby se při vytváření agenta úloh účastnila Tato databázová role:

- jobs_reader

Podrobnosti o oprávnění těchto rolí najdete v části oprávnění v tomto dokumentu. Tuto uloženou proceduru mohou použít pouze členové sysadmin k úpravě atributů úloh, které vlastní jiní uživatelé.

### <a name="sp_add_target_group"></a><a name="sp_add_target_group"></a>sp_add_target_group

Přidá cílovou skupinu.

#### <a name="syntax"></a>Syntaxe

```syntaxsql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```

#### <a name="arguments"></a>Argumenty

[ **\@ target_group_name =** ] ' target_group_name '  
Název cílové skupiny, která se má vytvořit. target_group_name je nvarchar (128) bez výchozího nastavení.

[ **\@ target_group_id =** ] target_group_id výstup identifikačního čísla cílové skupiny přiřazeného k úloze v případě úspěšného vytvoření. target_group_id je výstupní proměnná typu uniqueidentifier s výchozí hodnotou NULL.

#### <a name="return-code-values"></a>Hodnoty návratového kódu

0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky

Cílové skupiny poskytují snadný způsob, jak zaměřit úlohu na kolekci databází.

#### <a name="permissions"></a>Oprávnění

Ve výchozím nastavení mohou tuto uloženou proceduru spustit členové pevné role serveru sysadmin. Omezují uživatele jenom na to, že budou moct monitorovat úlohy, a vy můžete v databázi agenta úlohy zadat, aby se při vytváření agenta úloh účastnila Tato databázová role:

- jobs_reader

Podrobnosti o oprávnění těchto rolí najdete v části oprávnění v tomto dokumentu. Tuto uloženou proceduru mohou použít pouze členové sysadmin k úpravě atributů úloh, které vlastní jiní uživatelé.

### <a name="sp_delete_target_group"></a><a name="sp_delete_target_group"></a>sp_delete_target_group

Odstraní cílovou skupinu.

#### <a name="syntax"></a>Syntaxe

```syntaxsql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```

#### <a name="arguments"></a>Argumenty

[ **\@ target_group_name =** ] ' target_group_name '  
Název cílové skupiny, která se má odstranit target_group_name je nvarchar (128) bez výchozího nastavení.

#### <a name="return-code-values"></a>Hodnoty návratového kódu

0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky

Žádné

#### <a name="permissions"></a>Oprávnění

Ve výchozím nastavení mohou tuto uloženou proceduru spustit členové pevné role serveru sysadmin. Omezují uživatele jenom na to, že budou moct monitorovat úlohy, a vy můžete v databázi agenta úlohy zadat, aby se při vytváření agenta úloh účastnila Tato databázová role:

- jobs_reader

Podrobnosti o oprávnění těchto rolí najdete v části oprávnění v tomto dokumentu. Tuto uloženou proceduru mohou použít pouze členové sysadmin k úpravě atributů úloh, které vlastní jiní uživatelé.

### <a name="sp_add_target_group_member"></a><a name="sp_add_target_group_member"></a>sp_add_target_group_member

Přidá databázi nebo skupinu databází do cílové skupiny.

#### <a name="syntax"></a>Syntaxe

```syntaxsql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]
        [ , [ @target_type = ] 'target_type' ]
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]
        [ , [ @server_name = ] 'server_name' ]
        [ , [ @database_name = ] 'database_name' ]
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]
        [ , [ @shard_map_name = ] 'shard_map_name' ]
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>Argumenty

[ **\@ target_group_name =** ] ' target_group_name '  
Název cílové skupiny, do které bude člen přidán. target_group_name je nvarchar (128) bez výchozího nastavení.

[ **\@ membership_type =** ] ' membership_type '  
Určuje, jestli bude cílový člen skupiny zahrnutý nebo vyloučený. target_group_name je nvarchar (128) s výchozím nastavením include. Platné hodnoty pro membership_type jsou include nebo Exclude.

[ **\@ target_type =** ] ' target_type '  
Typ cílové databáze nebo kolekce databází včetně všech databází na serveru, všech databází v elastickém fondu, všech databází v mapě horizontálních oddílů nebo jednotlivé databáze. target_type je nvarchar (128) bez výchozího nastavení. Platné hodnoty pro target_type jsou "SqlServer", "SqlElasticPool", "SqlDatabase" nebo "SqlShardMap".

[ **\@ refresh_credential_name =** ] ' refresh_credential_name '  
Název databáze s rozsahem pověření. refresh_credential_name je nvarchar (128) bez výchozího nastavení.

[ **\@ server_name =** ] ' server_name '  
Název serveru, který má být přidán do zadané cílové skupiny. Pokud target_type je SqlServer, měla by se zadat server_name. server_name je nvarchar (128) bez výchozího nastavení.

[ **\@ database_name =** ] ' database_name '  
Název databáze, která se má přidat do zadané cílové skupiny. Pokud je target_type ' SqlDatabase ', je třeba zadat database_name. database_name je nvarchar (128) bez výchozího nastavení.

[ **\@ elastic_pool_name =** ] ' elastic_pool_name '  
Název elastického fondu, který má být přidán do zadané cílové skupiny. Pokud je target_type ' SqlElasticPool ', je třeba zadat elastic_pool_name. elastic_pool_name je nvarchar (128) bez výchozího nastavení.

[ **\@ shard_map_name =** ] ' shard_map_name '  
Název fondu mapování horizontálních oddílů, který se má přidat do zadané cílové skupiny. Pokud je target_type ' SqlShardMap ', je třeba zadat elastic_pool_name. shard_map_name je nvarchar (128) bez výchozího nastavení.

[ **\@ target_id =** ] výstup target_group_id  
Cílové identifikační číslo přiřazené k cílovému členu skupiny, pokud je vytvořeno do cílové skupiny. target_id je výstupní proměnná typu uniqueidentifier s výchozí hodnotou NULL.
Hodnoty návratového kódu 0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky

Úloha se spustí na všech samostatných databázích v rámci serveru nebo v elastickém fondu v době spuštění, když je server nebo elastický fond zahrnutý do cílové skupiny.

#### <a name="permissions"></a>Oprávnění

Ve výchozím nastavení mohou tuto uloženou proceduru spustit členové pevné role serveru sysadmin. Omezují uživatele jenom na to, že budou moct monitorovat úlohy, a vy můžete v databázi agenta úlohy zadat, aby se při vytváření agenta úloh účastnila Tato databázová role:

- jobs_reader

Podrobnosti o oprávnění těchto rolí najdete v části oprávnění v tomto dokumentu. Tuto uloženou proceduru mohou použít pouze členové sysadmin k úpravě atributů úloh, které vlastní jiní uživatelé.

#### <a name="examples"></a>Příklady

Následující příklad přidá všechny databáze na serverech Londýn a NewYork do skupinových serverů, které udržují informace o zákaznících. V tomto případě je nutné se připojit k databázi Jobs zadané při vytváření agenta úloh v tomto případě ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ;
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information'
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net' ;
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net' ;
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="sp_delete_target_group_member"></a><a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

Odebere člena cílové skupiny z cílové skupiny.

#### <a name="syntax"></a>Syntaxe

```syntaxsql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
   [ , [ @target_id = ] 'target_id']
```

#### <a name="arguments"></a>Argumenty

[ @target_group_name =] ' target_group_name '  
Název cílové skupiny, ze které se má odebrat člen cílové skupiny. target_group_name je nvarchar (128) bez výchozího nastavení.

[ @target_id =] target_id  
 Cílové identifikační číslo přiřazené cílovému členu skupiny, který se má odebrat target_id je typ uniqueidentifier s výchozí hodnotou NULL.

#### <a name="return-code-values"></a>Hodnoty návratového kódu

0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky

Cílové skupiny poskytují snadný způsob, jak zaměřit úlohu na kolekci databází.

#### <a name="permissions"></a>Oprávnění

Ve výchozím nastavení mohou tuto uloženou proceduru spustit členové pevné role serveru sysadmin. Omezují uživatele jenom na to, že budou moct monitorovat úlohy, a vy můžete v databázi agenta úlohy zadat, aby se při vytváření agenta úloh účastnila Tato databázová role:

- jobs_reader

Podrobnosti o oprávnění těchto rolí najdete v části oprávnění v tomto dokumentu. Tuto uloženou proceduru mohou použít pouze členové sysadmin k úpravě atributů úloh, které vlastní jiní uživatelé.

#### <a name="examples"></a>Příklady

Následující příklad odebere Londýn server ze skupin serverů, které udržují informace o zákaznících. V tomto případě je nutné se připojit k databázi Jobs zadané při vytváření agenta úloh v tomto případě ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ;
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net'

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid
GO
```

### <a name="sp_purge_jobhistory"></a><a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

Odebere záznamy historie pro úlohu.

#### <a name="syntax"></a>Syntaxe

```syntaxsql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumenty

[ **\@ job_name =** ] ' job_name '  
Název úlohy, pro kterou chcete odstranit záznamy historie. job_name je nvarchar (128), přičemž výchozí hodnota je NULL. Musí být zadán buď job_id, nebo job_name, ale nelze zadat obě.

[ **\@ job_id =** ] job_id  
 Identifikační číslo úlohy pro záznamy, které mají být odstraněny. job_id je uniqueidentifier a má výchozí hodnotu NULL. Musí být zadán buď job_id, nebo job_name, ale nelze zadat obě.

[ **\@ oldest_date =** ] oldest_date  
 Nejstarší záznam, který se uchová v historii. oldest_date je DATETIME2 s výchozí hodnotou NULL. Když se zadá oldest_date, sp_purge_jobhistory odebere jenom záznamy, které jsou starší než zadaná hodnota.

#### <a name="return-code-values"></a>Hodnoty návratového kódu

0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky

Cílové skupiny poskytují snadný způsob, jak zaměřit úlohu na kolekci databází.

#### <a name="permissions"></a>Oprávnění

Ve výchozím nastavení mohou tuto uloženou proceduru spustit členové pevné role serveru sysadmin. Omezují uživatele jenom na to, že budou moct monitorovat úlohy, a vy můžete v databázi agenta úlohy zadat, aby se při vytváření agenta úloh účastnila Tato databázová role:

- jobs_reader

Podrobnosti o oprávnění těchto rolí najdete v části oprávnění v tomto dokumentu. Tuto uloženou proceduru mohou použít pouze členové sysadmin k úpravě atributů úloh, které vlastní jiní uživatelé.

#### <a name="examples"></a>Příklady

Následující příklad přidá všechny databáze na serverech Londýn a NewYork do skupinových serverů, které udržují informace o zákaznících. V tomto případě je nutné se připojit k databázi Jobs zadané při vytváření agenta úloh v tomto případě ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```

## <a name="job-views"></a>Zobrazení úloh

V [databázi Jobs](job-automation-overview.md#job-database)jsou k dispozici následující zobrazení.

|Zobrazení  |Description  |
|---------|---------|
|[job_executions](#job_executions-view)     |  Zobrazuje historii spuštění úlohy.      |
|[úlohy](#jobs-view)     |   Zobrazí všechny úlohy.      |
|[job_versions](#job_versions-view)     |   Zobrazuje všechny verze úloh.      |
|[jobsteps](#jobsteps-view)     |     Zobrazuje všechny kroky v aktuální verzi každé úlohy.    |
|[jobstep_versions](#jobstep_versions-view)     |     Zobrazí všechny kroky ve všech verzích každé úlohy.    |
|[target_groups](#target_groups-view)     |      Zobrazí všechny cílové skupiny.   |
|[target_group_members](#target_group_members-view)     |   Zobrazí všechny členy všech cílových skupin.      |

### <a name="job_executions-view"></a><a name="job_executions-view"></a>zobrazení job_executions

[úlohy]. [job_executions]

Zobrazuje historii spuštění úlohy.

|Název sloupce | Datový typ | Popis |
|---------|---------|---------|
|**job_execution_id** | uniqueidentifier | Jedinečné ID instance provádění úlohy.
|**job_name** | nvarchar (128) | Název úlohy
|**job_id** | uniqueidentifier | Jedinečné ID úlohy
|**job_version** | int | Verze úlohy (automaticky aktualizované při každé změně úlohy)
|**step_id** |int | Jedinečný identifikátor (pro tento úkol) pro krok Hodnota NULL znamená, že se jedná o spuštění nadřazené úlohy.
|**is_active** | bit | Označuje, zda jsou informace aktivní nebo neaktivní. 1 označuje aktivní úlohy a 0 označuje neaktivní.
|**Nejčastější** | nvarchar (50) | Hodnota označující stav úlohy: ' vytvořeno ', ' probíhá ', ' neúspěšné ', ' úspěch ', ' vynecháno ', ' SucceededWithSkipped '|
|**create_time**| datetime2 (7) | Datum a čas vytvoření úlohy.
|**start_time** | datetime2 (7) | Datum a čas spuštění úlohy. Hodnota NULL, pokud úloha ještě nebyla provedena.
|**end_time** | datetime2 (7) | Datum a čas, kdy byla úloha dokončena. Hodnota NULL, pokud se úloha ještě nespustila nebo ještě neproběhla její provádění.
|**current_attempts** | int | Počet opakovaných pokusů o krok. Nadřazená úloha bude 0, spuštění podřízené úlohy bude na základě zásad spouštění 1 nebo vyšší.
|**current_attempt_start_time** | datetime2 (7) | Datum a čas spuštění úlohy. Hodnota NULL znamená, že se jedná o spuštění nadřazené úlohy.
|**last_message** | nvarchar (max) | Zpráva historie úlohy nebo kroku.
|**target_type** | nvarchar (128) | Typ cílové databáze nebo kolekce databází včetně všech databází na serveru, všech databází v elastickém fondu nebo v databázi. Platné hodnoty pro target_type jsou "SqlServer", "SqlElasticPool" nebo "SqlDatabase". Hodnota NULL znamená, že se jedná o spuštění nadřazené úlohy.
|**target_id** | uniqueidentifier | Jedinečné ID cílového člena skupiny  Hodnota NULL znamená, že se jedná o spuštění nadřazené úlohy.
|**target_group_name** | nvarchar (128) | Název cílové skupiny Hodnota NULL znamená, že se jedná o spuštění nadřazené úlohy.
|**target_server_name** | nvarchar (256)  | Název serveru, který je obsažený v cílové skupině. Zadané jenom v případě, že target_type je SqlServer. Hodnota NULL znamená, že se jedná o spuštění nadřazené úlohy.
|**target_database_name** | nvarchar (128) | Název databáze obsažená v cílové skupině. Zadáno pouze v případě, že target_type je ' SqlDatabase '. Hodnota NULL znamená, že se jedná o spuštění nadřazené úlohy.

### <a name="jobs-view"></a>zobrazení úloh

[úlohy]. úlohy

Zobrazí všechny úlohy.

|Název sloupce | Datový typ |Popis|
|------|------|-------|
|**job_name** | nvarchar (128) | Název úlohy|
|**job_id**| uniqueidentifier |Jedinečné ID úlohy|
|**job_version** |int |Verze úlohy (automaticky aktualizované při každé změně úlohy)|
|**název** |nvarchar (512)| Popis úlohy Enabled bit: udává, zda je úloha povolená nebo zakázaná. 1 označuje povolené úlohy a 0 indikuje zakázané úlohy.|
|**schedule_interval_type**|nvarchar (50) |Hodnota, která indikuje, kdy se má úloha spustit: jednou, minutami, ' hodiny ', ' days ', ' weeks ', ' months '
|**schedule_interval_count**|int|Počet schedule_interval_type období mezi jednotlivými spuštěními úlohy.|
|**schedule_start_time**|datetime2 (7)|Datum a čas posledního spuštění úlohy.|
|**schedule_end_time**|datetime2 (7)|Datum a čas, kdy se úloha naposledy dokončila.|

### <a name="job_versions-view"></a><a name="job_versions-view"></a>zobrazení job_versions

[úlohy]. [job_versions]

Zobrazuje všechny verze úloh.

|Název sloupce|Datový typ|Popis|
|------|------|-------|
|**job_name**|nvarchar (128)|Název úlohy|
|**job_id**|uniqueidentifier|Jedinečné ID úlohy|
|**job_version**|int|Verze úlohy (automaticky aktualizované při každé změně úlohy)|

### <a name="jobsteps-view"></a>zobrazení JobSteps

[úlohy]. [jobsteps]

Zobrazuje všechny kroky v aktuální verzi každé úlohy.

|Název sloupce|Datový typ|Popis|
|------|------|-------|
|**job_name**|nvarchar (128)|Název úlohy|
|**job_id**|uniqueidentifier|Jedinečné ID úlohy|
|**job_version**|int|Verze úlohy (automaticky aktualizované při každé změně úlohy)|
|**step_id**|int|Jedinečný identifikátor (pro tento úkol) pro krok|
|**step_name**|nvarchar (128)|Jedinečný název (pro tuto úlohu) pro krok|
|**command_type**|nvarchar (50)|Typ příkazu, který se má provést v kroku úlohy Pro V1 se hodnota musí rovnat hodnotě a výchozí hodnota je ' TSql '.|
|**command_source**|nvarchar (50)|Umístění příkazu Pro v1 je jako výchozí výchozí a jenom přijatá hodnota.|
|**systému**|nvarchar (max)|Příkazy, které mají být spouštěny elastickými úlohami prostřednictvím command_type.|
|**credential_name**|nvarchar (128)|Název databáze s rozsahem přihlašovacích údajů, které se použily k provedení úlohy|
|**target_group_name**|nvarchar (128)|Název cílové skupiny|
|**target_group_id**|uniqueidentifier|Jedinečné ID cílové skupiny|
|**initial_retry_interval_seconds**|int|Zpoždění před prvním opakovaným pokusem. Výchozí hodnota je 1.|
|**maximum_retry_interval_seconds**|int|Maximální prodleva mezi opakovanými pokusy. Pokud by zpoždění mezi opakovanými pokusy růst větší než tato hodnota, je místo toho omezené na tuto hodnotu. Výchozí hodnota je 120.|
|**retry_interval_backoff_multiplier**|real|Násobitel, který se má použít u zpoždění při opakování, pokud se několik pokusů o spuštění kroku úlohy nezdaří. Výchozí hodnota je 2,0.|
|**retry_attempts**|int|Počet opakovaných pokusů, které se mají použít, pokud tento krok neproběhne úspěšně. Výchozí hodnota je 10, což znamená, že se neopakují žádné pokusy.|
|**step_timeout_seconds**|int|Množství času v minutách mezi pokusy o opakování. Výchozí hodnota je 0, což znamená interval 0 minut.|
|**output_type**|nvarchar (11)|Umístění příkazu V aktuální verzi Preview je možnost inline výchozí a jenom přijatá hodnota.|
|**output_credential_name**|nvarchar (128)|Název přihlašovacích údajů, které se mají použít k připojení k cílovému serveru pro uložení sady výsledků.|
|**output_subscription_id**|uniqueidentifier|Jedinečné ID odběru cílového server\databaseu pro výsledky nastavené z provádění dotazu.|
|**output_resource_group_name**|nvarchar (128)|Název skupiny prostředků, ve které se nachází cílový server.|
|**output_server_name**|nvarchar (256)|Název cílového serveru pro sadu výsledků.|
|**output_database_name**|nvarchar (128)|Název cílové databáze pro sadu výsledků.|
|**output_schema_name**|nvarchar (max)|Název cílového schématu. Výchozí hodnota je dbo, pokud není zadána.|
|**output_table_name**|nvarchar (max)|Název tabulky, do které se uloží sady výsledků dotazu. Tabulka se vytvoří automaticky v závislosti na schématu sady výsledků, pokud ještě neexistuje. Schéma musí odpovídat schématu sady výsledků.|
|**max_parallelism**|int|Maximální počet databází na elastický fond, ve kterém se spustí krok úlohy v čase. Výchozí hodnota je NULL, což znamená bez omezení. |

### <a name="jobstep_versions-view"></a><a name="jobstep_versions-view"></a>zobrazení jobstep_versions

[úlohy]. [jobstep_versions]

Zobrazí všechny kroky ve všech verzích každé úlohy. Schéma je stejné jako [JobSteps](#jobsteps-view).

### <a name="target_groups-view"></a><a name="target_groups-view"></a>zobrazení target_groups

[úlohy]. [target_groups]

Zobrazí seznam všech cílových skupin.

|Název sloupce|Datový typ|Popis|
|-----|-----|-----|
|**target_group_name**|nvarchar (128)|Název cílové skupiny, kolekce databází.
|**target_group_id**|uniqueidentifier|Jedinečné ID cílové skupiny

### <a name="target_group_members-view"></a><a name="target_group_members-view"></a>zobrazení target_group_members

[úlohy]. [target_group_members]

Zobrazí všechny členy všech cílových skupin.

|Název sloupce|Datový typ|Popis|
|-----|-----|-----|
|**target_group_name**|nvarchar (128|Název cílové skupiny, kolekce databází. |
|**target_group_id**|uniqueidentifier|Jedinečné ID cílové skupiny|
|**membership_type**|int|Určuje, jestli je cílový člen skupiny zahrnutý nebo vyloučený v cílové skupině. Platné hodnoty pro target_group_name jsou include nebo Exclude.|
|**target_type**|nvarchar (128)|Typ cílové databáze nebo kolekce databází včetně všech databází na serveru, všech databází v elastickém fondu nebo v databázi. Platné hodnoty pro target_type jsou "SqlServer", "SqlElasticPool", "SqlDatabase" nebo "SqlShardMap".|
|**target_id**|uniqueidentifier|Jedinečné ID cílového člena skupiny|
|**refresh_credential_name**|nvarchar (128)|Název databáze s rozsahem pověření používané pro připojení k cílovému členu skupiny.|
|**subscription_id**|uniqueidentifier|Jedinečné ID předplatného|
|**resource_group_name**|nvarchar (128)|Název skupiny prostředků, ve které se nachází cílový člen skupiny.|
|**server_name**|nvarchar (128)|Název serveru, který je obsažený v cílové skupině. Zadané jenom v případě, že target_type je SqlServer. |
|**database_name**|nvarchar (128)|Název databáze obsažená v cílové skupině. Zadáno pouze v případě, že target_type je ' SqlDatabase '.|
|**elastic_pool_name**|nvarchar (128)|Název elastického fondu obsažený v cílové skupině. Zadáno pouze v případě, že target_type je ' SqlElasticPool '.|
|**shard_map_name**|nvarchar (128)|Název map horizontálních oddílů obsažených v cílové skupině. Zadáno pouze v případě, že target_type je ' SqlShardMap '.|

## <a name="resources"></a>Zdroje a prostředky

- ![Ikona odkazu na téma](/sql/database-engine/configure-windows/media/topic-link.gif "Ikona odkazu na téma") [– konvence syntaxe Transact-SQL](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  

## <a name="next-steps"></a>Další kroky

- [Vytváření a správa elastických úloh s využitím PowerShellu](elastic-jobs-powershell-create.md)
- [Autorizace a oprávnění](/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
