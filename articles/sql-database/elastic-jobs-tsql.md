---
title: Vytvoření a správa Azure SQL úlohy Elastic Database pomocí jazyka Transact-SQL (T-SQL) | Dokumentace Microsoftu
description: Spusťte skripty v rozsáhlé skupině databází se agent Elastických úloh databáze pomocí příkazů jazyka Transact-SQL (T-SQL).
services: sql-database
author: jaredmoo
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 06/14/2018
ms.author: jaredmoo
ms.openlocfilehash: ae5dafcebd50ecd22309a7771b0edf01a97fd7a7
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "43842597"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Použití příkazů jazyka Transact-SQL (T-SQL) k vytvoření a správa Elastických úloh databáze

Tento článek obsahuje mnoho ukázkové scénáře, abyste mohli začít pracovat s Elastickými úlohami pomocí jazyka T-SQL.

V příkladech se používá [uložených procedur komponentami TableAdapter](#job-stored-procedures) a [zobrazení](#job-views) k dispozici v [ *databáze úloh*](elastic-jobs-overview.md#job-database).

Příkaz Transact-SQL (T-SQL) slouží k vytváření, konfiguraci, spuštění a Správa úloh. Vytvoření agenta Elastických úloh není podporováno v T-SQL, proto je nutné nejprve vytvořit *agenta Elastických úloh* pomocí portálu, nebo [Powershellu](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Vytvoření pověření pro provádění úloh

Přihlašovací údaje, které se používá pro připojení k vaší cílové databáze pro spuštění skriptu. Přihlašovací údaje, které musí mít příslušná oprávnění pro databáze určená cílová skupina úspěšně spustit skript. Při použití serveru a/nebo člena skupiny cílový fond, důrazně doporučujeme, abyste vytvořit hlavní pověření pro použití k aktualizaci přihlašovacích údajů před rozšíření serveru a/nebo fond v době provádění úlohy. Přihlašovací údaje s rozsahem databáze se vytvoří na databáze agenta úloh. Musí být stejné přihlašovací údaje, které slouží k *Vytvořte přihlašovací údaje* a *vytvořit uživatele z přihlášení k udělení oprávnění databáze přihlášení* na cílové databáze.


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

Následující příklad ukazuje, jak spustit úlohu proti všem databázím na serveru.  
Připojte se k [ *databáze úloh* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:


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


## <a name="exclude-a-single-database"></a>Vyloučit izolované databáze

Následující příklad ukazuje, jak spustit úlohu proti všem databázím na serveru, s výjimkou databázi s názvem *MappingDB*.  
Připojte se k [ *databáze úloh* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

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

Následující příklad ukazuje, jak cílit na všechny databáze v jedné nebo víc elastických fondů.  
Připojte se k [ *databáze úloh* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

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


## <a name="deploy-new-schema-to-many-databases"></a>Nasazení nové schéma pro velký počet databází

Následující příklad ukazuje, jak nasadit nové schéma pro všechny databáze.  
Připojte se k [ *databáze úloh* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:


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

V mnoha scénářích shromažďování dat může být užitečné zahrnout některé z těchto proměnných skriptovací umožňující výsledky úlohy následného zpracování.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Například skupinu pro všechny výsledky z společně provedení stejné úlohy, použijte *$(job_execution_id)* jak je znázorněno v následujícím příkazu:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Monitorování výkonu databáze

Následující příklad vytvoří novou úlohu ke shromažďování dat výkonu z několika databází.

Ve výchozím nastavení bude vypadat agent úlohy k vytvoření této tabulky můžete ukládat výsledky vrácené v. Díky tomu přihlašovací jméno přidružené k pověření použité pro přihlašovací údaje, které výstup muset mít dostatečná oprávnění k provedení této. Pokud chcete ručně vytvořit tabulku předem je potřeba mít následující vlastnosti:
1. Sloupce se správným názvem a datové typy pro sadu výsledků dotazu.
2. Další sloupec pro internal_execution_id s datovým typem uniqueidentifier.
3. Neclusterovaný index s názvem "IX_<TableName>_Internal_Execution_ID" ve sloupci internal_execution_id.

Připojte se k [ *databáze úloh* ](elastic-jobs-overview.md#job-database) a spusťte následující příkazy:

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


## <a name="view-job-definitions"></a>Definice zobrazení úloh

Následující příklad ukazuje, jak zobrazit aktuální definice úlohy.  
Připojte se k [ *databáze úloh* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

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


## <a name="begin-ad-hoc-execution-of-a-job"></a>Zahájit provádění ad-hoc úlohy

Následující příklad ukazuje, jak chcete okamžitě spustit úlohu.  
Připojte se k [ *databáze úloh* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

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


## <a name="schedule-execution-of-a-job"></a>Plánování spouštění úlohy

Následující příklad ukazuje, jak naplánovat úlohu pro budoucí spouštění.  
Připojte se k [ *databáze úloh* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Stav provádění úlohy monitorování

Následující příklad ukazuje, jak chcete-li zobrazit podrobnosti o stavu spuštění pro všechny úlohy.  
Připojte se k [ *databáze úloh* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

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
Připojte se k [ *databáze úloh* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

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

Následující příklad ukazuje, jak odstranit historii úloh před k určitému datu.  
Připojte se k [ *databáze úloh* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Odstranění úlohy a všechny jeho historie úloh

Následující příklad ukazuje, jak odstranit úlohu a všechny související historie úlohy.  
Připojte se k [ *databáze úloh* ](elastic-jobs-overview.md#job-database) a spusťte následující příkaz:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Úloha uložené procedury

Následující uložené procedury jsou v [úloh databáze](elastic-jobs-overview.md#job-database).



|Uložená procedura  |Popis  |
|---------|---------|
|[sp_add_job](#spaddjob)     |     Přidá novou úlohu.    |
|[sp_update_job ](#spupdatejob)    |      Aktualizuje existující úlohy.   |
|[sp_delete_job](#spdeletejob)     |      Odstraní existující úlohy.   |
|[sp_add_jobstep](#spaddjobstep)    |    Přidá nový krok do úlohy.     |
|[sp_update_jobstep](#spupdatejobstep)     |     Aktualizuje krok úlohy.    |
|[sp_delete_jobstep](#spdeletejobstep)     |     Odstraní krok úlohy.    |
|[sp_start_job](#spstartjob)    |  Začne provádět úlohy.       |
|[sp_stop_job](#spstopjob)     |     Zastaví provádění úlohy.   |
|[sp_add_target_group](#spaddtargetgroup)    |     Přidá cílovou skupinu.    |
|[sp_delete_target_group](#spdeletetargetgroup)     |    Odstraní cílovou skupinu.     |
|[sp_add_target_group_member](#spaddtargetgroupmember)     |    Přidá do skupiny cílové databázi nebo skupině databází.     |
|[sp_delete_target_group_member](#spdeletetargetgroupmember)     |     Odebere z cílová skupina členem cílové skupiny.    |
|[sp_purge_jobhistory ](#sppurgejobhistory)    |    Odebere záznamy historie úlohy.     |





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

[  **@job_name =** ] "hodnotu job_name.  
Název úlohy. Název musí být jedinečné a nesmí obsahovat znak procenta (%). hodnotu job_name je nvarchar(128) bez výchozí hodnoty.

[  **@description =** ] "popisu"  
Popis úlohy. Popis je nvarchar(512), výchozí hodnota je NULL. Pokud popis je vynechán, použije se prázdný řetězec.

[  **@enabled =** ] povolená  
Plán úloh Určuje, zda je povoleno. Povolena je bit, výchozí hodnota je 0 (zakázáno). Pokud je 0, úloha není povoleno a nespustí podle zadaného plánu; Nicméně jej lze spustit ručně. Pokud 1, úloha se spustí podle zadaného plánu a můžete spustit také ručně.

[  **@schedule_interval_type =**] schedule_interval_type  
Hodnota znamená, když je úloha má být proveden. schedule_interval_type je nvarchar(50), výchozí hodnota je jednou a může být jedna z následujících hodnot:
- Jednou,
- 'Minuty'
- 'Hodiny'
- "Days",
- "Týdnů.
- "Měsíců.

[  **@schedule_interval_count =** ] schedule_interval_count  
Počet období schedule_interval_count ke kterým dochází mezi každé spuštění úlohy. schedule_interval_count je int, výchozí hodnota je 1. Hodnota musí být větší než nebo rovno 1.

[  **@schedule_start_time =** ] schedule_start_time  
Datum, na jaké úlohy můžete začít spuštění. schedule_start_time DATETIME2, je výchozí 00:00:00.0000000 0001-01-01.

[  **@schedule_end_time =** ] schedule_end_time  
Datum, které úlohy můžete zastavit provádění. schedule_end_time DATETIME2, je výchozí 9999-12-31 11:59:59.0000000. 

[  **@job_id =** ] parametrem job_id výstupu  
Identifikační číslo úlohy přiřazených k úloze, pokud byl úspěšně vytvořen. parametrem job_id je proměnná výstupní typ uniqueidentifier.

#### <a name="return-code-values"></a>Hodnoty návratový kód

0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
sp_add_job se musí spouštět z databáze agenta úloh při vytvoření úlohy agenta.
Po přidání úloha spustila sp_add_job, sp_add_jobstep je možné přidat kroky, které provádějí činnosti úlohy. Číslo úlohy počáteční verze je 0, což se zvýší na 1 při prvním krokem je přidání.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení, členové role pevného serveru správce sysadmin systému tuto uloženou proceduru mohou spustit. Omezit přístup jenom uživatel moct monitorovat úlohy, můžete udělit uživatele, aby byla součástí následující role databáze v databáze agenta úloh při vytvoření agenta úlohy:

- jobs_reader

Podrobnosti o oprávněních pracovníci v těchto rolích najdete v části oprávnění v tomto dokumentu. Jenom členové skupiny sysadmin můžete upravit vlastnosti úlohy, které vlastní jiní uživatelé tuto uloženou proceduru.

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
[  **@job_name =** ] "hodnotu job_name.  
Název úlohy aktualizovat. hodnotu job_name je nvarchar(128).

[  **@new_name =** ] "Nový_název.  
Nový název úlohy. nový_název je nvarchar(128).

[  **@description =** ] "popisu"  
Popis úlohy. Popis je nvarchar(512).

[  **@enabled =** ] povolená  
Určuje, jestli plán úloh je povoleno (1) nebo nejsou povolené (0). Povolena je bit.

[  **@schedule_interval_type=** ] schedule_interval_type  
Hodnota znamená, když je úloha má být proveden. schedule_interval_type je nvarchar(50) a může být jedna z následujících hodnot:

- Jednou,
- 'Minuty'
- 'Hodiny'
- "Days",
- "Týdnů.
- "Měsíců.

[  **@schedule_interval_count=** ] schedule_interval_count  
Počet období schedule_interval_count ke kterým dochází mezi každé spuštění úlohy. schedule_interval_count je int, výchozí hodnota je 1. Hodnota musí být větší než nebo rovno 1.

[  **@schedule_start_time=** ] schedule_start_time  
Datum, na jaké úlohy můžete začít spuštění. schedule_start_time DATETIME2, je výchozí 00:00:00.0000000 0001-01-01.

[  **@schedule_end_time=** ] schedule_end_time  
Datum, které úlohy můžete zastavit provádění. schedule_end_time DATETIME2, je výchozí 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Hodnoty návratový kód
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Po přidání úloha spustila sp_add_job, sp_add_jobstep je možné přidat kroky, které provádějí činnosti úlohy. Číslo úlohy počáteční verze je 0, což se zvýší na 1 při prvním krokem je přidání.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení, členové role pevného serveru správce sysadmin systému tuto uloženou proceduru mohou spustit. Omezit přístup jenom uživatel moct monitorovat úlohy, můžete udělit uživatele, aby byla součástí následující role databáze v databáze agenta úloh při vytvoření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních pracovníci v těchto rolích najdete v části oprávnění v tomto dokumentu. Jenom členové skupiny sysadmin můžete upravit vlastnosti úlohy, které vlastní jiní uživatelé tuto uloženou proceduru.



### <a name="spdeletejob"></a>sp_delete_job

Odstraní existující úlohy.

#### <a name="syntax"></a>Syntaxe

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumenty
[  **@job_name =** ] "hodnotu job_name.  
Název úlohy, která se má odstranit. hodnotu job_name je nvarchar(128).

[  **@force =** ] vynutit  
Určuje, jestli se má odstranit, pokud úloha nemá žádné spuštění v průběhu a zrušení všech probíhajících spuštění (1) nebo převzetí služeb při, pokud všechny prováděné úlohy jsou v průběhu (0). platnost je bit.

#### <a name="return-code-values"></a>Hodnoty návratový kód
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Historie úlohy automaticky odstraní při odstranění úlohy.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení, členové role pevného serveru správce sysadmin systému tuto uloženou proceduru mohou spustit. Omezit přístup jenom uživatel moct monitorovat úlohy, můžete udělit uživatele, aby byla součástí následující role databáze v databáze agenta úloh při vytvoření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních pracovníci v těchto rolích najdete v části oprávnění v tomto dokumentu. Jenom členové skupiny sysadmin můžete upravit vlastnosti úlohy, které vlastní jiní uživatelé tuto uloženou proceduru.



### <a name="spaddjobstep"></a>sp_add_jobstep

Přidá nový krok do úlohy.

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

[  **@job_name =** ] "hodnotu job_name.  
Název úlohy, ke kterému chcete přidat krok. hodnotu job_name je nvarchar(128).

[  **@step_id =** ] step_id  
Identifikační číslo sekvence pro krok úlohy. Krok identifikační čísla začínají znakem 1 a zvýšit bez mezer. Pokud toto id již existující krok, pak krok a všechny další kroky se budou mít jejich id uživatele zvýší tak, aby tento nový krok může být vložen do sekvence. Pokud není zadán, step_id automaticky přiřadí na poslední v pořadí kroků. step_id je celé číslo

[  **@step_name =** ] step_name  
Název kroku. Musí být zadána, s výjimkou první krok úlohy, která má výchozí název 'JobStep' (pro usnadnění práce). STEP_NAME je nvarchar(128).

[  **@command_type =** ] "command_type.  
Typ příkazu, který provádí tento krok úlohy. command_type je nvarchar(50) s výchozí hodnotou TSql, to znamená, že hodnota @command_type parametr je skript T-SQL.

Je-li zadána, hodnota musí být TSql.

[  **@command_source =** ] "command_source.  
Typ umístění, kde je uložen příkazu. command_source je nvarchar(50) s výchozí hodnotou vloženého, to znamená, že hodnota @command_source parametr je literál text příkazu.

Je-li zadána, hodnota musí být vložená.

[  **@command =** ] 'command'  
Příkaz musí být platný skript T-SQL a pak provádí tento krok úlohy. příkaz je nvarchar(max), výchozí hodnota je NULL.

[  **@credential_name =** ] "credential_name.  
Název databáze s rozsahem přihlašovací údaje uložené v této databázi řízení úloh, který se používá pro připojení ke každé z cílové databáze v cílové skupině při spuštění tohoto kroku. credential_name je nvarchar(128).

[  **@target_group_name =** ] "target-group_name.  
Název cílové skupiny, která obsahuje cílové databáze, které se spustí krok úlohy na. target_group_name je nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Zpoždění před prvním opakováním pokus selže při pokusu o provedení počáteční krok úlohy. initial_retry_interval_seconds je int, s výchozí hodnotou 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maximální zpoždění mezi opakovanými pokusy. Pokud by růst větší než tato hodnota zpoždění mezi opakovanými pokusy, to je se na tuto hodnotu místo toho uzavřeny. maximum_retry_interval_seconds je int, s výchozí hodnotou 120.

[  **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Násobitel použít na zpoždění opakování, pokud více úloh kroku spuštění pokusí selhání. Například pokud prvním opakováním měli trvat 5 sekund a omezení rychlosti multiplikátor je 2.0, pak druhý opakování budou mít ke zpoždění 10 sekund a třetí opakování budou mít ke zpoždění 20 sekund. retry_interval_backoff_multiplier je skutečný s výchozí hodnotou 2.0.

[  **@retry_attempts =** ] retry_attempts  
Počet pokusů o zopakování provedení počáteční pokus nebude úspěšný. Například pokud je hodnota retry_attempts 10, pak bude 1 počáteční pokus a 10 pokusy o opakování, poskytuje celkový počet pokusů o zadání 11. Pokud se poslední opakovaný pokus nezdaří, bude ukončen provádění úlohy se životního cyklu se nezdařilo. retry_attempts je int, s výchozí hodnotou 10.

[  **@step_timeout_seconds =** ] step_timeout_seconds  
Maximální množství času kroku ke spuštění. Pokud dojde k překročení této doby bude ukončen provádění úlohy s životního cyklu vypršel časový limit. step_timeout_seconds je int, s výchozí hodnotou 43 200 sekund (12 hodin).

[  **@output_type =** ] "output_type.  
Pokud není null, typ cílového umístění, které sady výsledků dotazu prvním příkazu je zapsán do. output_type je nvarchar(50), výchozí hodnota je NULL.

Je-li zadána, hodnota musí být SQL Database.

[  **@output_credential_name =** ] "output_credential_name.  
Pokud není null, název databáze s rozsahem přihlašovací údaj, který se používá pro připojení k cílové databázi výstup. Je třeba zadat Pokud output_type rovná SQL Database. output_credential_name je nvarchar(128) s výchozí hodnotou Null.

[  **@output_subscription_id =** ] "output_subscription_id.  
Popis musí.

[  **@output_resource_group_name =** ] "output_resource_group_name.  
Popis musí.

[  **@output_server_name =** ] "output_server_name.  
Pokud není null, plně kvalifikovaný název DNS serveru, který obsahuje výstup cílové databázi. Je třeba zadat Pokud output_type rovná SQL Database. output_server_name je nvarchar(256), výchozí hodnota je NULL.

[  **@output_database_name =** ] "output_database_name.  
Pokud není null, název databáze, která obsahuje výstupní cílové tabulky. Je třeba zadat Pokud output_type rovná SQL Database. output_database_name je nvarchar(128), výchozí hodnota je NULL.

[  **@output_schema_name =** ] "output_schema_name.  
Pokud není null, název schématu SQL, který obsahuje výstup cílové tabulky. Pokud output_type shodná s SQL Database, výchozí hodnota je dbo. output_schema_name je nvarchar(128).

[  **@output_table_name =** ] "output_table_name.  
Pokud není null, název tabulky, která nastavit první výsledek příkazu se zapíšou do. Pokud v tabulce ještě neexistuje, vytvoří se závislosti na schématu vrací sadu výsledků. Je třeba zadat Pokud output_type rovná SQL Database. output_table_name je nvarchar(128) s výchozí hodnotou Null.

[  **@job_version =** ] job_version výstupu  
Výstupní parametr, který se přiřadí nové číslo verze úlohy. job_version je int.

[  **@max_parallelism =** ] max_parallelism výstupu  
Maximální úroveň paralelismus za elastický fond. Pokud sadu a pak krok úlohy bude omezený spustit jenom na maximum, které velký počet databází na elastický fond. To platí pro každý elastický fond, který je buď přímo součástí cílové skupiny nebo se nachází uvnitř serveru, který je součástí cílové skupiny. max_parallelism je int.


#### <a name="return-code-values"></a>Hodnoty návratový kód
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Po úspěšném sp_add_jobstep se zvýší, aktuální číslo verze úlohy. Při příštím spuštění úlohy se použije na novou verzi. Pokud úloha právě probíhá, tohoto spuštění nebude obsahovat nový krok.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení, členové role pevného serveru správce sysadmin systému tuto uloženou proceduru mohou spustit. Omezit přístup jenom uživatel moct monitorovat úlohy, můžete udělit uživatele, aby byla součástí následující role databáze v databáze agenta úloh při vytvoření agenta úlohy:  

- jobs_reader

Podrobnosti o oprávněních pracovníci v těchto rolích najdete v části oprávnění v tomto dokumentu. Jenom členové skupiny sysadmin můžete upravit vlastnosti úlohy, které vlastní jiní uživatelé tuto uloženou proceduru.



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
[  **@job_name =** ] "hodnotu job_name.  
Název úlohy, ke kterému patří kroku. hodnotu job_name je nvarchar(128).

[  **@step_id =** ] step_id  
Identifikační číslo pro krok úlohy, které má být upraven. Musí být zadán buď step_id nebo step_name. step_id je celé číslo

[  **@step_name =** ] "step_name.  
Název kroku změnit. Musí být zadán buď step_id nebo step_name. STEP_NAME je nvarchar(128).

[  **@new_id =** ] new_id  
Nové identifikační číslo sekvence pro krok úlohy. Krok identifikační čísla začínají znakem 1 a zvýšit bez mezer. Pokud je krok v pořadí změníte, pak ostatní kroky budou automaticky označuje.

[  **@new_name =** ] "Nový_název.  
Nový název kroku. nový_název je nvarchar(128).

[  **@command_type =** ] "command_type.  
Typ příkazu, který provádí tento krok úlohy. command_type je nvarchar(50) s výchozí hodnotou TSql, to znamená, že hodnota @command_type parametr je skript T-SQL.

Je-li zadána, hodnota musí být TSql.

[  **@command_source =** ] "command_source.  
Typ umístění, kde je uložen příkazu. command_source je nvarchar(50) s výchozí hodnotou vloženého, to znamená, že hodnota @command_source parametr je literál text příkazu.

Je-li zadána, hodnota musí být vložená.

[  **@command =** ] 'command'  
Příkazy musí být platný skript T-SQL a pak provádí tento krok úlohy. příkaz je nvarchar(max), výchozí hodnota je NULL.

[  **@credential_name =** ] "credential_name.  
Název databáze s rozsahem přihlašovací údaje uložené v této databázi řízení úloh, který se používá pro připojení ke každé z cílové databáze v cílové skupině při spuštění tohoto kroku. credential_name je nvarchar(128).

[  **@target_group_name =** ] "target-group_name.  
Název cílové skupiny, která obsahuje cílové databáze, které se spustí krok úlohy na. target_group_name je nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Zpoždění před prvním opakováním pokus selže při pokusu o provedení počáteční krok úlohy. initial_retry_interval_seconds je int, s výchozí hodnotou 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maximální zpoždění mezi opakovanými pokusy. Pokud by růst větší než tato hodnota zpoždění mezi opakovanými pokusy, to je se na tuto hodnotu místo toho uzavřeny. maximum_retry_interval_seconds je int, s výchozí hodnotou 120.

[  **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Násobitel použít na zpoždění opakování, pokud více úloh kroku spuštění pokusí selhání. Například pokud prvním opakováním měli trvat 5 sekund a omezení rychlosti multiplikátor je 2.0, pak druhý opakování budou mít ke zpoždění 10 sekund a třetí opakování budou mít ke zpoždění 20 sekund. retry_interval_backoff_multiplier je skutečný s výchozí hodnotou 2.0.

[  **@retry_attempts =** ] retry_attempts  
Počet pokusů o zopakování provedení počáteční pokus nebude úspěšný. Například pokud je hodnota retry_attempts 10, pak bude 1 počáteční pokus a 10 pokusy o opakování, poskytuje celkový počet pokusů o zadání 11. Pokud se poslední opakovaný pokus nezdaří, bude ukončen provádění úlohy se životního cyklu se nezdařilo. retry_attempts je int, s výchozí hodnotou 10.

[  **@step_timeout_seconds =** ] step_timeout_seconds  
Maximální množství času kroku ke spuštění. Pokud dojde k překročení této doby bude ukončen provádění úlohy s životního cyklu vypršel časový limit. step_timeout_seconds je int, s výchozí hodnotou 43 200 sekund (12 hodin).

[  **@output_type =** ] "output_type.  
Pokud není null, typ cílového umístění, které sady výsledků dotazu prvním příkazu je zapsán do. Resetovat hodnotu output_type zpět na hodnotu NULL, nastavte hodnotu tohoto parametru na "(prázdný řetězec). output_type je nvarchar(50), výchozí hodnota je NULL.

Je-li zadána, hodnota musí být SQL Database.

[  **@output_credential_name =** ] "output_credential_name.  
Pokud není null, název databáze s rozsahem přihlašovací údaj, který se používá pro připojení k cílové databázi výstup. Je třeba zadat Pokud output_type rovná SQL Database. Resetovat hodnotu output_credential_name zpět na hodnotu NULL, nastavte hodnotu tohoto parametru na "(prázdný řetězec). output_credential_name je nvarchar(128) s výchozí hodnotou Null.

[  **@output_server_name =** ] "output_server_name.  
Pokud není null, plně kvalifikovaný název DNS serveru, který obsahuje výstup cílové databázi. Je třeba zadat Pokud output_type rovná SQL Database. Resetovat hodnotu output_server_name zpět na hodnotu NULL, nastavte hodnotu tohoto parametru na "(prázdný řetězec). output_server_name je nvarchar(256), výchozí hodnota je NULL.

[  **@output_database_name =** ] "output_database_name.  
Pokud není null, název databáze, která obsahuje výstupní cílové tabulky. Je třeba zadat Pokud output_type rovná SQL Database. Resetovat hodnotu output_database_name zpět na hodnotu NULL, nastavte hodnotu tohoto parametru na "(prázdný řetězec). output_database_name je nvarchar(128), výchozí hodnota je NULL.

[  **@output_schema_name =** ] "output_schema_name.  
Pokud není null, název schématu SQL, který obsahuje výstup cílové tabulky. Pokud output_type shodná s SQL Database, výchozí hodnota je dbo. Resetovat hodnotu output_schema_name zpět na hodnotu NULL, nastavte hodnotu tohoto parametru na "(prázdný řetězec). output_schema_name je nvarchar(128).

[  **@output_table_name =** ] "output_table_name.  
Pokud není null, název tabulky, která nastavit první výsledek příkazu se zapíšou do. Pokud v tabulce ještě neexistuje, vytvoří se závislosti na schématu vrací sadu výsledků. Je třeba zadat Pokud output_type rovná SQL Database. Resetovat hodnotu output_server_name zpět na hodnotu NULL, nastavte hodnotu tohoto parametru na "(prázdný řetězec). output_table_name je nvarchar(128) s výchozí hodnotou Null.

[  **@job_version =** ] job_version výstupu  
Výstupní parametr, který se přiřadí nové číslo verze úlohy. job_version je int.

[  **@max_parallelism =** ] max_parallelism výstupu  
Maximální úroveň paralelismus za elastický fond. Pokud sadu a pak krok úlohy bude omezený spustit jenom na maximum, které velký počet databází na elastický fond. To platí pro každý elastický fond, který je buď přímo součástí cílové skupiny nebo se nachází uvnitř serveru, který je součástí cílové skupiny. Resetovat hodnotu max_parallelism zpět na hodnotu null, nastavte hodnotu tohoto parametru na hodnotu -1. max_parallelism je int.


#### <a name="return-code-values"></a>Hodnoty návratový kód
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Tyto zásady neovlivní žádné v průběhu provádění úlohy. Po úspěšném sp_update_jobstep se zvýší číslo verze úlohy. Při příštím spuštění úlohy se použije na novou verzi.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení, členové role pevného serveru správce sysadmin systému tuto uloženou proceduru mohou spustit. Omezit přístup jenom uživatel moct monitorovat úlohy, můžete udělit uživatele, aby byla součástí následující role databáze v databáze agenta úloh při vytvoření agenta úlohy:

- jobs_reader

Podrobnosti o oprávněních pracovníci v těchto rolích najdete v části oprávnění v tomto dokumentu. Jenom členové skupiny sysadmin můžete použít tuto uloženou proceduru upravit atributy úlohy, které vlastní jiní uživatelé




### <a name="spdeletejobstep"></a>sp_delete_jobstep

Krok úlohy zruší úlohu.

#### <a name="syntax"></a>Syntaxe


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumenty
[  **@job_name =** ] "hodnotu job_name.  
Název úlohy, ze kterého se krok se odebere. hodnotu job_name je nvarchar(128) bez výchozí hodnoty.

[  **@step_id =** ] step_id  
Identifikační číslo pro krok úlohy odstranění. Musí být zadán buď step_id nebo step_name. step_id je celé číslo

[  **@step_name =** ] "step_name.  
Název kroku, která se má odstranit. Musí být zadán buď step_id nebo step_name. STEP_NAME je nvarchar(128).

[  **@job_version =** ] job_version výstupu  
Výstupní parametr, který se přiřadí nové číslo verze úlohy. job_version je int.

#### <a name="return-code-values"></a>Hodnoty návratový kód
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Tyto zásady neovlivní žádné v průběhu provádění úlohy. Po úspěšném sp_update_jobstep se zvýší číslo verze úlohy. Při příštím spuštění úlohy se použije na novou verzi.

Další kroky úlohy budou automaticky označuje k vyplnění mezer doleva o krok odstraněné úlohy.
 
#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení, členové role pevného serveru správce sysadmin systému tuto uloženou proceduru mohou spustit. Omezit přístup jenom uživatel moct monitorovat úlohy, můžete udělit uživatele, aby byla součástí následující role databáze v databáze agenta úloh při vytvoření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních pracovníci v těchto rolích najdete v části oprávnění v tomto dokumentu. Jenom členové skupiny sysadmin můžete upravit vlastnosti úlohy, které vlastní jiní uživatelé tuto uloženou proceduru.






### <a name="spstartjob"></a>sp_start_job

Začne provádět úlohy.

#### <a name="syntax"></a>Syntaxe


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argumenty
[  **@job_name =** ] "hodnotu job_name.  
Název úlohy, ze kterého se krok se odebere. hodnotu job_name je nvarchar(128) bez výchozí hodnoty.

[  **@job_execution_id =** ] job_execution_id výstupu  
Výstupní parametr, který se přiřadí id spuštění úlohy. job_version je typ uniqueidentifier.

#### <a name="return-code-values"></a>Hodnoty návratový kód
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Žádné.
 
#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení, členové role pevného serveru správce sysadmin systému tuto uloženou proceduru mohou spustit. Omezit přístup jenom uživatel moct monitorovat úlohy, můžete udělit uživatele, aby byla součástí následující role databáze v databáze agenta úloh při vytvoření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních pracovníci v těchto rolích najdete v části oprávnění v tomto dokumentu. Jenom členové skupiny sysadmin můžete upravit vlastnosti úlohy, které vlastní jiní uživatelé tuto uloženou proceduru.

### <a name="spstopjob"></a>sp_stop_job

Zastaví provádění úlohy.

#### <a name="syntax"></a>Syntaxe


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argumenty
[  **@job_execution_id =** ] job_execution_id  
Identifikační číslo provádění úlohy zastavení. job_execution_id je uniqueidentifier s výchozí hodnotou NULL.

#### <a name="return-code-values"></a>Hodnoty návratový kód
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Žádné.
 
#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení, členové role pevného serveru správce sysadmin systému tuto uloženou proceduru mohou spustit. Omezit přístup jenom uživatel moct monitorovat úlohy, můžete udělit uživatele, aby byla součástí následující role databáze v databáze agenta úloh při vytvoření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních pracovníci v těchto rolích najdete v části oprávnění v tomto dokumentu. Jenom členové skupiny sysadmin můžete upravit vlastnosti úlohy, které vlastní jiní uživatelé tuto uloženou proceduru.


### <a name="spaddtargetgroup"></a>sp_add_target_group

Přidá cílovou skupinu.

#### <a name="syntax"></a>Syntaxe


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argumenty
[  **@target_group_name =** ] "target_group_name.  
Název cílové skupiny, chcete-li vytvořit. target_group_name je nvarchar(128) bez výchozí hodnoty.

[  **@target_group_id =** ] target_group_id výstup cílové skupině identifikační číslo přiřazené do úlohy, pokud byl úspěšně vytvořen. target_group_id je proměnná výstupního typu UniqueIdentifier, výchozí hodnota je NULL.

#### <a name="return-code-values"></a>Hodnoty návratový kód
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Cílové skupiny poskytují snadný způsob, jak cílit na úlohu v kolekci databází.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení, členové role pevného serveru správce sysadmin systému tuto uloženou proceduru mohou spustit. Omezit přístup jenom uživatel moct monitorovat úlohy, můžete udělit uživatele, aby byla součástí následující role databáze v databáze agenta úloh při vytvoření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních pracovníci v těchto rolích najdete v části oprávnění v tomto dokumentu. Jenom členové skupiny sysadmin můžete upravit vlastnosti úlohy, které vlastní jiní uživatelé tuto uloženou proceduru.

### <a name="spdeletetargetgroup"></a>sp_delete_target_group

Odstraní cílovou skupinu.

#### <a name="syntax"></a>Syntaxe


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argumenty
[  **@target_group_name =** ] "target_group_name.  
Název cílové skupiny odstraňte. target_group_name je nvarchar(128) bez výchozí hodnoty.

#### <a name="return-code-values"></a>Hodnoty návratový kód
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Žádné.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení, členové role pevného serveru správce sysadmin systému tuto uloženou proceduru mohou spustit. Omezit přístup jenom uživatel moct monitorovat úlohy, můžete udělit uživatele, aby byla součástí následující role databáze v databáze agenta úloh při vytvoření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních pracovníci v těchto rolích najdete v části oprávnění v tomto dokumentu. Jenom členové skupiny sysadmin můžete upravit vlastnosti úlohy, které vlastní jiní uživatelé tuto uloženou proceduru.

### <a name="spaddtargetgroupmember"></a>sp_add_target_group_member

Přidá do skupiny cílové databázi nebo skupině databází.

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
[  **@target_group_name =** ] "target_group_name.  
Název cílové skupiny, ke které se přidají člena. target_group_name je nvarchar(128) bez výchozí hodnoty.

[  **@membership_type =** ] "membership_type.  
Určuje, pokud cílový člen skupiny budou zahrnuty nebo vyloučeny. target_group_name je nvarchar(128) s výchozí hodnotu "Zahrnutí". Platné hodnoty pro target_group_name jsou "Zahrnutí" nebo "Vyloučit".

[  **@target_type =** ] "target_type –.  
Typ cílové databázi nebo kolekci databází, včetně všech databází na serveru, všechny databáze v Elastickém fondu, všechny databáze v mapě horizontálních oddílů nebo jednotlivé databáze. target_type – je nvarchar(128) bez výchozí hodnoty. Target_type – platné hodnoty jsou "Systému SQL Server", "SqlElasticPool", "SqlDatabase" nebo "SqlShardMap". 

[  **@refresh_credential_name =** ] "refresh_credential_name.  
Název logického serveru. refresh_credential_name je nvarchar(128) bez výchozí hodnoty.

[  **@server_name =** ] "Název_serveru"  
Název logického serveru, který by měl být přidán do zadanou cílovou skupinu. název_serveru musí být zadaný po target_type – "Systému SQL Server". název_serveru je nvarchar(128) bez výchozí hodnoty.

[  **@database_name =** ] "název_databáze.  
Název databáze, který by měl být přidán do zadanou cílovou skupinu. target_type – po "SqlDatabase" musí být zadaný název databáze database_name. Název databáze database_name je nvarchar(128) bez výchozí hodnoty.

[  **@elastic_pool_name =** ] "elastic_pool_name.  
Název elastického fondu, který by měl být přidán do zadanou cílovou skupinu. elastic_pool_name musí být zadaný po target_type – "SqlElasticPool". elastic_pool_name je nvarchar(128) bez výchozí hodnoty.

[  **@shard_map_name =** ] "shard_map_name.  
Název fondu mapy horizontálních oddílů, který by měl být přidán do zadanou cílovou skupinu. elastic_pool_name musí být zadaný po target_type – "SqlSqlShardMap". shard_map_name je nvarchar(128) bez výchozí hodnoty.

[  **@target_id =** ] target_group_id výstupu  
Identifikační číslo cílového přiřazeny k členovi cílové skupiny, pokud vytvořili přidán do cílové skupině. target_id je proměnná výstupního typu UniqueIdentifier, výchozí hodnota je NULL.
Návratové hodnoty kód 0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Úloha se spustí na všechny databáze na daném serveru nebo v době provádění, když elastický fond nebo logického serveru elastický fond je součástí cílové skupiny.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení, členové role pevného serveru správce sysadmin systému tuto uloženou proceduru mohou spustit. Omezit přístup jenom uživatel moct monitorovat úlohy, můžete udělit uživatele, aby byla součástí následující role databáze v databáze agenta úloh při vytvoření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních pracovníci v těchto rolích najdete v části oprávnění v tomto dokumentu. Jenom členové skupiny sysadmin můžete upravit vlastnosti úlohy, které vlastní jiní uživatelé tuto uloženou proceduru.

#### <a name="examples"></a>Příklady
Následující příklad přidá všechny databáze v Londýně a NewYork serverů do skupiny informace o zákaznících údržba serverů. Musíte se připojit k databázi úloh při vytvoření agent úlohy v tomto případě ElasticJobs.

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

Odebere z cílová skupina členem cílové skupiny.

#### <a name="syntax"></a>Syntaxe


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] ‘target_id’]
```



Argumenty [ @target_group_name =] "target_group_name.  
Název cílové skupiny, ze kterého chcete odebrat člena cílové skupiny. target_group_name je nvarchar(128) bez výchozí hodnoty.

[ @target_id =] target_id  
 Cíl identifikační číslo přiřazené cílový člen skupiny má být odebrán. target_id je uniqueidentifier, výchozí hodnota je NULL.

#### <a name="return-code-values"></a>Hodnoty návratový kód
0 (úspěch) nebo 1 (chyba)

#### <a name="remarks"></a>Poznámky
Cílové skupiny poskytují snadný způsob, jak cílit na úlohu v kolekci databází.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení, členové role pevného serveru správce sysadmin systému tuto uloženou proceduru mohou spustit. Omezit přístup jenom uživatel moct monitorovat úlohy, můžete udělit uživatele, aby byla součástí následující role databáze v databáze agenta úloh při vytvoření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních pracovníci v těchto rolích najdete v části oprávnění v tomto dokumentu. Jenom členové skupiny sysadmin můžete upravit vlastnosti úlohy, které vlastní jiní uživatelé tuto uloženou proceduru.

#### <a name="examples"></a>Příklady
Následující příklad odebere server Londýn ze skupiny informace o zákaznících údržba serverů. Musíte se připojit k databázi úloh při vytvoření agent úlohy v tomto případě ElasticJobs.

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

Odebere záznamy historie úlohy.

#### <a name="syntax"></a>Syntaxe


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumenty
[  **@job_name =** ] "hodnotu job_name.  
Název úlohy, pro kterou chcete odstranit záznamy historie. hodnotu job_name je nvarchar(128), výchozí hodnota je NULL. Musí být zadán parametrem job_id nebo hodnotu job_name, ale nelze zadat obě.

[  **@job_id =** ] parametrem job_id  
 Identifikační číslo úlohy úlohy pro záznamy, které chcete odstranit. parametrem job_id je uniqueidentifier, výchozí hodnota je NULL. Musí být zadán parametrem job_id nebo hodnotu job_name, ale nelze zadat obě.

[  **@oldest_date =** ] oldest_date  
 Nejstarší záznam, který chcete uchovat v historii. oldest_date DATETIME2, je výchozí hodnota je NULL. Pokud je zadána oldest_date, sp_purge_jobhistory pouze odebere záznamy, které jsou starší než hodnota zadaná.

#### <a name="return-code-values"></a>Hodnoty návratový kód
0 (úspěch) nebo 1 (chyba) poznámky cílové skupiny poskytují snadný způsob, jak cílit na úlohu v kolekci databází.

#### <a name="permissions"></a>Oprávnění
Ve výchozím nastavení, členové role pevného serveru správce sysadmin systému tuto uloženou proceduru mohou spustit. Omezit přístup jenom uživatel moct monitorovat úlohy, můžete udělit uživatele, aby byla součástí následující role databáze v databáze agenta úloh při vytvoření agenta úlohy:
- jobs_reader

Podrobnosti o oprávněních pracovníci v těchto rolích najdete v části oprávnění v tomto dokumentu. Jenom členové skupiny sysadmin můžete upravit vlastnosti úlohy, které vlastní jiní uživatelé tuto uloženou proceduru.

#### <a name="examples"></a>Příklady
Následující příklad přidá všechny databáze v Londýně a NewYork serverů do skupiny informace o zákaznících údržba serverů. Musíte se připojit k databázi úloh při vytvoření agent úlohy v tomto případě ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Zobrazení úlohy

Jsou k dispozici v následujících zobrazení [úloh databáze](elastic-jobs-overview.md#job-database).


|Zobrazení  |Popis  |
|---------|---------|
|[jobs_executions](#jobsexecutions-view)     |  Zobrazí historii spuštění úlohy.      |
|[Úlohy](#jobs-view)     |   Zobrazí všechny úlohy.      |
|[job_versions](#jobversions-view)     |   Zobrazí všechny verze úlohy.      |
|[kroky úlohy](#jobsteps-view)     |     Zobrazí všechny kroky v aktuální verzi každou úlohu.    |
|[jobstep_versions](#jobstepversions-view)     |     Zobrazí všechny kroky ve všech verzích každé úlohy.    |
|[target_groups](#targetgroups-view)     |      Zobrazí všechny cílové skupiny.   |
|[target_group_members](#targetgroups-view)     |   Zobrazuje všechny členy všechny cílové skupiny.      |


### <a name="jobsexecutions-view"></a>jobs_executions zobrazení

[úlohy]. [jobs_executions]

Zobrazí historii spuštění úlohy.


|Název sloupce|   Typ dat   |Popis|
|---------|---------|---------|
|**job_execution_id**   |UniqueIdentifier|  Jedinečné ID instance provádění úlohy.
|**hodnotu job_name**   |nvarchar(128)  |Název úlohy.
|**parametrem job_id** |UniqueIdentifier|  Jedinečné ID úlohy.
|**job_version**    |int    |Verze úlohy (automaticky aktualizuje pokaždé, když se upraví úlohy).
|**step_id**    |int|   (Pro tuto úlohu) Jedinečný identifikátor pro krok. Hodnota NULL znamená, že to je provádění nadřazené úlohy.
|**is_active**| Bit |Určuje, zda informace jsou aktivní nebo neaktivní. 1 označuje aktivní úlohy a 0 označuje neaktivní.
|**životní cyklus**| nvarchar(50)|Hodnotu označující stav úlohy: 'Vytvořit', v průběhu, 'se nezdařil., "Bylo úspěšné", "Bylo přeskočeno", "SucceededWithSkipped"|
|**create_time**|   datetime2(7)|   Datum a čas vytvoření úlohy.
|**start_time** |datetime2(7)|  Datum a čas spuštění úlohy spuštění. Hodnota NULL, pokud úloha ještě nebyla spuštěna.
|**end_time**|  datetime2(7)    |Datum a čas dokončení provádění úlohy. Hodnota NULL, pokud úloha ještě nebyla spuštěna, nebo nebylo dosud dokončen.
|**current_attempts**   |int    |Počet pokusů, které se na krok opakovat. Nadřazená úloha bude 0, podřízená úloha spuštění bude 1 nebo větší podle zásady spouštění.
|**current_attempt_start_time** |datetime2(7)|  Datum a čas spuštění úlohy spuštění. Hodnota NULL znamená, že to je provádění nadřazené úlohy.
|**last_message**   |nvarchar(max)| Zpráva historie úloh nebo krok. 
|**target_type**|   nvarchar(128)   |Typ cílové databázi nebo kolekci databází, včetně všech databází serveru, všechny databáze v Elastickém fondu nebo v databázi. Target_type – platné hodnoty jsou "Systému SQL Server", "SqlElasticPool" nebo "SqlDatabase". Hodnota NULL znamená, že to je provádění nadřazené úlohy.
|**target_id**  |UniqueIdentifier|  Jedinečné ID člena cílové skupiny.  Hodnota NULL znamená, že to je provádění nadřazené úlohy.
|**target_group_name**  |nvarchar(128)  |Název cílové skupiny. Hodnota NULL znamená, že to je provádění nadřazené úlohy.
|**target_server_name**|    nvarchar(256)|  Název logického serveru obsažené v cílové skupině. Zadat jenom v případě target_type – je "Systému SQL Server". Hodnota NULL znamená, že to je provádění nadřazené úlohy.
|**target_database_name**   |nvarchar(128)| Název databáze, které jsou obsaženy v cílové skupině. Zadat, jenom když target_type – je "SqlDatabase". Hodnota NULL znamená, že to je provádění nadřazené úlohy.


### <a name="jobs-view"></a>zobrazení úloh

[úlohy]. [úloh]

Zobrazí všechny úlohy.

|Název sloupce|   Typ dat|  Popis|
|------|------|-------|
|**hodnotu job_name**|  nvarchar(128)   |Název úlohy.|
|**parametrem job_id**|    UniqueIdentifier    |Jedinečné ID úlohy.|
|**job_version**    |int    |Verze úlohy (automaticky aktualizuje pokaždé, když se upraví úlohy).|
|**Popis**    |nvarchar(512)| Popis pro úlohu. povolené bit označuje, zda je povoleno úlohy. 1 znamená povolené úlohy a 0 znamená zakázané úlohy.|
|**schedule_interval_type** |nvarchar(50)   |Hodnota určující, kdy úloha má být provedena: "Po", 'Minuty', 'Hodiny', ' dnů","Týden","měsíců.
|**schedule_interval_count**|   int|    Počet období schedule_interval_type ke kterým dochází mezi každé spuštění úlohy.|
|**schedule_start_time**    |datetime2(7)|  Datum a čas, kdy byl poslední bylo zahájeno spuštění úlohy.|
|**schedule_end_time**| datetime2(7)|   Datum a čas, kdy byl poslední dokončené spuštění úlohy.|


### <a name="jobversions-view"></a>job_versions zobrazení

[úlohy]. [job_verions]

Zobrazí všechny verze úlohy.

|Název sloupce|   Typ dat|  Popis|
|------|------|-------|
|**hodnotu job_name**|  nvarchar(128)   |Název úlohy.|
|**parametrem job_id**|    UniqueIdentifier    |Jedinečné ID úlohy.|
|**job_version**    |int    |Verze úlohy (automaticky aktualizuje pokaždé, když se upraví úlohy).|


### <a name="jobsteps-view"></a>Zobrazit kroky úlohy

[úlohy]. [kroky úlohy]

Zobrazí všechny kroky v aktuální verzi každou úlohu.

|Název sloupce    |Typ dat| Popis|
|------|------|-------|
|**hodnotu job_name**   |nvarchar(128)| Název úlohy.|
|**parametrem job_id** |UniqueIdentifier   |Jedinečné ID úlohy.|
|**job_version**|   int|    Verze úlohy (automaticky aktualizuje pokaždé, když se upraví úlohy).|
|**step_id**    |int    |(Pro tuto úlohu) Jedinečný identifikátor pro krok.|
|**STEP_NAME**  |nvarchar(128)  |(Pro tuto úlohu) jedinečný název kroku.|
|**command_type**   |nvarchar(50)   |Typ příkazu ke spuštění v krok úlohy. Pro v1, musí hodnota rovná a výchozí hodnota je "TSql".|
|**command_source** |nvarchar(50)|  Umístění příkazu. "Vložené" pro v1, je výchozí a přijatelný jenom hodnoty.|
|**Příkaz**|   nvarchar(max)|  Příkazy mají být provedeny pomocí Elastických úloh prostřednictvím command_type.|
|**credential_name**|   nvarchar(128)   |Název přihlašovacích údajů s rozsahem databáze použitý k provádění úlohy.|
|**target_group_name**| nvarchar(128)   |Název cílové skupiny.|
|**target_group_id**|   UniqueIdentifier|   Jedinečné ID cílové skupiny.|
|**initial_retry_interval_seconds**|    int |Zpoždění před první pokus o opakování. Výchozí hodnota je 1.|
|**maximum_retry_interval_seconds** |int|   Maximální zpoždění mezi opakovanými pokusy. Pokud by růst větší než tato hodnota zpoždění mezi opakovanými pokusy, to je se na tuto hodnotu místo toho uzavřeny. Výchozí hodnota je 120.|
|**retry_interval_backoff_multiplier**  |Real|  Násobitel použít na zpoždění opakování, pokud více úloh kroku spuštění pokusí selhání. Výchozí hodnota je 2.0.|
|**retry_attempts** |int|   Počet opakovaných pokusů použít, pokud tento krok nezdaří. Výchozí hodnota 10, označující žádné pokusy o opakování.|
|**step_timeout_seconds**   |int|   Množství času během několika minut mezi pokusy o opakování. Výchozí hodnota je 0, což označuje 0 minut.|
|**output_type**    |nvarchar(11)|  Umístění příkazu. "Vložené" v aktuální verzi preview, je výchozí a přijatelný jenom hodnoty.|
|**output_credential_name**|    nvarchar(128)   |Nastavit název přihlašovacích údajů, který se má použít pro připojení k cílovému serveru můžete ukládat výsledky.|
|**output_subscription_id**|    UniqueIdentifier|   Jedinečné ID předplatného cílového server\database výsledků nastavit na spuštění dotazu.|
|**output_resource_group_name** |nvarchar(128)| Název skupiny prostředků, ve které se nachází cílový server.|
|**output_server_name**|    nvarchar(256)   |Název cílového serveru pro sadu výsledků.|
|**output_database_name**   |nvarchar(128)| Název cílové databáze sady výsledků.|
|**output_schema_name** |nvarchar(max)| Název cílové schéma. Výchozí hodnota je dbo, pokud není zadaný.|
|**output_table_name**| nvarchar(max)|  Název tabulky můžete ukládat výsledky nastavení z výsledků dotazu. Tabulka se vytvoří automaticky v závislosti na schématu výsledky nastavte, pokud ještě neexistuje. Schéma musí odpovídat schématu sady výsledků.|
|**max_parallelism**|   int|    Maximální počet databází na každý elastický fond, který krok úlohy poběží současně. Výchozí hodnota je NULL, což znamená bez omezení. |


### <a name="jobstepversions-view"></a>jobstep_versions zobrazení

[úlohy]. [jobstep_versions]

Zobrazí všechny kroky ve všech verzích každé úlohy. Schéma je stejný jako [kroky úlohy](#jobsteps-view).

### <a name="targetgroups-view"></a>target_groups zobrazení

[úlohy]. [target_groups]

Uvádí všechny cílové skupiny.

|Název sloupce|Typ dat| Popis|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |Název cílové skupiny, kolekce databází. 
|**target_group_id**    |UniqueIdentifier   |Jedinečné ID cílové skupiny.

### <a name="targetgroupsmembers-view"></a>target_groups_members zobrazení

[úlohy]. [target_groups_members]

Zobrazuje všechny členy všechny cílové skupiny.

|Název sloupce|Typ dat| Popis|
|-----|-----|-----|
|**target_group_name**  |nvarchar (128|Název cílové skupiny, kolekce databází. |
|**target_group_id**    |UniqueIdentifier   |Jedinečné ID cílové skupiny.|
|**membership_type**    |int|   Určuje, zda je cílový člen skupiny zahrnuty nebo vyloučeny v cílové skupině. Platné hodnoty pro target_group_name jsou "Zahrnutí" nebo "Vyloučit".|
|**target_type**    |nvarchar(128)| Typ cílové databázi nebo kolekci databází, včetně všech databází serveru, všechny databáze v Elastickém fondu nebo v databázi. Target_type – platné hodnoty jsou "Systému SQL Server", "SqlElasticPool", "SqlDatabase" nebo "SqlShardMap".|
|**target_id**  |UniqueIdentifier|  Jedinečné ID člena cílové skupiny.|
|**refresh_credential_name**    |nvarchar(128)  |Název databáze s rozsahem přihlašovací údaje použité pro připojení k cílové člena skupiny.|
|**subscription_id**    |UniqueIdentifier|  Jedinečné ID předplatného.|
|**resource_group_name**    |nvarchar(128)| Název skupiny prostředků, ve kterém se nachází cílový člen skupiny.|
|**název_serveru**    |nvarchar(128)  |Název logického serveru obsažené v cílové skupině. Zadat jenom v případě target_type – je "Systému SQL Server". |
|**Název databáze database_name**  |nvarchar(128)  |Název databáze, které jsou obsaženy v cílové skupině. Zadat, jenom když target_type – je "SqlDatabase".|
|**elastic_pool_name**  |nvarchar(128)| Název elastického fondu obsažené v cílové skupině. Zadat, jenom když target_type – je "SqlElasticPool".|
|**shard_map_name** |nvarchar(128)| Název mapy horizontálních oddílů, které jsou obsaženy v cílové skupině. Zadat, jenom když target_type – je "SqlShardMap".|


## <a name="resources"></a>Zdroje a prostředky

 - ![Ikona odkaz tématu](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "ikonu odkazu tématu") [konvencí syntaxe Transact-SQL](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  


## <a name="next-steps"></a>Další postup

- [Vytváření a správa elastických úloh s využitím PowerShellu](elastic-jobs-powershell.md)
- [Autorizace a oprávnění systému SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
  
