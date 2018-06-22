---
title: Vytvářet a spravovat Azure SQL elastické databáze úlohy pomocí jazyka Transact-SQL (T-SQL) | Microsoft Docs
description: Spusťte skripty mezi mnoha databázemi se agenta úlohy elastické databáze pomocí jazyka Transact-SQL (T-SQL).
services: sql-database
author: jaredmoo
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 06/14/2018
ms.author: jaredmoo
ms.openlocfilehash: fb6e4ebd635d8afa8e679ee5bb0f5646f28f887b
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313336"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Vytvářet a spravovat úlohy elastické databáze pomocí jazyka Transact-SQL (T-SQL)

Tento článek obsahuje mnoho ukázkové scénáře, abyste mohli začít pracovat s elastické úlohy pomocí T-SQL.

Příklady používají [uložené procedury](#job-stored-procedures) a [zobrazení](#job-views) k dispozici v [ *úlohy databáze*](elastic-jobs-overview.md#job-database).

Příkaz Transact-SQL (T-SQL) se používá k vytvoření, konfiguraci, spuštění a spravovat úlohy. Vytvoření elastické úlohy agenta není podporováno v T-SQL, takže je nutné nejprve vytvořit *elastické úlohy agenta* pomocí portálu nebo [prostředí PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Vytvoření pověření pro provádění úlohy

Přihlašovací údaje slouží k připojení k databázím cíl pro spuštění skriptu. Přihlašovací údaje musí mít příslušná oprávnění v databázích určeného cílová skupina se úspěšně spustit skript. Při použití serveru nebo člen fondu cílové skupiny, je vysoce navrhované vytvořit hlavní pověření pro použití aktualizujte přihlašovací údaje před rozšíření serveru nebo fondu v době provádění úlohy. Přihlašovací údaje databáze obor se vytvoří v databázi úlohy agenta. Musí být stejné přihlašovací údaje používané k *vytvořit přihlášení* a *vytvořit uživateli z přihlášení k udělení oprávnění databáze přihlášení* na cílovým databázím.


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

## <a name="create-a-target-group-servers"></a>Vytvořte cílovou skupinu (servery)

Následující příklad ukazuje, jak provést úlohy pro všechny databáze na serveru.  
Připojení k [ *úlohy databáze* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:


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


## <a name="exclude-a-single-database"></a>Vyloučit jedné databáze

Následující příklad ukazuje, jak provést úlohy pro všechny databáze na serveru, s výjimkou databáze s názvem *MappingDB*.  
Připojení k [ *úlohy databáze* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group = N'ServerGroup'
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

--Excude a database target member from the server target group
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


## <a name="create-a-target-group-pools"></a>Vytvořte cílovou skupinu (fondy)

Následující příklad ukazuje, jak pro všechny databáze v jedné nebo více elastické fondy.  
Připojení k [ *úlohy databáze* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

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


## <a name="deploy-new-schema-to-many-databases"></a>Nasadit nové schéma do mnoha databázemi

Následující příklad ukazuje, jak nasadit nové schéma pro všechny databáze.  
Připojení k [ *úlohy databáze* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:


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


## <a name="data-collection-using-built-in-parameters"></a>Shromažďování dat pomocí předdefinovaných parametrů

V mnoha scénářích kolekci dat může být užitečný některé z těchto proměnných skriptování pomohou po zpracování výsledků úlohy.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Například skupina všechny výsledky ze stejné spuštění úlohy společně použít *$(job_execution_id)* jak je znázorněno v následujícím příkazu:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Monitorování výkonu databáze

Následující příklad vytvoří novou úlohu pro shromažďování dat z více databází.  
Připojení k [ *úlohy databáze* ](elastic-jobs-overview.md#job-database) a spusťte následující příkazy:

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
@output_credential_name=’myjobcred’,
@output_server_name=’server1.database.windows.net',
@output_database_name=’<resultsdb>',
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
@output_server_name=’server1.database.windows.net',
@output_database_name=’resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>Zobrazení definice úlohy

Následující příklad ukazuje, jak chcete-li zobrazit aktuální definice úlohy.  
Připojení k [ *úlohy databáze* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

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


## <a name="begin-ad-hoc-execution-of-a-job"></a>Zahájit ad-hoc provádění úlohy

Následující příklad ukazuje, jak pro okamžité spuštění úlohy.  
Připojení k [ *úlohy databáze* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

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


## <a name="schedule-execution-of-a-job"></a>Plán spuštění úlohy

Následující příklad ukazuje, jak naplánovat úlohu pro budoucí provedení.  
Připojení k [ *úlohy databáze* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Monitorovat stav spuštění úlohy

Následující příklad ukazuje, jak chcete-li zobrazit podrobnosti o stavu spuštění pro všechny úlohy.  
Připojení k [ *úlohy databáze* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named ‘ResultsPoolJob’
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named ‘ResultsPoolsJob’
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions 
WHERE is_active = 1
ORDER BY start_time DESC
```


## <a name="cancel-a-job"></a>Zrušení úlohy

Následující příklad ukazuje, jak zrušit úlohy.  
Připojení k [ *úlohy databáze* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

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


## <a name="delete-old-job-history"></a>Odstranit staré historie úlohy

Následující příklad ukazuje, jak odstranit historii úlohy před konkrétním datem.  
Připojení k [ *úlohy databáze* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Odstranit úlohu a všechny jeho historie úlohy

Následující příklad ukazuje, jak odstranit úlohu a všechny související historie úlohy.  
Připojení k [ *úlohy databáze* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Úloha uložené procedury

Následující uložené procedury jsou v [úlohy databáze](elastic-jobs-overview.md#job-database).



|Uložená procedura  |Popis  |
|---------|---------|
|[sp_add_job](#spaddjob)     |     Přidá novou úlohu.    |
|[sp_update_job ](#spupdatejob)    |      Aktualizuje existující úlohy.   |
|[sp_delete_job](#spdeletejob)     |      Odstraní stávající úloze.   |
|[sp_add_jobstep](#spaddjobstep)    |    Přidá krok úlohy.     |
|[sp_update_jobstep](#spupdatejobstep)     |     Aktualizuje krok úlohy.    |
|[sp_delete_jobstep](#spdeletejobstep)     |     Odstraní krok úlohy.    |
|[sp_start_job](#spstartjob)    |  Spustí provádění úlohy.       |
|[sp_stop_job](#spstopjob)     |     Ukončí provádění úlohy.   |
|[sp_add_target_group](#spaddtargetgroup)    |     Přidá cílovou skupinu.    |
|[sp_delete_target_group](#spdeletetargetgroup)     |    Odstraní cílovou skupinu.     |
|[sp_add_target_group_member](#spaddtargetgroupmember)     |    Přidá v databázi nebo skupině databází na cílovou skupinu.     |
|[sp_delete_target_group_member](#spdeletetargetgroupmember)     |     Odebere člena skupiny cíl cílovou skupinu.    |
|[sp_purge_jobhistory ](#sppurgejobhistory)    |    Odebere záznamy historie pro úlohu.     |





### <a name="spaddjob"></a>sp_add_job

Přidá novou úlohu. 
  
#### <a name="syntax"></a>Syntaxe  
  

```sql
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

[  **@job_name =** ] 'job_name.  
Název úlohy. Název musí být jedinečné a nesmí obsahovat znak procenta (%). job_name je nvarchar(128) s žádná výchozí hodnota.

[  **@description =** ] "Popis"  
Popis úlohy. Popis je nvarchar(512), výchozí hodnota je NULL. Pokud je vynechán popis, prázdný řetězec se používá.

[  **@enabled =** ] povoleno  
Jestli je povolené plán úloh. Povolena je bit, výchozí hodnota je 0 (zakázáno). Pokud je 0, úloha není povoleno a nejde spustit podle plánu; ale spuštěním ručně. Pokud 1, úloha se spustí podle plánu a můžete spustit také ručně.

[  **@schedule_interval_type =**] schedule_interval_type  
Hodnota znamená, když se úloha se má provádět. schedule_interval_type je nvarchar(50), výchozí hodnota je jednou a může být jedna z následujících hodnot:
- Jednou,
- 'Minut.
- 'Hodin.
- 'Dnů.
- 'Týdnů.
- "Měsíců.

[  **@schedule_interval_count =** ] schedule_interval_count  
Počet období schedule_interval_count proběhnout mezi každé spuštění úlohy. schedule_interval_count je int, výchozí hodnota je 1. Hodnota musí být větší než nebo rovno 1.

[  **@schedule_start_time =** ] schedule_start_time  
Datum, na která úloha se může začít provádění. schedule_start_time je DATETIME2, s výchozím 00:00:00.0000000 0001-01-01.

[  **@schedule_end_time =** ] schedule_end_time  
Datum, na která úloha se může zastavit provádění. schedule_end_time je DATETIME2, s výchozím 9999-12-31 11:59:59.0000000. 

[  **@job_id =** ] job_id výstup  
Úloha identifikační číslo přiřazené do úlohy, pokud se úspěšně vytvořil. job_id je proměnná výstup z typ uniqueidentifier.

#### <a name="return-code-values"></a>Návratové hodnoty kódu

0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
sp_add_job se musí spouštět z databáze agenta úlohy při vytváření úlohy agenta.
Po provedení sp_add_job přidat úloha sp_add_jobstep lze přidat kroky, které provádějí aktivity pro úlohu. Číslo počáteční verze úlohy je 0, což se zvýší na 1 při prvním krokem je přidání.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení členové role pevného serveru sysadmin mohou spouštět tuto uloženou proceduru. Omezit přístup uživatele k právě nebudou moct sledování úloh, můžete udělit uživatelům být součástí následující role databáze v databázi agenta úlohy při vytváření úlohy agenta:

- jobs_reader

Podrobnosti o oprávnění z těchto rolí najdete v části oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úlohy, které vlastní jiní uživatelé.

### <a name="spupdatejob"></a>sp_update_job

Aktualizuje existující úlohy.

#### <a name="syntax"></a>Syntaxe

```sql
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
[  **@job_name =** ] 'job_name.  
Název úlohy aktualizovat. job_name je nvarchar(128).

[  **@new_name =** ] 'Nový_název.  
Nový název úlohy. nový_název je nvarchar(128).

[  **@description =** ] "Popis"  
Popis úlohy. Popis je nvarchar(512).

[  **@enabled =** ] povoleno  
Určuje, jestli plán úloh je povoleno (1) nebo není povolen (0). Povolena je bit.

[  **@schedule_interval_type=** ] schedule_interval_type  
Hodnota znamená, když se úloha se má provádět. schedule_interval_type je nvarchar(50) a může být jedna z následujících hodnot:

- Jednou,
- 'Minut.
- 'Hodin.
- 'Dnů.
- 'Týdnů.
- "Měsíců.

[  **@schedule_interval_count=** ] schedule_interval_count  
Počet období schedule_interval_count proběhnout mezi každé spuštění úlohy. schedule_interval_count je int, výchozí hodnota je 1. Hodnota musí být větší než nebo rovno 1.

[  **@schedule_start_time=** ] schedule_start_time  
Datum, na která úloha se může začít provádění. schedule_start_time je DATETIME2, s výchozím 00:00:00.0000000 0001-01-01.

[  **@schedule_end_time=** ] schedule_end_time  
Datum, na která úloha se může zastavit provádění. schedule_end_time je DATETIME2, s výchozím 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Návratové hodnoty kódu
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Po provedení sp_add_job přidat úloha sp_add_jobstep lze přidat kroky, které provádějí aktivity pro úlohu. Číslo počáteční verze úlohy je 0, což se zvýší na 1 při prvním krokem je přidání.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení členové role pevného serveru sysadmin mohou spouštět tuto uloženou proceduru. Omezit přístup uživatele k právě nebudou moct sledování úloh, můžete udělit uživatelům být součástí následující role databáze v databázi agenta úlohy při vytváření úlohy agenta:
- jobs_reader

Podrobnosti o oprávnění z těchto rolí najdete v části oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úlohy, které vlastní jiní uživatelé.



### <a name="spdeletejob"></a>sp_delete_job

Odstraní stávající úloze.

#### <a name="syntax"></a>Syntaxe

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumenty
[  **@job_name =** ] 'job_name.  
Název úlohy pro odstranění. job_name je nvarchar(128).

[  **@force =** ] vynutit  
Určuje, jestli se má odstranit, pokud úloha nemá žádné spuštěních v průběhu a zrušte všechny probíhající spuštěních (1) nebo selhání, pokud jsou všechny úlohy spuštěních v průběhu (0). platnost je bit.

#### <a name="return-code-values"></a>Návratové hodnoty kódu
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Historie úlohy je automaticky odstraněna při odstranění úlohy.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení členové role pevného serveru sysadmin mohou spouštět tuto uloženou proceduru. Omezit přístup uživatele k právě nebudou moct sledování úloh, můžete udělit uživatelům být součástí následující role databáze v databázi agenta úlohy při vytváření úlohy agenta:
- jobs_reader

Podrobnosti o oprávnění z těchto rolí najdete v části oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úlohy, které vlastní jiní uživatelé.



### <a name="spaddjobstep"></a>sp_add_jobstep

Přidá krok úlohy.

#### <a name="syntax"></a>Syntaxe


```sql
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

[  **@job_name =** ] 'job_name.  
Název úlohy, do které chcete přidat krok. job_name je nvarchar(128).

[  **@step_id =** ] step_id  
Pořadí identifikační číslo pro krok úlohy. Krok identifikačními čísly začínají znakem 1 a zvýšit bez mezer. Pokud toto id již používá existující krok, pak krok a všechny následující kroky se bude mít jejich id je zvýší tak, aby tento nový krok lze vložit do sekvenci. Pokud není zadáno, step_id bude automaticky přiřazen na poslední v pořadí kroků. step_id je typ int.

[  **@step_name =** ] step_name  
Název kroku. Musí být zadán, s výjimkou první krok úlohu, u které má výchozí název "Krok úlohy" (pro práci v aplikaci). STEP_NAME je nvarchar(128).

[  **@command_type =** ] 'command_type.  
Typ příkazu, který spouští tento krok úlohy. command_type je nvarchar(50) s výchozí hodnotou TSql, znamená to, že hodnota @command_type parametr je skriptu T-SQL.

-Li zadána, hodnota musí být TSql.

[  **@command_source =** ] 'command_source.  
Typ umístění pro uložení příkaz. command_source je nvarchar(50) s výchozí hodnotou vložené, znamená to, že hodnota @command_source parametr je literál text příkazu.

-Li zadána, hodnota musí být vložené.

[  **@command =** ], příkaz:  
Příkaz musí být platný skriptu T-SQL a pak tento krok úlohy provedený. příkaz je nvarchar(max), výchozí hodnota je NULL.

[  **@credential_name =** ] 'credential_name.  
Název databáze obor přihlašovací údaje uložené v této databázi úlohy ovládací prvek, který se používá pro připojení k každé cílové databáze v rámci cílové skupiny, když se spustí tento krok. credential_name je nvarchar(128).

[  **@target_group_name =** ] 'target-group_name.  
Název cílové skupiny, která obsahuje cílové databáze, které se bude provádět krok úlohy na. target_group_name je nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Zpoždění před pokusem první opakovat, pokud úloha kroku dojde k chybě při pokusu o provedení počáteční. initial_retry_interval_seconds je int, výchozí hodnotu 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maximální prodleva mezi pokusy o opakování. Pokud prodleva mezi opakovanými pokusy by růst větší než tato hodnota, je je se na tuto hodnotu místo omezená. maximum_retry_interval_seconds je int, s výchozí hodnotou 120.

[  **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Násobitel chcete použít pro zpoždění opakování, pokud úlohy několika krok provádění pokusů o selže. Například pokud první opakování měl zpoždění 5 sekund a omezení rychlosti násobitel je 2.0, pak druhý opakování bude mít zpoždění 10 sekund a třetí opakování bude mít zpoždění 20 sekund. retry_interval_backoff_multiplier je skutečné s výchozí hodnotou 2.0.

[  **@retry_attempts =** ] retry_attempts  
Počet opakovaných pokusů o provedení spuštění, pokud počáteční pokus selže. Například pokud je hodnota retry_attempts 10, pak bude 1 počáteční pokus a 10 pokusy o opakování, udělíte celkem 11 pokusů. Pokud poslední opakovat pokus selže, bude ukončen provádění úlohy s cyklus životnosti se nezdařilo. retry_attempts je int, s výchozí hodnotou 10.

[  **@step_timeout_seconds =** ] step_timeout_seconds  
Maximální množství času, které jsou povoleny pro krok provést. Pokud dojde k překročení této doby, bude ukončen provádění úlohy s cyklus životnosti TimedOut. step_timeout_seconds je int, s výchozí hodnotou 43 200 sekund (12 hodin).

[  **@output_type =** ] 'output_type.  
Pokud není null, typ cílového umístění, které nastavit první výsledek příkazu je zapsán do. output_type je nvarchar(50), výchozí hodnota je NULL.

-Li zadána, hodnota musí být SqlDatabase.

[  **@output_credential_name =** ] 'output_credential_name.  
Pokud není null, název databáze obor přihlašovacích údajů, který se používá pro připojení k cílové databázi výstup. Musí být zadán, pokud output_type rovná SqlDatabase. output_credential_name je nvarchar(128), výchozí hodnotou Null.

[  **@output_subscription_id =** ] 'output_subscription_id.  
Vyžaduje popis.

[  **@output_resource_group_name =** ] 'output_resource_group_name.  
Vyžaduje popis.

[  **@output_server_name =** ] 'output_server_name.  
Pokud není null, plně kvalifikovaný název DNS serveru, který obsahuje výstup cílové databáze. Musí být zadán, pokud output_type rovná SqlDatabase. output_server_name je nvarchar(256), výchozí hodnota je NULL.

[  **@output_database_name =** ] 'output_database_name.  
Pokud není null, název databáze, která obsahuje výstupní cílové tabulky. Musí být zadán, pokud output_type rovná SqlDatabase. output_database_name je nvarchar(128), výchozí hodnota je NULL.

[  **@output_schema_name =** ] 'output_schema_name.  
Pokud není null, název schématu SQL, která obsahuje výstupní cílové tabulky. Pokud se output_type rovná SqlDatabase, výchozí hodnota je dbo. output_schema_name je nvarchar(128).

[  **@output_table_name =** ] 'output_table_name.  
Pokud není null, název tabulky, která nastavit první výsledek příkazu se zapíšou do. Pokud ještě neexistuje v tabulce, bude vytvořena podle schématu vrací sadu výsledků dotazu. Musí být zadán, pokud output_type rovná SqlDatabase. output_table_name je nvarchar(128), výchozí hodnotou Null.

[  **@job_version =** ] job_version výstup  
Výstupní parametr, který se přiřadí číslo verze nové úlohy. job_version je int.

[  **@max_parallelism =** ] max_parallelism výstup  
Maximální úroveň paralelismus za elastického fondu. Pokud sadu, pak krok úlohy bude omezený spustit jen u do maximální počet databází pro elastický fond. To platí pro každý elastického fondu, který je buď přímo součástí cílové skupiny nebo je v serveru, který je zahrnut v cílové skupině. max_parallelism je int.


#### <a name="return-code-values"></a>Návratové hodnoty kódu
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Pokud je sp_add_jobstep úspěšná, aktuální číslo verze úlohy se zvýší. Při příštím spuštění úlohy se spustí, se použije na novou verzi. Pokud právě probíhá úloha, že provádění nebude obsahovat nový krok.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení členové role pevného serveru sysadmin mohou spouštět tuto uloženou proceduru. Omezit přístup uživatele k právě nebudou moct sledování úloh, můžete udělit uživatelům být součástí následující role databáze v databázi agenta úlohy při vytváření úlohy agenta:  

- jobs_reader

Podrobnosti o oprávnění z těchto rolí najdete v části oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úlohy, které vlastní jiní uživatelé.



### <a name="spupdatejobstep"></a>sp_update_jobstep

Aktualizuje krok úlohy.

#### <a name="syntax"></a>Syntaxe

```sql
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
[  **@job_name =** ] 'job_name.  
Název úlohy, ke kterému patří krok. job_name je nvarchar(128).

[  **@step_id =** ] step_id  
Identifikační číslo pro krok úlohy má být změněn. Musí být zadán buď step_id nebo step_name. step_id je typ int.

[  **@step_name =** ] 'step_name.  
Název kroku má být změněn. Musí být zadán buď step_id nebo step_name. STEP_NAME je nvarchar(128).

[  **@new_id =** ] new_id  
Nové pořadí identifikační číslo pro krok úlohy. Krok identifikačními čísly začínají znakem 1 a zvýšit bez mezer. Pokud je přeuspořádány krok, pak další kroky budou automaticky označuje.

[  **@new_name =** ] 'Nový_název.  
Nový název kroku. nový_název je nvarchar(128).

[  **@command_type =** ] 'command_type.  
Typ příkazu, který spouští tento krok úlohy. command_type je nvarchar(50) s výchozí hodnotou TSql, znamená to, že hodnota @command_type parametr je skriptu T-SQL.

-Li zadána, hodnota musí být TSql.

[  **@command_source =** ] 'command_source.  
Typ umístění pro uložení příkaz. command_source je nvarchar(50) s výchozí hodnotou vložené, znamená to, že hodnota @command_source parametr je literál text příkazu.

-Li zadána, hodnota musí být vložené.

[  **@command =** ], příkaz:  
Příkazy musí být platný skriptu T-SQL a pak tento krok úlohy provedený. příkaz je nvarchar(max), výchozí hodnota je NULL.

[  **@credential_name =** ] 'credential_name.  
Název databáze obor přihlašovací údaje uložené v této databázi úlohy ovládací prvek, který se používá pro připojení k každé cílové databáze v rámci cílové skupiny, když se spustí tento krok. credential_name je nvarchar(128).

[  **@target_group_name =** ] 'target-group_name.  
Název cílové skupiny, která obsahuje cílové databáze, které se bude provádět krok úlohy na. target_group_name je nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Zpoždění před pokusem první opakovat, pokud úloha kroku dojde k chybě při pokusu o provedení počáteční. initial_retry_interval_seconds je int, výchozí hodnotu 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maximální prodleva mezi pokusy o opakování. Pokud prodleva mezi opakovanými pokusy by růst větší než tato hodnota, je je se na tuto hodnotu místo omezená. maximum_retry_interval_seconds je int, s výchozí hodnotou 120.

[  **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Násobitel chcete použít pro zpoždění opakování, pokud úlohy několika krok provádění pokusů o selže. Například pokud první opakování měl zpoždění 5 sekund a omezení rychlosti násobitel je 2.0, pak druhý opakování bude mít zpoždění 10 sekund a třetí opakování bude mít zpoždění 20 sekund. retry_interval_backoff_multiplier je skutečné s výchozí hodnotou 2.0.

[  **@retry_attempts =** ] retry_attempts  
Počet opakovaných pokusů o provedení spuštění, pokud počáteční pokus selže. Například pokud je hodnota retry_attempts 10, pak bude 1 počáteční pokus a 10 pokusy o opakování, udělíte celkem 11 pokusů. Pokud poslední opakovat pokus selže, bude ukončen provádění úlohy s cyklus životnosti se nezdařilo. retry_attempts je int, s výchozí hodnotou 10.

[  **@step_timeout_seconds =** ] step_timeout_seconds  
Maximální množství času, které jsou povoleny pro krok provést. Pokud dojde k překročení této doby, bude ukončen provádění úlohy s cyklus životnosti TimedOut. step_timeout_seconds je int, s výchozí hodnotou 43 200 sekund (12 hodin).

[  **@output_type =** ] 'output_type.  
Pokud není null, typ cílového umístění, které nastavit první výsledek příkazu je zapsán do. Pokud chcete resetovat hodnotu output_type zpět na hodnotu NULL, nastavte tento parametr hodnotu na hodnotu (prázdný řetězec). output_type je nvarchar(50), výchozí hodnota je NULL.

-Li zadána, hodnota musí být SqlDatabase.

[  **@output_credential_name =** ] 'output_credential_name.  
Pokud není null, název databáze obor přihlašovacích údajů, který se používá pro připojení k cílové databázi výstup. Musí být zadán, pokud output_type rovná SqlDatabase. Pokud chcete resetovat hodnotu output_credential_name zpět na hodnotu NULL, nastavte tento parametr hodnotu na hodnotu (prázdný řetězec). output_credential_name je nvarchar(128), výchozí hodnotou Null.

[  **@output_server_name =** ] 'output_server_name.  
Pokud není null, plně kvalifikovaný název DNS serveru, který obsahuje výstup cílové databáze. Musí být zadán, pokud output_type rovná SqlDatabase. Pokud chcete resetovat hodnotu output_server_name zpět na hodnotu NULL, nastavte tento parametr hodnotu na hodnotu (prázdný řetězec). output_server_name je nvarchar(256), výchozí hodnota je NULL.

[  **@output_database_name =** ] 'output_database_name.  
Pokud není null, název databáze, která obsahuje výstupní cílové tabulky. Musí být zadán, pokud output_type rovná SqlDatabase. Pokud chcete resetovat hodnotu output_database_name zpět na hodnotu NULL, nastavte tento parametr hodnotu na hodnotu (prázdný řetězec). output_database_name je nvarchar(128), výchozí hodnota je NULL.

[  **@output_schema_name =** ] 'output_schema_name.  
Pokud není null, název schématu SQL, která obsahuje výstupní cílové tabulky. Pokud se output_type rovná SqlDatabase, výchozí hodnota je dbo. Pokud chcete resetovat hodnotu output_schema_name zpět na hodnotu NULL, nastavte tento parametr hodnotu na hodnotu (prázdný řetězec). output_schema_name je nvarchar(128).

[  **@output_table_name =** ] 'output_table_name.  
Pokud není null, název tabulky, která nastavit první výsledek příkazu se zapíšou do. Pokud ještě neexistuje v tabulce, bude vytvořena podle schématu vrací sadu výsledků dotazu. Musí být zadán, pokud output_type rovná SqlDatabase. Pokud chcete resetovat hodnotu output_server_name zpět na hodnotu NULL, nastavte tento parametr hodnotu na hodnotu (prázdný řetězec). output_table_name je nvarchar(128), výchozí hodnotou Null.

[  **@job_version =** ] job_version výstup  
Výstupní parametr, který se přiřadí číslo verze nové úlohy. job_version je int.

[  **@max_parallelism =** ] max_parallelism výstup  
Maximální úroveň paralelismus za elastického fondu. Pokud sadu, pak krok úlohy bude omezený spustit jen u do maximální počet databází pro elastický fond. To platí pro každý elastického fondu, který je buď přímo součástí cílové skupiny nebo je v serveru, který je zahrnut v cílové skupině. Pokud chcete resetovat hodnotu max_parallelism zpět na hodnotu null, nastavte tento parametr hodnotu na hodnotu -1. max_parallelism je int.


#### <a name="return-code-values"></a>Návratové hodnoty kódu
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Neovlivní žádné spuštěních probíhající úlohy. Pokud je sp_update_jobstep úspěšná, se zvýší číslo verze úlohy. Při příštím spuštění úlohy se spustí, se použije na novou verzi.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení členové role pevného serveru sysadmin mohou spouštět tuto uloženou proceduru. Omezit přístup uživatele k právě nebudou moct sledování úloh, můžete udělit uživatelům být součástí následující role databáze v databázi agenta úlohy při vytváření úlohy agenta:

- jobs_reader

Podrobnosti o oprávnění z těchto rolí najdete v části oprávnění v tomto dokumentu. Pouze členové sysadmin mohou používat tuto uloženou proceduru upravit atributy úlohy, které jsou vlastněny jiných uživatelů




### <a name="spdeletejobstep"></a>sp_delete_jobstep

Krok úlohy se odebere z úlohy.

#### <a name="syntax"></a>Syntaxe


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumenty
[  **@job_name =** ] 'job_name.  
Název úlohy, ze kterého se odeberou krok. job_name je nvarchar(128) s žádná výchozí hodnota.

[  **@step_id =** ] step_id  
Identifikační číslo pro krok úlohy pro odstranění. Musí být zadán buď step_id nebo step_name. step_id je typ int.

[  **@step_name =** ] 'step_name.  
Název kroku k odstranění. Musí být zadán buď step_id nebo step_name. STEP_NAME je nvarchar(128).

[  **@job_version =** ] job_version výstup  
Výstupní parametr, který se přiřadí číslo verze nové úlohy. job_version je int.

#### <a name="return-code-values"></a>Návratové hodnoty kódu
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Neovlivní žádné spuštěních probíhající úlohy. Pokud je sp_update_jobstep úspěšná, se zvýší číslo verze úlohy. Při příštím spuštění úlohy se spustí, se použije na novou verzi.

Další kroky úlohy budou automaticky označuje k vyplnění mezeru po krok odstraněné úlohy.
 
#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení členové role pevného serveru sysadmin mohou spouštět tuto uloženou proceduru. Omezit přístup uživatele k právě nebudou moct sledování úloh, můžete udělit uživatelům být součástí následující role databáze v databázi agenta úlohy při vytváření úlohy agenta:
- jobs_reader

Podrobnosti o oprávnění z těchto rolí najdete v části oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úlohy, které vlastní jiní uživatelé.






### <a name="spstartjob"></a>sp_start_job

Spustí provádění úlohy.

#### <a name="syntax"></a>Syntaxe


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argumenty
[  **@job_name =** ] 'job_name.  
Název úlohy, ze kterého se odeberou krok. job_name je nvarchar(128) s žádná výchozí hodnota.

[  **@job_execution_id =** ] job_execution_id výstup  
Výstupní parametr, který bude přiřazeno id spuštění úlohy. job_version je typ uniqueidentifier.

#### <a name="return-code-values"></a>Návratové hodnoty kódu
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Žádné.
 
#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení členové role pevného serveru sysadmin mohou spouštět tuto uloženou proceduru. Omezit přístup uživatele k právě nebudou moct sledování úloh, můžete udělit uživatelům být součástí následující role databáze v databázi agenta úlohy při vytváření úlohy agenta:
- jobs_reader

Podrobnosti o oprávnění z těchto rolí najdete v části oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úlohy, které vlastní jiní uživatelé.

### <a name="spstopjob"></a>sp_stop_job

Ukončí provádění úlohy.

#### <a name="syntax"></a>Syntaxe


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argumenty
[  **@job_execution_id =** ] job_execution_id  
Identifikační číslo provádění úlohy zastavit. job_execution_id je uniqueidentifier výchozí hodnotu NULL.

#### <a name="return-code-values"></a>Návratové hodnoty kódu
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Žádné.
 
#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení členové role pevného serveru sysadmin mohou spouštět tuto uloženou proceduru. Omezit přístup uživatele k právě nebudou moct sledování úloh, můžete udělit uživatelům být součástí následující role databáze v databázi agenta úlohy při vytváření úlohy agenta:
- jobs_reader

Podrobnosti o oprávnění z těchto rolí najdete v části oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úlohy, které vlastní jiní uživatelé.


### <a name="spaddtargetgroup"></a>sp_add_target_group

Přidá cílovou skupinu.

#### <a name="syntax"></a>Syntaxe


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argumenty
[  **@target_group_name =** ] 'target_group_name.  
Název cílové skupiny k vytvoření. target_group_name je nvarchar(128) s žádná výchozí hodnota.

[  **@target_group_id =** ] target_group_id výstup cílové skupiny identifikační číslo přiřazené do úlohy, pokud se úspěšně vytvořil. target_group_id je proměnná výstup z typu uniqueidentifier, výchozí hodnota je NULL.

#### <a name="return-code-values"></a>Návratové hodnoty kódu
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Cílové skupiny poskytují snadný způsob, jak cíle úlohu v kolekci databází.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení členové role pevného serveru sysadmin mohou spouštět tuto uloženou proceduru. Omezit přístup uživatele k právě nebudou moct sledování úloh, můžete udělit uživatelům být součástí následující role databáze v databázi agenta úlohy při vytváření úlohy agenta:
- jobs_reader

Podrobnosti o oprávnění z těchto rolí najdete v části oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úlohy, které vlastní jiní uživatelé.

### <a name="spdeletetargetgroup"></a>sp_delete_target_group

Odstraní cílovou skupinu.

#### <a name="syntax"></a>Syntaxe


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argumenty
[  **@target_group_name =** ] 'target_group_name.  
Název cílové skupiny k odstranění. target_group_name je nvarchar(128) s žádná výchozí hodnota.

#### <a name="return-code-values"></a>Návratové hodnoty kódu
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Žádné.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení členové role pevného serveru sysadmin mohou spouštět tuto uloženou proceduru. Omezit přístup uživatele k právě nebudou moct sledování úloh, můžete udělit uživatelům být součástí následující role databáze v databázi agenta úlohy při vytváření úlohy agenta:
- jobs_reader

Podrobnosti o oprávnění z těchto rolí najdete v části oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úlohy, které vlastní jiní uživatelé.

### <a name="spaddtargetgroupmember"></a>sp_add_target_group_member

Přidá v databázi nebo skupině databází na cílovou skupinu.

#### <a name="syntax"></a>Syntaxe

```sql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] ‘membership_type’ ]   
        [ , [ @target_type = ] ‘target_type’ ]   
        [ , [ @refresh_credential_name = ] ‘refresh_credential_name’ ]   
        [ , [ @server_name = ] ‘server_name’ ]   
        [ , [ @database_name = ] ‘database_name’ ]   
        [ , [ @elastic_pool_name = ] ‘elastic_pool_name’ ]   
        [ , [ @shard_map_name = ] ‘shard_map_name’ ]   
        [ , [ @target_id = ] ‘target_id’ OUTPUT ]
```

#### <a name="arguments"></a>Argumenty
[  **@target_group_name =** ] 'target_group_name.  
Název cílové skupiny, ke kterému člen bude přidán. target_group_name je nvarchar(128) s žádná výchozí hodnota.

[  **@membership_type =** ] 'membership_type.  
Určuje, pokud bude člena cílové skupiny zahrnout nebo vyloučit. target_group_name je nvarchar(128) výchozí 'Zahrnout'. Platné hodnoty pro target_group_name jsou 'Zahrnout' nebo 'Vyloučit'.

[  **@target_type =** ] 'target_type.  
Typ cílová databáze nebo kolekce databáze, včetně všechny databáze na serveru, všechny databáze v Elastickém fondu, všechny databáze v mapování horizontálních nebo jednotlivé databáze. target_type je nvarchar(128) s žádná výchozí hodnota. Platné hodnoty pro target_type jsou 'SqlServer', 'SqlElasticPool', 'SqlDatabase' nebo 'SqlShardMap'. 

[  **@refresh_credential_name =** ] 'refresh_credential_name.  
Název logického serveru. refresh_credential_name je nvarchar(128) s žádná výchozí hodnota.

[  **@server_name =** ] "Název_serveru"  
Název logického serveru, který musí být přidaní do zadanou cílovou skupinu. název_serveru třeba zadat při target_type 'SqlServer'. název_serveru je nvarchar(128) s žádná výchozí hodnota.

[  **@database_name =** ] 'název_databáze.  
Název databáze, která musí být přidaní do zadanou cílovou skupinu. Název databáze database_name by měl zadat, když target_type je "SqlDatabase". Název databáze database_name je nvarchar(128) s žádná výchozí hodnota.

[  **@elastic_pool_name =** ] 'elastic_pool_name.  
Název elastického fondu, který musí být přidaní do zadanou cílovou skupinu. elastic_pool_name musí zadat, když je target_type 'SqlElasticPool'. elastic_pool_name je nvarchar(128) s žádná výchozí hodnota.

[  **@shard_map_name =** ] 'shard_map_name.  
Název fondu mapy horizontálního oddílu, který musí být přidaní do zadanou cílovou skupinu. elastic_pool_name musí zadat, když je target_type 'SqlSqlShardMap'. shard_map_name je nvarchar(128) s žádná výchozí hodnota.

[  **@target_id =** ] target_group_id výstup  
Cíl identifikační číslo přiřazené pro člena skupiny cíl, pokud vytvořili přidat k cílové skupině. target_id je proměnná výstup z typu uniqueidentifier, výchozí hodnota je NULL.
Návratový kód hodnoty 0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Spustí úlohu na všech databází v rámci serveru nebo elastického fondu v době spuštění, když logického serveru nebo elastického fondu je zahrnuta v cílové skupině.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení členové role pevného serveru sysadmin mohou spouštět tuto uloženou proceduru. Omezit přístup uživatele k právě nebudou moct sledování úloh, můžete udělit uživatelům být součástí následující role databáze v databázi agenta úlohy při vytváření úlohy agenta:
- jobs_reader

Podrobnosti o oprávnění z těchto rolí najdete v části oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úlohy, které vlastní jiní uživatelé.

#### <a name="examples"></a>Příklady
Následující příklad přidá všechny databáze v serverů Londýn a NewYork ke skupině serverů zachování údaje zákazníka. Je nutné připojit k databázi úlohy při vytváření úlohy agenta, v tomto případu ElasticJobs zadat.

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

### <a name="spdeletetargetgroupmember"></a>sp_delete_target_group_member

Odebere člena skupiny cíl cílovou skupinu.

#### <a name="syntax"></a>Syntaxe


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] ‘target_id’]
```



Argumenty [ @target_group_name =] 'target_group_name.  
Název cílové skupiny, ze které chcete odebrat člena cílové skupiny. target_group_name je nvarchar(128) s žádná výchozí hodnota.

[ @target_id =] target_id  
 Cíl identifikační číslo přiřazené člena skupiny cíl odeberou. target_id je uniqueidentifier, výchozí hodnota je NULL.

#### <a name="return-code-values"></a>Návratové hodnoty kódu
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Cílové skupiny poskytují snadný způsob, jak cíle úlohu v kolekci databází.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení členové role pevného serveru sysadmin mohou spouštět tuto uloženou proceduru. Omezit přístup uživatele k právě nebudou moct sledování úloh, můžete udělit uživatelům být součástí následující role databáze v databázi agenta úlohy při vytváření úlohy agenta:
- jobs_reader

Podrobnosti o oprávnění z těchto rolí najdete v části oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úlohy, které vlastní jiní uživatelé.

#### <a name="examples"></a>Příklady
Následující příklad odebrat ze skupiny informace o zákazníkovi údržba serverů Londýn serveru. Je nutné připojit k databázi úlohy při vytváření úlohy agenta, v tomto případu ElasticJobs zadat.

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

### <a name="sppurgejobhistory"></a>sp_purge_jobhistory

Odebere záznamy historie pro úlohu.

#### <a name="syntax"></a>Syntaxe


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumenty
[  **@job_name =** ] 'job_name.  
Název úlohy, pro kterou chcete odstranit záznamy historie. job_name je nvarchar(128), výchozí hodnota je NULL. Musí být zadán buď job_id nebo job_name, ale nelze zadat současně.

[  **@job_id =** ] job_id  
 Identifikační číslo úlohy úlohy pro záznamy, které chcete odstranit. job_id je uniqueidentifier, výchozí hodnota je NULL. Musí být zadán buď job_id nebo job_name, ale nelze zadat současně.

[  **@oldest_date =** ] oldest_date  
 Nejstaršího záznamu, které se uchovají v historii. oldest_date DATETIME2, je výchozí hodnota je NULL. Pokud je zadána oldest_date, sp_purge_jobhistory pouze odebere záznamy, které jsou starší než hodnota zadaná.

#### <a name="return-code-values"></a>Návratové hodnoty kódu
0 (úspěch) nebo 1 (chyba) poznámky cílové skupiny poskytují snadný způsob, jak cíle úlohu v kolekci databází.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení členové role pevného serveru sysadmin mohou spouštět tuto uloženou proceduru. Omezit přístup uživatele k právě nebudou moct sledování úloh, můžete udělit uživatelům být součástí následující role databáze v databázi agenta úlohy při vytváření úlohy agenta:
- jobs_reader

Podrobnosti o oprávnění z těchto rolí najdete v části oprávnění v tomto dokumentu. Pouze členové sysadmin můžete použít tuto uloženou proceduru upravit atributy úlohy, které vlastní jiní uživatelé.

#### <a name="examples"></a>Příklady
Následující příklad přidá všechny databáze v serverů Londýn a NewYork ke skupině serverů zachování údaje zákazníka. Je nutné připojit k databázi úlohy při vytváření úlohy agenta, v tomto případu ElasticJobs zadat.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Zobrazení úloh

Jsou k dispozici v následujících zobrazení [úlohy databáze](elastic-jobs-overview.md#job-database).


|Zobrazení  |Popis  |
|---------|---------|
|[jobs_executions](#jobsexecutions-view)     |  Zobrazuje historie provádění úlohy.      |
|[Úlohy](#jobs-view)     |   Zobrazí všechny úlohy.      |
|[job_versions](#jobversions-view)     |   Zobrazí všechny úlohy verze.      |
|[kroky úlohy](#jobsteps-view)     |     Zobrazí všechny kroky v aktuální verzi Každá úloha.    |
|[jobstep_versions](#jobstepversions-view)     |     Zobrazuje všechny kroky ve všech verzích Každá úloha.    |
|[target_groups](#targetgroups-view)     |      Zobrazí všechny cílové skupiny.   |
|[target_group_members](#targetgroups-view)     |   Obsahuje všechny členy všechny cílové skupiny.      |


### <a name="jobsexecutions-view"></a>jobs_executions zobrazení

[úlohy]. [jobs_executions]

Zobrazuje historie provádění úlohy.


|Název sloupce|   Typ dat   |Popis|
|---------|---------|---------|
|**job_execution_id**   |Typ UniqueIdentifier|  Jedinečné ID instance provádění úlohy.
|**job_name**   |Nvarchar(128)  |Název úlohy.
|**job_id** |Typ UniqueIdentifier|  Jedinečné ID úlohy.
|**job_version**    |celá čísla    |Verze úlohy (automaticky aktualizuje pokaždé, když je upravit úlohu).
|**step_id**    |celá čísla|   (Pro tuto úlohu) Jedinečný identifikátor pro krok. Hodnota NULL znamená, že to je provádění úlohy nadřazené.
|**is_active**| Bit |Určuje, zda informace je aktivní nebo neaktivní. 1 znamená aktivních úloh a 0 znamená neaktivní.
|**Životní cyklus**| Nvarchar(50)|Hodnotu, která určuje stav úlohy: 'Vytvořit', v průběhu, 'se nezdařilo., 'bylo úspěšné., 'Přeskočené', 'SucceededWithSkipped'|
|**create_time**|   datetime2(7)|   Datum a čas vytvoření úlohy.
|**start_time** |datetime2(7)|  Datum a čas úlohu spustil provádění. Hodnota NULL, pokud úloha ještě nebyla spuštěna.
|**end_time**|  datetime2(7)    |Datum a čas dokončení provádění úlohy. Hodnota NULL, pokud úloha ještě nebyla spuštěna, nebo nebylo dosud dokončil provádění.
|**current_attempts**   |celá čísla    |Počet přístupů, které se na krok opakovat. Nadřazené úloze bude 0, podřízené úlohy spuštěních bude 1 nebo vyšší na základě zpracování zásad.
|**current_attempt_start_time** |datetime2(7)|  Datum a čas úlohu spustil provádění. Hodnota NULL znamená, že to je provádění úlohy nadřazené.
|**last_message**   |nvarchar(max)| Zpráva historie úloh nebo krok. 
|**target_type**|   Nvarchar(128)   |Typ cílová databáze nebo kolekce databáze, včetně všech databází v serveru, všechny databáze v Elastickém fondu nebo v databázi. Platné hodnoty pro target_type jsou 'SqlServer', 'SqlElasticPool' nebo "SqlDatabase". Hodnota NULL znamená, že to je provádění úlohy nadřazené.
|**target_id**  |Typ UniqueIdentifier|  Jedinečné ID člena cílové skupiny.  Hodnota NULL znamená, že to je provádění úlohy nadřazené.
|**target_group_name**  |Nvarchar(128)  |Název cílové skupiny. Hodnota NULL znamená, že to je provádění úlohy nadřazené.
|**target_server_name**|    nvarchar(256)|  Název logického serveru obsažené v cílové skupině. Zadat, pokud je target_type 'SqlServer'. Hodnota NULL znamená, že to je provádění úlohy nadřazené.
|**target_database_name**   |Nvarchar(128)| Název databáze obsažené v cílové skupině. Zadat, pouze pokud je target_type "SqlDatabase". Hodnota NULL znamená, že to je provádění úlohy nadřazené.


### <a name="jobs-view"></a>v zobrazení úloh

[úlohy]. [úlohy]

Zobrazí všechny úlohy.

|Název sloupce|   Typ dat|  Popis|
|------|------|-------|
|**job_name**|  Nvarchar(128)   |Název úlohy.|
|**job_id**|    Typ UniqueIdentifier    |Jedinečné ID úlohy.|
|**job_version**    |celá čísla    |Verze úlohy (automaticky aktualizuje pokaždé, když je upravit úlohu).|
|**Popis**    |nvarchar(512)| Popis pro úlohu. povoleno bit označuje, zda je povoleno úlohy. 1 znamená povoleno úlohy a 0 znamená zakázáno úlohy.|
|**schedule_interval_type** |Nvarchar(50)   |Hodnotu, která určuje, kdy má být zpracována úlohu: 'jednou, 'minut., 'hodiny, 'dny, týdny., 'měsíců.
|**schedule_interval_count**|   celá čísla|    Počet období schedule_interval_type proběhnout mezi každé spuštění úlohy.|
|**schedule_start_time**    |datetime2(7)|  Datum a čas, kdy byl poslední bylo zahájeno spuštění úlohy.|
|**schedule_end_time**| datetime2(7)|   Datum a čas, kdy byl poslední dokončené provádění úlohy.|


### <a name="jobversions-view"></a>job_versions zobrazení

[úlohy]. [job_verions]

Zobrazí všechny úlohy verze.

|Název sloupce|   Typ dat|  Popis|
|------|------|-------|
|**job_name**|  Nvarchar(128)   |Název úlohy.|
|**job_id**|    Typ UniqueIdentifier    |Jedinečné ID úlohy.|
|**job_version**    |celá čísla    |Verze úlohy (automaticky aktualizuje pokaždé, když je upravit úlohu).|


### <a name="jobsteps-view"></a>Zobrazit kroky úlohy

[úlohy]. [kroky úlohy]

Zobrazí všechny kroky v aktuální verzi Každá úloha.

|Název sloupce    |Typ dat| Popis|
|------|------|-------|
|**job_name**   |Nvarchar(128)| Název úlohy.|
|**job_id** |Typ UniqueIdentifier   |Jedinečné ID úlohy.|
|**job_version**|   celá čísla|    Verze úlohy (automaticky aktualizuje pokaždé, když je upravit úlohu).|
|**step_id**    |celá čísla    |(Pro tuto úlohu) Jedinečný identifikátor pro krok.|
|**STEP_NAME**  |Nvarchar(128)  |(Pro tuto úlohu) jedinečný název pro krok.|
|**command_type**   |Nvarchar(50)   |Typ příkazu v kroku úlohy. Pro v1, hodnota musí být rovna k a výchozí hodnota je 'TSql'.|
|**command_source** |Nvarchar(50)|  Umístění příkazu. Pro v1 'vložené, je výchozí a pouze přijatá hodnota.|
|**příkaz**|   nvarchar(max)|  Příkazy, provádět elastické úlohy prostřednictvím command_type.|
|**credential_name**|   Nvarchar(128)   |Název oboru databáze přihlašovací údaje použité k provádění úlohy.|
|**target_group_name**| Nvarchar(128)   |Název cílové skupiny.|
|**target_group_id**|   Typ UniqueIdentifier|   Jedinečné ID cílové skupiny.|
|**initial_retry_interval_seconds**|    celá čísla |Zpoždění před první pokus o opakování. Výchozí hodnota je 1.|
|**maximum_retry_interval_seconds** |celá čísla|   Maximální prodleva mezi pokusy o opakování. Pokud prodleva mezi opakovanými pokusy by růst větší než tato hodnota, je je se na tuto hodnotu místo omezená. Výchozí hodnota je 120.|
|**retry_interval_backoff_multiplier**  |skutečné|  Násobitel chcete použít pro zpoždění opakování, pokud úlohy několika krok provádění pokusů o selže. Výchozí hodnota je 2.0.|
|**retry_attempts** |celá čísla|   Počet opakování pokusů má použít, pokud tento krok nezdaří. Výchozí hodnotu 10, který označuje počet pokusů o opakování.|
|**step_timeout_seconds**   |celá čísla|   Množství času v minutách mezi pokusy o opakování. Výchozí hodnota je 0, který označuje intervalu 0 minut.|
|**output_type**    |nvarchar(11)|  Umístění příkazu. V aktuální verzi preview 'vložené, je výchozí a pouze přijatá hodnota.|
|**output_credential_name**|    Nvarchar(128)   |Nastavit název přihlašovacích údajů, který se má použít pro připojení k cílovému serveru můžete ukládat výsledky.|
|**output_subscription_id**|    Typ UniqueIdentifier|   Jedinečné ID předplatného cílového server\database výsledků nastavit od spuštění dotazu.|
|**output_resource_group_name** |Nvarchar(128)| Název skupiny prostředků, ve kterém se nachází na cílový server.|
|**output_server_name**|    nvarchar(256)   |Název cílového serveru pro sadu výsledků.|
|**output_database_name**   |Nvarchar(128)| Název cílové databáze pro sadu výsledků.|
|**output_schema_name** |nvarchar(max)| Název cílové schéma. Výchozí hodnota je dbo, není-li zadána.|
|**output_table_name**| nvarchar(max)|  Název tabulky můžete ukládat výsledky nastavit z výsledků dotazu. Tabulka bude vytvořena automaticky v závislosti na schématu výsledky nastavit, pokud ještě neexistuje. Schéma musí odpovídat schématu sady výsledků.|
|**max_parallelism**|   celá čísla|    Maximální počet databází pro elastický fond, který se má spustit krok úlohy najednou. Výchozí hodnota je NULL, což znamená, není nijak omezena. |


### <a name="jobstepversions-view"></a>jobstep_versions zobrazení

[úlohy]. [jobstep_versions]

Zobrazuje všechny kroky ve všech verzích Každá úloha. Schéma je stejný jako [kroky úlohy](#jobsteps-view).

### <a name="targetgroups-view"></a>target_groups zobrazení

[úlohy]. [target_groups]

Zobrazí všechny cílové skupiny.

|Název sloupce|Typ dat| Popis|
|-----|-----|-----|
|**target_group_name**| Nvarchar(128)   |Název cílové skupiny, kolekce databáze. 
|**target_group_id**    |Typ UniqueIdentifier   |Jedinečné ID cílové skupiny.

### <a name="targetgroupsmembers-view"></a>target_groups_members zobrazení

[úlohy]. [target_groups_members]

Obsahuje všechny členy všechny cílové skupiny.

|Název sloupce|Typ dat| Popis|
|-----|-----|-----|
|**target_group_name**  |nvarchar (128|Název cílové skupiny, kolekce databáze. |
|**target_group_id**    |Typ UniqueIdentifier   |Jedinečné ID cílové skupiny.|
|**membership_type**    |celá čísla|   Určuje, pokud je cílový člena skupiny zahrnout nebo vyloučit v cílové skupině. Platné hodnoty pro target_group_name jsou 'Zahrnout' nebo 'Vyloučit'.|
|**target_type**    |Nvarchar(128)| Typ cílová databáze nebo kolekce databáze, včetně všech databází v serveru, všechny databáze v Elastickém fondu nebo v databázi. Platné hodnoty pro target_type jsou 'SqlServer', 'SqlElasticPool', 'SqlDatabase' nebo 'SqlShardMap'.|
|**target_id**  |Typ UniqueIdentifier|  Jedinečné ID člena cílové skupiny.|
|**refresh_credential_name**    |Nvarchar(128)  |Název databáze obor pověření použitá pro připojení k cílové člena skupiny.|
|**subscription_id**    |Typ UniqueIdentifier|  Jedinečné ID předplatného.|
|**resource_group_name**    |Nvarchar(128)| Název skupiny prostředků, ve kterém se nachází cílový člena skupiny.|
|**název_serveru**    |Nvarchar(128)  |Název logického serveru obsažené v cílové skupině. Zadat, pokud je target_type 'SqlServer'. |
|**Název databáze database_name**  |Nvarchar(128)  |Název databáze obsažené v cílové skupině. Zadat, pouze pokud je target_type "SqlDatabase".|
|**elastic_pool_name**  |Nvarchar(128)| Název elastického fondu obsažené v cílové skupině. Zadat, pouze pokud je target_type 'SqlElasticPool'.|
|**shard_map_name** |Nvarchar(128)| Název mapy horizontálního oddílu obsažené v cílové skupině. Zadat, pouze pokud je target_type 'SqlShardMap'.|


## <a name="resources"></a>Zdroje a prostředky

 - ![Ikona odkaz tématu](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "ikonu propojení tématu") [konvence syntaxe Transact-SQL](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  


## <a name="next-steps"></a>Další postup

- [Vytvářet a spravovat elastické úlohy pomocí prostředí PowerShell](elastic-jobs-powershell.md)
- [Autorizace a oprávnění systému SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
  