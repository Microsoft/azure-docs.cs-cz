---
title: Vytváření a správa úloh elastické databáze pomocí transact-SQL (T-SQL)
description: Spouštějte skripty v mnoha databázích s agentem elastická databázová úloha pomocí Transact-SQL (T-SQL).
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 02/07/2020
ms.openlocfilehash: 740a42dc94cdfa8d5c5a91b32b58cbff4c1bcda0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687773"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Použití transact-SQL (T-SQL) k vytvoření a správě úloh elastické databáze

Tento článek obsahuje mnoho příkladů scénářů, jak začít pracovat s elastickými úlohami pomocí T-SQL.

Příklady používají [uložené procedury](#job-stored-procedures) a [zobrazení,](#job-views) které jsou k dispozici v [*databázi úloh*](sql-database-job-automation-overview.md#job-database).

Transact-SQL (T-SQL) se používá k vytváření, konfiguraci, spouštění a správě úloh. Vytvoření agenta elastické úlohy není v T-SQL podporováno, takže musíte nejprve vytvořit *agenta elastické úlohy* pomocí portálu nebo [prostředí PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Vytvoření pověření pro spuštění úlohy

Pověření se používá k připojení k cílovým databázím pro spuštění skriptu. Pověření potřebuje příslušná oprávnění v databázích určených cílovou skupinou, aby bylo možné skript úspěšně spustit. Při použití člena cílové skupiny serveru nebo fondu se důrazně doporučuje vytvořit hlavní pověření pro použití k aktualizaci pověření před rozšířením serveru nebo fondu v době spuštění úlohy. Pověření s rozsahem databáze je vytvořeno v databázi agenta úlohy. Stejné přihlašovací údaje musí být použity k *vytvoření přihlášení* a vytvořit uživatele z *přihlášení udělit oprávnění databáze přihlášení* na cílové databáze.


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

## <a name="create-a-target-group-servers"></a>Vytvoření cílové skupiny (serverů)

Následující příklad ukazuje, jak provést úlohu proti všem databázím na serveru.  
Připojte se k [*databázi úloh*](sql-database-job-automation-overview.md#job-database) a spusťte následující příkaz:


```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```


## <a name="exclude-an-individual-database"></a>Vyloučení jednotlivé databáze

Následující příklad ukazuje, jak provést úlohu proti všem databázím na serveru SQL Database, s výjimkou databáze s názvem *MappingDB*.  
Připojte se k [*databázi úloh*](sql-database-job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name='server2.database.windows.net'
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name =N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```


## <a name="create-a-target-group-pools"></a>Vytvoření cílové skupiny (fondy)

Následující příklad ukazuje, jak cílit na všechny databáze v jednom nebo více elastických fondů.  
Připojte se k [*databázi úloh*](sql-database-job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```


## <a name="deploy-new-schema-to-many-databases"></a>Nasazení nového schématu do mnoha databází

Následující příklad ukazuje, jak nasadit nové schéma do všech databází.  
Připojte se k [*databázi úloh*](sql-database-job-automation-overview.md#job-database) a spusťte následující příkaz:


```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables 
            WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```


## <a name="data-collection-using-built-in-parameters"></a>Shromažďování dat pomocí vestavěných parametrů

V mnoha scénářích shromažďování dat může být užitečné zahrnout některé z těchto skriptovacích proměnných, které pomohou po zpracování výsledků úlohy.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Chcete-li například seskupit všechny výsledky ze stejného spuštění úlohy společně, použijte *$(job_execution_id),* jak je znázorněno v následujícím příkazu:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Monitorování výkonu databáze

Následující příklad vytvoří novou úlohu pro shromažďování dat o výkonu z více databází.

Ve výchozím nastavení vytvoří agent úlohy výstupní tabulku pro uložení vrácených výsledků. Proto musí mít objekt zabezpečení databáze přidružený k výstupnímu `CREATE TABLE` pověření minimálně `ALTER` `SELECT`následující `INSERT` `DELETE` oprávnění: v databázi , , `SELECT` ve výstupní tabulce nebo jejím schématu a v zobrazení katalogu [sys.indexes.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql)

Pokud chcete tabulku vytvořit předem, musí mít následující vlastnosti:
1. Sloupce se správným názvem a datovými typy pro sadu výsledků.
2. Další sloupec pro internal_execution_id s datovým typem uniqueidentifier.
3. Index bez clusteru `IX_<TableName>_Internal_Execution_ID` pojmenovaný ve sloupci internal_execution_id.
4. Všechna oprávnění uvedená `CREATE TABLE` výše s výjimkou oprávnění k databázi.

Připojte se k [*databázi úloh*](sql-database-job-automation-overview.md#job-database) a spusťte následující příkazy:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsJob',
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name='myjobcred',
@target_group_name='PoolGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='<resultsdb>',
@output_table_name='<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name='MasterGroup',
@target_type='SqlDatabase',
@server_name='server1.database.windows.net',
@database_name='master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name='ResultsPoolsJob',
@description='Demo: Collection Performance data from all pools',
@schedule_interval_type='Minutes',
@schedule_interval_count=15

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
@credential_name='myjobcred',
@target_group_name='MasterGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>Zobrazit definice zakázek

Následující příklad ukazuje, jak zobrazit aktuální definice zakázek.  
Připojte se k [*databázi úloh*](sql-database-job-automation-overview.md#job-database) a spusťte následující příkaz:

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


## <a name="begin-ad-hoc-execution-of-a-job"></a>Zahájení ad hoc provádění úlohy

Následující příklad ukazuje, jak okamžitě spustit úlohu.  
Připojte se k [*databázi úloh*](sql-database-job-automation-overview.md#job-database) a spusťte následující příkaz:

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


## <a name="schedule-execution-of-a-job"></a>Naplánovat spuštění úlohy

Následující příklad ukazuje, jak naplánovat úlohu pro budoucí spuštění.  
Připojte se k [*databázi úloh*](sql-database-job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Sledovat stav provádění úlohy

Následující příklad ukazuje, jak zobrazit podrobnosti o stavu spuštění pro všechny úlohy.  
Připojte se k [*databázi úloh*](sql-database-job-automation-overview.md#job-database) a spusťte následující příkaz:

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
Připojte se k [*databázi úloh*](sql-database-job-automation-overview.md#job-database) a spusťte následující příkaz:

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


## <a name="delete-old-job-history"></a>Odstranit starou historii úloh

Následující příklad ukazuje, jak odstranit historii úloh před určitým datem.  
Připojte se k [*databázi úloh*](sql-database-job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Odstranění úlohy a veškeré její historie úloh

Následující příklad ukazuje, jak odstranit úlohu a všechny související historie úloh.  
Připojte se k [*databázi úloh*](sql-database-job-automation-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Uložené postupy úloh

Následující uložené procedury jsou v [databázi úloh](sql-database-job-automation-overview.md#job-database).



|Uložená procedura  |Popis  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Přidá novou úlohu.    |
|[sp_update_job](#sp_update_job)    |      Aktualizuje existující úlohu.   |
|[sp_delete_job](#sp_delete_job)     |      Odstraní existující úlohu.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Přidá krok k úloze.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Aktualizuje krok úlohy.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Odstraní krok úlohy.    |
|[Sp_start_job](#sp_start_job)    |  Spustí provádění úlohy.       |
|[sp_stop_job](#sp_stop_job)     |     Zastaví provádění úloh.   |
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

[ ** \@job_name =** ] "job_name"  
Název úlohy. Název musí být jedinečný a nesmí obsahovat procento (%) Znak. job_name je nvarchar(128), bez výchozího nastavení.

[ ** \@popis =** ] "popisem"  
Popis úlohy. popis je nvarchar(512), s výchozí NULL. Pokud je popis vynechán, použije se prázdný řetězec.

[ ** \@povoleno =** ] povoleno  
Určuje, zda je povolen plán úlohy. Povoleno je bit, s výchozí 0 (zakázáno). Pokud 0, úloha není povolena a nespustí podle jeho plánu; lze jej však spustit ručně. Pokud 1, úloha bude spuštěna podle svého plánu a lze jej spustit také ručně.

[ ** \@schedule_interval_type =**] schedule_interval_type  
Hodnota označuje, kdy má být úloha provedena. schedule_interval_type je nvarchar(50), s výchozí Once a může být jedna z následujících hodnot:
- "Jednou",
- "Zápisy",
- "Hodiny",
- "Dny",
- "Týdny",
- "Měsíce"

[ ** \@schedule_interval_count =** ] schedule_interval_count  
Počet schedule_interval_count období mezi jednotlivými spuštěními úlohy. schedule_interval_count int, s výchozí hodnotou 1. Hodnota musí být větší nebo rovna 1.

[ ** \@schedule_start_time =** ] schedule_start_time  
Datum, kdy může spuštění úlohy začít. schedule_start_time je DATETIME2, s výchozí 0001-01-01 00:00:00.0000000.

[ ** \@schedule_end_time =** ] schedule_end_time  
Datum, kdy může být zastavení provádění úlohy. schedule_end_time je DATETIME2, s výchozí 9999-12-31 11:59:59.0000000. 

[ ** \@job_id =** ] job_id VÝSTUP  
Identifikační číslo úlohy přiřazené k úloze, pokud bylo úspěšně vytvořeno. job_id je výstupní proměnná typu uniqueidentifier.

#### <a name="return-code-values"></a>Vrácené hodnoty kódu

0 (úspěch) nebo 1 (selhání)

#### <a name="remarks"></a>Poznámky
sp_add_job musí být spuštěna z databáze agenta úlohy zadané při vytváření agenta úlohy.
Po spuštění sp_add_job pro přidání úlohy lze sp_add_jobstep přidat kroky, které provádějí aktivity pro úlohu. Číslo počáteční verze úlohy je 0, které bude při přidání prvního kroku zvýšeno na 1.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení mohou členové pevné role serveru sysadmin provést tuto uloženou proceduru. Omezují uživatele pouze sledovat úlohy, můžete udělit uživateli být součástí následující role databáze v databázi agenta práce zadané při vytváření agenta úlohy:

- jobs_reader

Podrobnosti o oprávněních těchto rolí naleznete v části Oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úloh, které jsou vlastněny jinými uživateli.

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
[ ** \@job_name =** ] "job_name"  
Název úlohy, která má být aktualizována. job_name je nvarchar (128).

[ ** \@new_name =** ] "new_name"  
Nový název úlohy. new_name je nvarchar (128).

[ ** \@popis =** ] "popisem"  
Popis úlohy. popis je nvarchar(512).

[ ** \@povoleno =** ] povoleno  
Určuje, zda je povolen plán úlohy (1) nebo není povolen (0). Povoleno je bit.

** \@[schedule_interval_type=** ] schedule_interval_type  
Hodnota označuje, kdy má být úloha provedena. schedule_interval_type je nvarchar(50) a může být jednou z následujících hodnot:

- "Jednou",
- "Zápisy",
- "Hodiny",
- "Dny",
- "Týdny",
- "Měsíce"

[ ** \@schedule_interval_count =** ] schedule_interval_count  
Počet schedule_interval_count období mezi jednotlivými spuštěními úlohy. schedule_interval_count int, s výchozí hodnotou 1. Hodnota musí být větší nebo rovna 1.

[ ** \@schedule_start_time =** ] schedule_start_time  
Datum, kdy může spuštění úlohy začít. schedule_start_time je DATETIME2, s výchozí 0001-01-01 00:00:00.0000000.

** \@[schedule_end_time=** ] schedule_end_time  
Datum, kdy může být zastavení provádění úlohy. schedule_end_time je DATETIME2, s výchozí 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Vrácené hodnoty kódu
0 (úspěch) nebo 1 (selhání)

#### <a name="remarks"></a>Poznámky
Po spuštění sp_add_job pro přidání úlohy lze sp_add_jobstep přidat kroky, které provádějí aktivity pro úlohu. Číslo počáteční verze úlohy je 0, které bude při přidání prvního kroku zvýšeno na 1.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení mohou členové pevné role serveru sysadmin provést tuto uloženou proceduru. Omezují uživatele pouze sledovat úlohy, můžete udělit uživateli být součástí následující role databáze v databázi agenta práce zadané při vytváření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních těchto rolí naleznete v části Oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úloh, které jsou vlastněny jinými uživateli.



### <a name="sp_delete_job"></a><a name="sp_delete_job"></a>sp_delete_job

Odstraní existující úlohu.

#### <a name="syntax"></a>Syntaxe

```syntaxsql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumenty
[ ** \@job_name =** ] "job_name"  
Název úlohy, která má být odstraněna. job_name je nvarchar (128).

[ ** \@síla =** ] síla  
Určuje, zda má být odstraněna, pokud úloha má nějaké spuštění probíhá a zrušit všechny probíhající spuštění (1) nebo selhání, pokud probíhají spuštění úlohy (0). síla je bit.

#### <a name="return-code-values"></a>Vrácené hodnoty kódu
0 (úspěch) nebo 1 (selhání)

#### <a name="remarks"></a>Poznámky
Historie úloh je automaticky odstraněna při odstranění úlohy.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení mohou členové pevné role serveru sysadmin provést tuto uloženou proceduru. Omezují uživatele pouze sledovat úlohy, můžete udělit uživateli být součástí následující role databáze v databázi agenta práce zadané při vytváření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních těchto rolí naleznete v části Oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úloh, které jsou vlastněny jinými uživateli.



### <a name="sp_add_jobstep"></a><a name="sp_add_jobstep"></a>sp_add_jobstep

Přidá krok k úloze.

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

[ ** \@job_name =** ] "job_name"  
Název úlohy, do které chcete přidat krok. job_name je nvarchar (128).

[ ** \@step_id =** ] step_id  
Identifikační číslo sekvence pro krok úlohy. Čísla krokových čísel začínají na 1 a posírají se bez mezer. Pokud existující krok již má toto id, pak tento krok a všechny následující kroky budou mít jejich id je přírůstek tak, aby tento nový krok lze vložit do sekvence. Pokud není zadán, step_id bude automaticky přiřazen a poslední v pořadí kroků. step_id je int.

[ ** \@step_name =** ] step_name  
Název kroku. Musí být zadán, s výjimkou prvního kroku úlohy, která (pro pohodlí) má výchozí název "JobStep". step_name je nvarchar (128).

[ ** \@command_type =** ] "command_type"  
Typ příkazu, který je proveden tímto jobstep. command_type je nvarchar(50), s výchozí hodnotou TSql, @command_type což znamená, že hodnota parametru je T-SQL skript.

Pokud je zadán, hodnota musí být TSql.

[ ** \@command_source =** ] "command_source"  
Typ umístění, kde je uložen příkaz. command_source je nvarchar(50), s výchozí hodnotou Inline, @command_source což znamená, že hodnota parametru je literál text příkazu.

Pokud je zadán, hodnota musí být Inline.

[ ** \@příkaz =** ] 'příkaz'  
Příkaz musí být platný skript T-SQL a poté je proveden tímto krokem úlohy. příkaz je nvarchar(max), s výchozí hodnotou NULL.

[ ** \@credential_name =** ] "credential_name"  
Název pověření s rozsahem databáze uložené v této databázi řízení úloh, který se používá pro připojení ke každé cílové databáze v rámci cílové skupiny při provádění tohoto kroku. credential_name je nvarchar (128).

[ ** \@target_group_name =** ] "group_name cílů"  
Název cílové skupiny, která obsahuje cílové databáze, na které bude krok úlohy proveden. target_group_name je nvarchar (128).

[ ** \@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Zpoždění před prvním pokusem o opakování, pokud se při pokusu o počáteční spuštění nezdaří krok úlohy. initial_retry_interval_seconds int, s výchozí hodnotou 1.

[ ** \@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maximální prodleva mezi pokusy o opakování. Pokud by se prodleva mezi opakovanými pokusy zvětšila, než je tato hodnota, je místo toho omezena na tuto hodnotu. maximum_retry_interval_seconds int, s výchozí hodnotou 120.

[ ** \@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Multiplikátor použít zpoždění opakování, pokud se nezdaří více pokusů o spuštění kroku úlohy. Například pokud první opakování mělo zpoždění 5 sekund a multiplikátor backoff je 2,0, pak druhý pokus bude mít zpoždění 10 sekund a třetí opakování bude mít zpoždění 20 sekund. retry_interval_backoff_multiplier je reálné, s výchozí hodnotou 2.0.

[ ** \@retry_attempts =** ] retry_attempts  
Počet opakování spuštění, pokud se počáteční pokus nezdaří. Pokud je například hodnota retry_attempts 10, bude k dispozici 1 počáteční pokus a 10 pokusů o opakování, což znamená celkem 11 pokusů. Pokud se poslední pokus o opakování nezdaří, bude provádění úlohy ukončeno životním cyklem failed. retry_attempts int, s výchozí hodnotou 10.

[ ** \@step_timeout_seconds =** ] step_timeout_seconds  
Maximální doba povolená pro krok provést. Pokud je tento čas překročen, spuštění úlohy bude ukončeno s životním cyklem TimedOut. step_timeout_seconds je int, s výchozí hodnotou 43 200 sekund (12 hodin).

[ ** \@output_type =** ] "output_type"  
Pokud není null, typ cíle, který příkaz první sadu výsledků je zapsána do. output_type je nvarchar(50), s výchozí NULL.

Pokud je zadán, hodnota musí být SqlDatabase.

[ ** \@output_credential_name =** ] "output_credential_name"  
Pokud není null, název pověření s rozsahem databáze, který se používá pro připojení k výstupní cílové databázi. Musí být zadán, pokud output_type rovná sqldatabase. output_credential_name je nvarchar(128), s výchozí hodnotou NULL.

[ ** \@output_subscription_id =** ] "output_subscription_id"  
Potřebuje popis.

[ ** \@output_resource_group_name =** ] "output_resource_group_name"  
Potřebuje popis.

[ ** \@output_server_name =** ] "output_server_name"  
Pokud není null, plně kvalifikovaný název DNS serveru, který obsahuje výstupní cílovou databázi. Musí být zadán, pokud output_type rovná sqldatabase. output_server_name je nvarchar(256), s výchozí NULL.

[ ** \@output_database_name =** ] "output_database_name"  
Pokud není null, název databáze, která obsahuje výstupní cílovou tabulku. Musí být zadán, pokud output_type rovná sqldatabase. output_database_name je nvarchar(128), s výchozí NULL.

[ ** \@output_schema_name =** ] "output_schema_name"  
Pokud není null, název schématu SQL, který obsahuje výstupní cílovou tabulku. Pokud output_type rovná SqlDatabase, výchozí hodnota je dbo. output_schema_name je nvarchar (128).

[ ** \@output_table_name =** ] "output_table_name"  
Pokud není null, název tabulky, do které bude zapsána první sada výsledků příkazu. Pokud tabulka ještě neexistuje, bude vytvořena na základě schématu vracející se sady výsledků. Musí být zadán, pokud output_type rovná sqldatabase. output_table_name je nvarchar(128), s výchozí hodnotou NULL.

[ ** \@job_version =** ] job_version výstup  
Výstupní parametr, kterému bude přiřazeno číslo nové verze úlohy. job_version je int.

[ ** \@max_parallelism =** ] max_parallelism výstup  
Maximální úroveň paralelismu na elastický fond. Pokud je nastavena, pak krok úlohy bude omezena pouze spustit na maximálně tolik databází na elastický fond. To platí pro každý elastický fond, který je buď přímo součástí cílové skupiny nebo je uvnitř serveru, který je součástí cílové skupiny. max_parallelism je int.


#### <a name="return-code-values"></a>Vrácené hodnoty kódu
0 (úspěch) nebo 1 (selhání)

#### <a name="remarks"></a>Poznámky
Když sp_add_jobstep úspěšné, číslo aktuální verze úlohy se zintáčí. Při příštím spuštění úlohy bude použita nová verze. Pokud úloha je aktuálně spuštěna, toto spuštění nebude obsahovat nový krok.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení mohou členové pevné role serveru sysadmin provést tuto uloženou proceduru. Omezují uživatele pouze sledovat úlohy, můžete udělit uživateli být součástí následující role databáze v databázi agenta práce zadané při vytváření agenta úlohy:  

- jobs_reader

Podrobnosti o oprávněních těchto rolí naleznete v části Oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úloh, které jsou vlastněny jinými uživateli.



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
[ ** \@job_name =** ] "job_name"  
Název úlohy, do které krok patří. job_name je nvarchar (128).

[ ** \@step_id =** ] step_id  
Identifikační číslo kroku úlohy, který má být změněn. Musí být zadána step_id nebo step_name. step_id je int.

[ ** \@step_name =** ] "step_name"  
Název kroku, který má být změněn. Musí být zadána step_id nebo step_name. step_name je nvarchar (128).

[ ** \@new_id =** ] new_id  
Nové identifikační číslo sekvence pro krok úlohy. Čísla krokových čísel začínají na 1 a posírají se bez mezer. Pokud je krok přeřazen, budou další kroky automaticky přečíslovány.

[ ** \@new_name =** ] "new_name"  
Nový název kroku. new_name je nvarchar (128).

[ ** \@command_type =** ] "command_type"  
Typ příkazu, který je proveden tímto jobstep. command_type je nvarchar(50), s výchozí hodnotou TSql, @command_type což znamená, že hodnota parametru je T-SQL skript.

Pokud je zadán, hodnota musí být TSql.

[ ** \@command_source =** ] "command_source"  
Typ umístění, kde je uložen příkaz. command_source je nvarchar(50), s výchozí hodnotou Inline, @command_source což znamená, že hodnota parametru je literál text příkazu.

Pokud je zadán, hodnota musí být Inline.

[ ** \@příkaz =** ] 'příkaz'  
Příkazy musí být platné T-SQL skript a pak je proveden v tomto kroku úlohy. příkaz je nvarchar(max), s výchozí hodnotou NULL.

[ ** \@credential_name =** ] "credential_name"  
Název pověření s rozsahem databáze uložené v této databázi řízení úloh, který se používá pro připojení ke každé cílové databáze v rámci cílové skupiny při provádění tohoto kroku. credential_name je nvarchar (128).

[ ** \@target_group_name =** ] "group_name cílů"  
Název cílové skupiny, která obsahuje cílové databáze, na které bude krok úlohy proveden. target_group_name je nvarchar (128).

[ ** \@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Zpoždění před prvním pokusem o opakování, pokud se při pokusu o počáteční spuštění nezdaří krok úlohy. initial_retry_interval_seconds int, s výchozí hodnotou 1.

[ ** \@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maximální prodleva mezi pokusy o opakování. Pokud by se prodleva mezi opakovanými pokusy zvětšila, než je tato hodnota, je místo toho omezena na tuto hodnotu. maximum_retry_interval_seconds int, s výchozí hodnotou 120.

[ ** \@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Multiplikátor použít zpoždění opakování, pokud se nezdaří více pokusů o spuštění kroku úlohy. Například pokud první opakování mělo zpoždění 5 sekund a multiplikátor backoff je 2,0, pak druhý pokus bude mít zpoždění 10 sekund a třetí opakování bude mít zpoždění 20 sekund. retry_interval_backoff_multiplier je reálné, s výchozí hodnotou 2.0.

[ ** \@retry_attempts =** ] retry_attempts  
Počet opakování spuštění, pokud se počáteční pokus nezdaří. Pokud je například hodnota retry_attempts 10, bude k dispozici 1 počáteční pokus a 10 pokusů o opakování, což znamená celkem 11 pokusů. Pokud se poslední pokus o opakování nezdaří, bude provádění úlohy ukončeno životním cyklem failed. retry_attempts int, s výchozí hodnotou 10.

[ ** \@step_timeout_seconds =** ] step_timeout_seconds  
Maximální doba povolená pro krok provést. Pokud je tento čas překročen, spuštění úlohy bude ukončeno s životním cyklem TimedOut. step_timeout_seconds je int, s výchozí hodnotou 43 200 sekund (12 hodin).

[ ** \@output_type =** ] "output_type"  
Pokud není null, typ cíle, který příkaz první sadu výsledků je zapsána do. Chcete-li obnovit hodnotu output_type zpět na hodnotu NULL, nastavte hodnotu tohoto parametru na hodnotu '' (prázdný řetězec). output_type je nvarchar(50), s výchozí NULL.

Pokud je zadán, hodnota musí být SqlDatabase.

[ ** \@output_credential_name =** ] "output_credential_name"  
Pokud není null, název pověření s rozsahem databáze, který se používá pro připojení k výstupní cílové databázi. Musí být zadán, pokud output_type rovná sqldatabase. Chcete-li obnovit hodnotu output_credential_name zpět na hodnotu NULL, nastavte hodnotu tohoto parametru na hodnotu '' (prázdný řetězec). output_credential_name je nvarchar(128), s výchozí hodnotou NULL.

[ ** \@output_server_name =** ] "output_server_name"  
Pokud není null, plně kvalifikovaný název DNS serveru, který obsahuje výstupní cílovou databázi. Musí být zadán, pokud output_type rovná sqldatabase. Chcete-li obnovit hodnotu output_server_name zpět na hodnotu NULL, nastavte hodnotu tohoto parametru na hodnotu '' (prázdný řetězec). output_server_name je nvarchar(256), s výchozí NULL.

[ ** \@output_database_name =** ] "output_database_name"  
Pokud není null, název databáze, která obsahuje výstupní cílovou tabulku. Musí být zadán, pokud output_type rovná sqldatabase. Chcete-li obnovit hodnotu output_database_name zpět na hodnotu NULL, nastavte hodnotu tohoto parametru na hodnotu '' (prázdný řetězec). output_database_name je nvarchar(128), s výchozí NULL.

[ ** \@output_schema_name =** ] "output_schema_name"  
Pokud není null, název schématu SQL, který obsahuje výstupní cílovou tabulku. Pokud output_type rovná SqlDatabase, výchozí hodnota je dbo. Chcete-li obnovit hodnotu output_schema_name zpět na hodnotu NULL, nastavte hodnotu tohoto parametru na hodnotu '' (prázdný řetězec). output_schema_name je nvarchar (128).

[ ** \@output_table_name =** ] "output_table_name"  
Pokud není null, název tabulky, do které bude zapsána první sada výsledků příkazu. Pokud tabulka ještě neexistuje, bude vytvořena na základě schématu vracející se sady výsledků. Musí být zadán, pokud output_type rovná sqldatabase. Chcete-li obnovit hodnotu output_server_name zpět na hodnotu NULL, nastavte hodnotu tohoto parametru na hodnotu '' (prázdný řetězec). output_table_name je nvarchar(128), s výchozí hodnotou NULL.

[ ** \@job_version =** ] job_version výstup  
Výstupní parametr, kterému bude přiřazeno číslo nové verze úlohy. job_version je int.

[ ** \@max_parallelism =** ] max_parallelism výstup  
Maximální úroveň paralelismu na elastický fond. Pokud je nastavena, pak krok úlohy bude omezena pouze spustit na maximálně tolik databází na elastický fond. To platí pro každý elastický fond, který je buď přímo součástí cílové skupiny nebo je uvnitř serveru, který je součástí cílové skupiny. Chcete-li obnovit hodnotu max_parallelism zpět na hodnotu null, nastavte hodnotu tohoto parametru na hodnotu -1. max_parallelism je int.


#### <a name="return-code-values"></a>Vrácené hodnoty kódu
0 (úspěch) nebo 1 (selhání)

#### <a name="remarks"></a>Poznámky
Žádné probíhající spuštění úlohy nebudou ovlivněny. Když sp_update_jobstep úspěšné, číslo verze úlohy se zintáčí. Při příštím spuštění úlohy bude použita nová verze.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení mohou členové pevné role serveru sysadmin provést tuto uloženou proceduru. Omezují uživatele pouze sledovat úlohy, můžete udělit uživateli být součástí následující role databáze v databázi agenta práce zadané při vytváření agenta úlohy:

- jobs_reader

Podrobnosti o oprávněních těchto rolí naleznete v části Oprávnění v tomto dokumentu. Tuto uloženou proceduru mohou používat pouze členové sysadminu k úpravě atributů úloh, které jsou vlastněny jinými uživateli.




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
[ ** \@job_name =** ] "job_name"  
Název úlohy, ze které bude krok odebrán. job_name je nvarchar(128), bez výchozího nastavení.

[ ** \@step_id =** ] step_id  
Identifikační číslo kroku úlohy, který má být odstraněn. Musí být zadána step_id nebo step_name. step_id je int.

[ ** \@step_name =** ] "step_name"  
Název kroku, který má být odstraněn. Musí být zadána step_id nebo step_name. step_name je nvarchar (128).

[ ** \@job_version =** ] job_version výstup  
Výstupní parametr, kterému bude přiřazeno číslo nové verze úlohy. job_version je int.

#### <a name="return-code-values"></a>Vrácené hodnoty kódu
0 (úspěch) nebo 1 (selhání)

#### <a name="remarks"></a>Poznámky
Žádné probíhající spuštění úlohy nebudou ovlivněny. Když sp_update_jobstep úspěšné, číslo verze úlohy se zintáčí. Při příštím spuštění úlohy bude použita nová verze.

Ostatní kroky úlohy budou automaticky přečíslovány tak, aby vyplnily mezeru zanechanou po kroku odstraněné úlohy.
 
#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení mohou členové pevné role serveru sysadmin provést tuto uloženou proceduru. Omezují uživatele pouze sledovat úlohy, můžete udělit uživateli být součástí následující role databáze v databázi agenta práce zadané při vytváření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních těchto rolí naleznete v části Oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úloh, které jsou vlastněny jinými uživateli.






### <a name="sp_start_job"></a><a name="sp_start_job"></a>Sp_start_job

Spustí provádění úlohy.

#### <a name="syntax"></a>Syntaxe


```syntaxsql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argumenty
[ ** \@job_name =** ] "job_name"  
Název úlohy, ze které bude krok odebrán. job_name je nvarchar(128), bez výchozího nastavení.

[ ** \@job_execution_id =** ] job_execution_id výstup  
Výstupní parametr, kterému bude přiřazeno id spuštění úlohy. job_version je jedinečný identifikátor.

#### <a name="return-code-values"></a>Vrácené hodnoty kódu
0 (úspěch) nebo 1 (selhání)

#### <a name="remarks"></a>Poznámky
Žádné.
 
#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení mohou členové pevné role serveru sysadmin provést tuto uloženou proceduru. Omezují uživatele pouze sledovat úlohy, můžete udělit uživateli být součástí následující role databáze v databázi agenta práce zadané při vytváření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních těchto rolí naleznete v části Oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úloh, které jsou vlastněny jinými uživateli.

### <a name="sp_stop_job"></a><a name="sp_stop_job"></a>sp_stop_job

Zastaví provádění úloh.

#### <a name="syntax"></a>Syntaxe


```syntaxsql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argumenty
[ ** \@job_execution_id =** ] job_execution_id  
Identifikační číslo spuštění úlohy zastavit. job_execution_id je uniqueidentifier, s výchozí NULL.

#### <a name="return-code-values"></a>Vrácené hodnoty kódu
0 (úspěch) nebo 1 (selhání)

#### <a name="remarks"></a>Poznámky
Žádné.
 
#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení mohou členové pevné role serveru sysadmin provést tuto uloženou proceduru. Omezují uživatele pouze sledovat úlohy, můžete udělit uživateli být součástí následující role databáze v databázi agenta práce zadané při vytváření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních těchto rolí naleznete v části Oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úloh, které jsou vlastněny jinými uživateli.


### <a name="sp_add_target_group"></a><a name="sp_add_target_group"></a>sp_add_target_group

Přidá cílovou skupinu.

#### <a name="syntax"></a>Syntaxe


```syntaxsql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argumenty
[ ** \@target_group_name =** ] "target_group_name"  
Název cílové skupiny, kterou chcete vytvořit. target_group_name je nvarchar(128), bez výchozí.

[ ** \@target_group_id =** ] target_group_id VÝSTUP Identifikační číslo cílové skupiny přiřazené k úloze, pokud bylo úspěšně vytvořeno. target_group_id je výstupní proměnná typu uniqueidentifier s výchozím hodnotou NULL.

#### <a name="return-code-values"></a>Vrácené hodnoty kódu
0 (úspěch) nebo 1 (selhání)

#### <a name="remarks"></a>Poznámky
Cílové skupiny poskytují snadný způsob, jak cílit úlohu v kolekci databází.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení mohou členové pevné role serveru sysadmin provést tuto uloženou proceduru. Omezují uživatele pouze sledovat úlohy, můžete udělit uživateli být součástí následující role databáze v databázi agenta práce zadané při vytváření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních těchto rolí naleznete v části Oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úloh, které jsou vlastněny jinými uživateli.

### <a name="sp_delete_target_group"></a><a name="sp_delete_target_group"></a>sp_delete_target_group

Odstraní cílovou skupinu.

#### <a name="syntax"></a>Syntaxe


```syntaxsql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argumenty
[ ** \@target_group_name =** ] "target_group_name"  
Název cílové skupiny, kterou chcete odstranit. target_group_name je nvarchar(128), bez výchozí.

#### <a name="return-code-values"></a>Vrácené hodnoty kódu
0 (úspěch) nebo 1 (selhání)

#### <a name="remarks"></a>Poznámky
Žádné.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení mohou členové pevné role serveru sysadmin provést tuto uloženou proceduru. Omezují uživatele pouze sledovat úlohy, můžete udělit uživateli být součástí následující role databáze v databázi agenta práce zadané při vytváření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních těchto rolí naleznete v části Oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úloh, které jsou vlastněny jinými uživateli.

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
[ ** \@target_group_name =** ] "target_group_name"  
Název cílové skupiny, do které bude člen přidán. target_group_name je nvarchar(128), bez výchozí.

[ ** \@membership_type =** ] "membership_type"  
Určuje, zda bude člen cílové skupiny zahrnut nebo vyloučen. target_group_name je nvarchar(128), s výchozí 'Zahrnout'. Platné hodnoty pro target_group_name jsou "Zahrnout" nebo "Vyloučit".

[ ** \@target_type =** ] "target_type"  
Typ cílové databáze nebo kolekce databází včetně všech databází na serveru, všechny databáze v elastickém fondu, všechny databáze v mapě střepů nebo jednotlivé databáze. target_type je nvarchar(128), bez výchozí. Platné hodnoty pro target_type jsou "SqlServer", "SqlElasticPool", "SqlDatabase" nebo SqlShardMap. 

[ ** \@refresh_credential_name =** ] "refresh_credential_name"  
Název serveru SQL Database. refresh_credential_name je nvarchar(128), bez výchozího nastavení.

[ ** \@server_name =** ] "server_name"  
Název serveru SQL Database, který by měl být přidán do zadané cílové skupiny. server_name by měl být zadán, pokud je target_type sqlserver. server_name je nvarchar(128), bez výchozí.

[ ** \@database_name =** ] "database_name"  
Název databáze, která by měla být přidána do zadané cílové skupiny. database_name by měl být zadán, pokud je target_type "SqlDatabase". database_name je nvarchar(128), bez výchozí.

[ ** \@elastic_pool_name =** ] "elastic_pool_name"  
Název elastického fondu, který by měl být přidán do zadané cílové skupiny. elastic_pool_name by měl být zadán, pokud je target_type "SqlElasticPool". elastic_pool_name je nvarchar(128), bez výchozího nastavení.

[ ** \@shard_map_name =** ] "shard_map_name"  
Název fondu mapy střepů, který by měl být přidán do zadané cílové skupiny. elastic_pool_name by měl být zadán, pokud je target_type SqlSqlShardMap. shard_map_name je nvarchar(128), bez výchozí.

[ ** \@target_id =** ] target_group_id VÝSTUP  
Cílové identifikační číslo přiřazené členovi cílové skupiny, pokud bylo vytvořeno, bylo přidáno do cílové skupiny. target_id je výstupní proměnná typu uniqueidentifier s výchozím hodnotou NULL.
Hodnoty návratového kódu 0 (úspěch) nebo 1 (selhání)

#### <a name="remarks"></a>Poznámky
Úloha se provede ve všech jednotlivých databázích v rámci databázového serveru SQL nebo v elastickém fondu v době spuštění, když je do cílové skupiny zahrnut server SQL Database nebo elastický fond.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení mohou členové pevné role serveru sysadmin provést tuto uloženou proceduru. Omezují uživatele pouze sledovat úlohy, můžete udělit uživateli být součástí následující role databáze v databázi agenta práce zadané při vytváření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních těchto rolí naleznete v části Oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úloh, které jsou vlastněny jinými uživateli.

#### <a name="examples"></a>Příklady
Následující příklad přidá všechny databáze na serverech V Londýně a New Yorku do skupiny Servery udržování informací o zákaznících. Musíte se připojit k databázi úloh zadaných při vytváření agenta úlohy, v tomto případě ElasticJobs.

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



Argumenty @target_group_name [ = ] 'target_group_name'  
Název cílové skupiny, ze které chcete odebrat člena cílové skupiny. target_group_name je nvarchar(128), bez výchozí.

[ @target_id = ] target_id  
 Cílové identifikační číslo přiřazené členovi cílové skupiny, který má být odebrán. target_id je jedinečný identifikátor s výchozím hodnotou NULL.

#### <a name="return-code-values"></a>Vrácené hodnoty kódu
0 (úspěch) nebo 1 (selhání)

#### <a name="remarks"></a>Poznámky
Cílové skupiny poskytují snadný způsob, jak cílit úlohu v kolekci databází.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení mohou členové pevné role serveru sysadmin provést tuto uloženou proceduru. Omezují uživatele pouze sledovat úlohy, můžete udělit uživateli být součástí následující role databáze v databázi agenta práce zadané při vytváření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních těchto rolí naleznete v části Oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úloh, které jsou vlastněny jinými uživateli.

#### <a name="examples"></a>Příklady
Následující příklad odebere londýnský server ze skupiny Servery, které udržují informace o zákaznících. Musíte se připojit k databázi úloh zadaných při vytváření agenta úlohy, v tomto případě ElasticJobs.

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
[ ** \@job_name =** ] "job_name"  
Název úlohy, pro kterou chcete odstranit záznamy historie. job_name je nvarchar(128), s výchozí NULL. Musí být zadána job_id nebo job_name, ale nelze zadat obě.

[ ** \@job_id =** ] job_id  
 Identifikační číslo úlohy pro záznamy, které mají být odstraněny. job_id je uniqueidentifier, s výchozí NULL. Musí být zadána job_id nebo job_name, ale nelze zadat obě.

[ ** \@oldest_date =** ] oldest_date  
 Nejstarší záznam, který se má uchovat v historii. oldest_date je DATETIME2, s výchozí hodnotou NULL. Pokud je zadánoldest_date sp_purge_jobhistory odebere pouze záznamy, které jsou starší než zadaná hodnota.

#### <a name="return-code-values"></a>Vrácené hodnoty kódu
0 (úspěch) nebo 1 (selhání) Poznámky Cílové skupiny poskytují snadný způsob, jak cílit úlohu na kolekci databází.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení mohou členové pevné role serveru sysadmin provést tuto uloženou proceduru. Omezují uživatele pouze sledovat úlohy, můžete udělit uživateli být součástí následující role databáze v databázi agenta práce zadané při vytváření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních těchto rolí naleznete v části Oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úloh, které jsou vlastněny jinými uživateli.

#### <a name="examples"></a>Příklady
Následující příklad přidá všechny databáze na serverech V Londýně a New Yorku do skupiny Servery udržování informací o zákaznících. Musíte se připojit k databázi úloh zadaných při vytváření agenta úlohy, v tomto případě ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Zobrazení úlohy

Následující zobrazení jsou k dispozici v [databázi úloh](sql-database-job-automation-overview.md#job-database).


|Zobrazit  |Popis  |
|---------|---------|
|[job_executions](#job_executions-view)     |  Zobrazuje historii spuštění úlohy.      |
|[Úlohy](#jobs-view)     |   Zobrazí všechny úlohy.      |
|[job_versions](#job_versions-view)     |   Zobrazí všechny verze úloh.      |
|[pracovní kroky](#jobsteps-view)     |     Zobrazí všechny kroky v aktuální verzi každé úlohy.    |
|[jobstep_versions](#jobstep_versions-view)     |     Zobrazí všechny kroky ve všech verzích každé úlohy.    |
|[target_groups](#target_groups-view)     |      Zobrazí všechny cílové skupiny.   |
|[target_group_members](#target_group_members-view)     |   Zobrazí všechny členy všech cílových skupin.      |


### <a name="job_executions-view"></a><a name="job_executions-view"></a>job_executions zobrazení

[pracovní místa]. To job_executions je v pořádku.

Zobrazuje historii spuštění úlohy.


|Název sloupce|   Datový typ   |Popis|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  Jedinečné ID instance provádění úlohy.
|**Job_name**   |nvarchar(128)  |Název úlohy.
|**Job_id** |uniqueidentifier|  Jedinečné ID úlohy.
|**job_version**    |int    |Verze úlohy (automaticky aktualizována při každé změně úlohy).
|**step_id**    |int|   Jedinečný identifikátor (pro tuto úlohu) pro daný krok. Null označuje, že se jedná o spuštění nadřazené úlohy.
|**is_active**| bitové |Označuje, zda jsou informace aktivní nebo neaktivní. 1 označuje aktivní úlohy a 0 označuje neaktivní.
|**Životního cyklu**| nvarchar(50)|Hodnota označující stav úlohy:'Vytvořeno', "Probíhá", "Nepodařilo se", "Proběhlo", přeskočeno, "SucceededWithSkipped"|
|**create_time**|   datetime2(7)|   Datum a čas vytvoření projektu.
|**start_time** |datetime2(7)|  Datum a čas spuštění úlohy. Null, pokud úloha ještě nebyla provedena.
|**end_time**|  datetime2(7)    |Datum a čas dokončení úlohy. Null, pokud úloha ještě nebyla provedena nebo ještě nebyla dokončena spuštění.
|**current_attempts**   |int    |Počet opakování kroku. Nadřazená úloha bude 0, podřízené spuštění úloh bude 1 nebo vyšší na základě zásad provádění.
|**current_attempt_start_time** |datetime2(7)|  Datum a čas spuštění úlohy. Null označuje, že se jedná o spuštění nadřazené úlohy.
|**last_message**   |Nvarchar(max)| Zpráva o historii úlohy nebo kroku. 
|**target_type**|   nvarchar(128)   |Typ cílové databáze nebo kolekce databází včetně všech databází na serveru, všech databází v elastickém fondu nebo v databázi. Platné hodnoty pro target_type jsou SqlServer, "SqlElasticPool" nebo SqlDatabase. Null označuje, že se jedná o spuštění nadřazené úlohy.
|**target_id**  |uniqueidentifier|  Jedinečné ID člena cílové skupiny.  Null označuje, že se jedná o spuštění nadřazené úlohy.
|**target_group_name**  |nvarchar(128)  |Název cílové skupiny. Null označuje, že se jedná o spuštění nadřazené úlohy.
|**target_server_name**|    nvarchar(256)|  Název databázového serveru SQL obsaženého v cílové skupině. Zadáno pouze v případě, target_type je "SqlServer". Null označuje, že se jedná o spuštění nadřazené úlohy.
|**target_database_name**   |nvarchar(128)| Název databáze obsažené v cílové skupině. Zadáno pouze v případě, target_type je "SqlDatabase". Null označuje, že se jedná o spuštění nadřazené úlohy.


### <a name="jobs-view"></a>zobrazení úloh

[pracovní místa]. [práce]

Zobrazí všechny úlohy.

|Název sloupce|   Datový typ|  Popis|
|------|------|-------|
|**Job_name**|  nvarchar(128)   |Název úlohy.|
|**Job_id**|    uniqueidentifier    |Jedinečné ID úlohy.|
|**job_version**    |int    |Verze úlohy (automaticky aktualizována při každé změně úlohy).|
|**Popis**    |nvarchar (512)| Popis úlohy. povolený bit Označuje, zda je úloha povolena nebo zakázána. 1 označuje povolené úlohy a 0 označuje zakázané úlohy.|
|**schedule_interval_type** |nvarchar(50)   |Hodnota označující, kdy má být úloha provedena:'Jednou', "Minuty", "Hodiny", "Dny", Týdny, Měsíce
|**schedule_interval_count**|   int|    Počet schedule_interval_type období mezi jednotlivými spuštěními úlohy.|
|**schedule_start_time**    |datetime2(7)|  Datum a čas, kdy byla úloha naposledy spuštěna.|
|**schedule_end_time**| datetime2(7)|   Datum a čas, kdy byla úloha naposledy dokončena.|


### <a name="job_versions-view"></a><a name="job_versions-view"></a>zobrazení job_versions

[pracovní místa]. [job_versions]

Zobrazí všechny verze úloh.

|Název sloupce|   Datový typ|  Popis|
|------|------|-------|
|**Job_name**|  nvarchar(128)   |Název úlohy.|
|**Job_id**|    uniqueidentifier    |Jedinečné ID úlohy.|
|**job_version**    |int    |Verze úlohy (automaticky aktualizována při každé změně úlohy).|


### <a name="jobsteps-view"></a>zobrazení jobsteps

[pracovní místa]. To je v pořádku.

Zobrazí všechny kroky v aktuální verzi každé úlohy.

|Název sloupce    |Datový typ| Popis|
|------|------|-------|
|**Job_name**   |nvarchar(128)| Název úlohy.|
|**Job_id** |uniqueidentifier   |Jedinečné ID úlohy.|
|**job_version**|   int|    Verze úlohy (automaticky aktualizována při každé změně úlohy).|
|**step_id**    |int    |Jedinečný identifikátor (pro tuto úlohu) pro daný krok.|
|**step_name**  |nvarchar(128)  |Jedinečný (pro tuto úlohu) název kroku.|
|**command_type**   |nvarchar(50)   |Typ příkazu, který má být proveden v kroku úlohy. Pro v1 hodnota musí rovnat a výchozí 'TSql'.|
|**command_source** |nvarchar(50)|  Umístění příkazu. Pro v1 'Inline' je výchozí a pouze přijatá hodnota.|
|**Příkaz**|   Nvarchar(max)|  Příkazy, které mají být provedeny elastické úlohy prostřednictvím command_type.|
|**credential_name**|   nvarchar(128)   |Název pověření s rozsahem databáze, které se používá k provedení úlohy.|
|**target_group_name**| nvarchar(128)   |Název cílové skupiny.|
|**target_group_id**|   uniqueidentifier|   Jedinečné ID cílové skupiny.|
|**initial_retry_interval_seconds**|    int |Zpoždění před prvním pokusem o opakování. Výchozí hodnota je 1.|
|**maximum_retry_interval_seconds** |int|   Maximální prodleva mezi pokusy o opakování. Pokud by se prodleva mezi opakovanými pokusy zvětšila, než je tato hodnota, je místo toho omezena na tuto hodnotu. Výchozí hodnota je 120.|
|**retry_interval_backoff_multiplier**  |reálná|  Multiplikátor použít zpoždění opakování, pokud se nezdaří více pokusů o spuštění kroku úlohy. Výchozí hodnota je 2.0.|
|**retry_attempts** |int|   Počet pokusů o opakování se pokusí použít, pokud tento krok selže. Výchozí hodnota 10, což znamená žádné pokusy o opakování.|
|**step_timeout_seconds**   |int|   Doba v minutách mezi pokusy o opakování. Výchozí hodnota je 0, což znamená interval 0 minut.|
|**output_type**    |nvarchar(11)|  Umístění příkazu. V aktuálním náhledu je výchozí a pouze přijatá hodnota "Inline".|
|**output_credential_name**|    nvarchar(128)   |Název pověření, která mají být použita pro připojení k cílovému serveru pro uložení sady výsledků.|
|**output_subscription_id**|    uniqueidentifier|   Jedinečné ID odběru cílového serveru\databáze pro výsledky nastavené z provádění dotazu.|
|**output_resource_group_name** |nvarchar(128)| Název skupiny prostředků, ve kterém je umístěn cílový server.|
|**output_server_name**|    nvarchar(256)   |Název cílového serveru pro sadu výsledků.|
|**output_database_name**   |nvarchar(128)| Název cílové databáze pro sadu výsledků.|
|**output_schema_name** |Nvarchar(max)| Název cílového schématu. Výchozí hodnota dbo, pokud není zadána.|
|**output_table_name**| Nvarchar(max)|  Název tabulky pro uložení výsledků nastavených z výsledků dotazu. Tabulka bude vytvořena automaticky na základě schématu sady výsledků, pokud ještě neexistuje. Schéma musí odpovídat schématu sady výsledků.|
|**max_parallelism**|   int|    Maximální počet databází na elastický fond, který krok úlohy bude spuštěn najednou. Výchozí hodnota je NULL, což znamená žádné omezení. |


### <a name="jobstep_versions-view"></a><a name="jobstep_versions-view"></a>jobstep_versions zobrazení

[pracovní místa]. [jobstep_versions]

Zobrazí všechny kroky ve všech verzích každé úlohy. Schéma je shodné s [jobsteps](#jobsteps-view).

### <a name="target_groups-view"></a><a name="target_groups-view"></a>target_groups zobrazení

[pracovní místa]. [target_groups]

Zobrazí seznam všech cílových skupin.

|Název sloupce|Datový typ| Popis|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |Název cílové skupiny, kolekce databází. 
|**target_group_id**    |uniqueidentifier   |Jedinečné ID cílové skupiny.

### <a name="target_group_members-view"></a><a name="target_group_members-view"></a>target_group_members zobrazení

[pracovní místa]. Je target_group_members.

Zobrazí všechny členy všech cílových skupin.

|Název sloupce|Datový typ| Popis|
|-----|-----|-----|
|**target_group_name**  |nvarchar(128)|Název cílové skupiny, kolekce databází. |
|**target_group_id**    |uniqueidentifier   |Jedinečné ID cílové skupiny.|
|**membership_type**    |int|   Určuje, zda je člen cílové skupiny zahrnut nebo vyloučen do cílové skupiny. Platné hodnoty pro target_group_name jsou "Zahrnout" nebo "Vyloučit".|
|**target_type**    |nvarchar(128)| Typ cílové databáze nebo kolekce databází včetně všech databází na serveru, všech databází v elastickém fondu nebo v databázi. Platné hodnoty pro target_type jsou "SqlServer", "SqlElasticPool", "SqlDatabase" nebo SqlShardMap.|
|**target_id**  |uniqueidentifier|  Jedinečné ID člena cílové skupiny.|
|**refresh_credential_name**    |nvarchar(128)  |Název pověření s rozsahem databáze, které se používá k připojení k členu cílové skupiny.|
|**subscription_id**    |uniqueidentifier|  Jedinečné ID předplatného.|
|**resource_group_name**    |nvarchar(128)| Název skupiny prostředků, ve které se nachází člen cílové skupiny.|
|**Název_serveru**    |nvarchar(128)  |Název databázového serveru SQL obsaženého v cílové skupině. Zadáno pouze v případě, target_type je "SqlServer". |
|**Název_databáze**  |nvarchar(128)  |Název databáze obsažené v cílové skupině. Zadáno pouze v případě, target_type je "SqlDatabase".|
|**elastic_pool_name**  |nvarchar(128)| Název elastického fondu obsaženého v cílové skupině. Zadáno pouze v případě, target_type je "SqlElasticPool".|
|**shard_map_name** |nvarchar(128)| Název mapy úlomků obsažených v cílové skupině. Zadáno pouze v případě, target_type je "SqlShardMap".|


## <a name="resources"></a>Prostředky

 - ![Ikona odkazu na téma](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "Ikona odkazu na téma") [Transact-SQL Syntax Conventions](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>Další kroky

- [Vytváření a správa elastických úloh s využitím PowerShellu](elastic-jobs-powershell.md)
- [Autorizace a oprávnění SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
