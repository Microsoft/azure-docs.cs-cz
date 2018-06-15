---
title: Začínáme s úlohami elastické databáze | Microsoft Docs
description: Spuštění skriptů T-SQL, které jsou rozmístěny v několika databází pomocí úlohy elastické databáze.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 4f12c3353ca4949b3c1c031420ec5a0b8fdb2dbf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649148"
---
# <a name="getting-started-with-elastic-database-jobs"></a>Začínáme s úlohami elastické databáze
Elastické databáze úlohy (preview) pro databázi SQL Azure umožňuje spolehlivě spuštění skriptů T-SQL, které jsou rozmístěny v několika databází při automaticky opakování a poskytování případné dokončení záruky. Další informace o funkci úlohy elastické databáze najdete v tématu [elastické úlohy](sql-database-elastic-jobs-overview.md).

Tento článek rozšiřuje najít v ukázce [Začínáme s nástroje elastické databáze](sql-database-elastic-scale-get-started.md). Po dokončení zjistíte, jak vytvářet a spravovat úlohy, které správu skupiny související databází. Není nutné používat nástroje elastické škálování, pokud chcete využít výhod elastické úlohy.

## <a name="prerequisites"></a>Požadavky
Stažení a spuštění [Začínáme s ukázkou nástroje elastické databáze](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Vytvoření horizontálního oddílu mapy manager pomocí ukázkové aplikace
Zde vytvoříte mapování horizontálních manager spolu s několika horizontálních oddílů, za nímž následuje vložení dat do horizontálních oddílů. Pokud již máte horizontálních oddílů nastavit s horizontálně dělená data v nich, můžete přeskočit následující kroky a přesunout k další části.

1. Sestavení a spuštění **Začínáme s nástroje elastické databáze** ukázkové aplikace. Postupujte podle pokynů až do kroku 7 v části [stažení a spuštění ukázkové aplikace](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). Na konci tohoto kroku 7 najdete v příkazovém řádku následující:

   ![příkazový řádek](./media/sql-database-elastic-query-getting-started/cmd-prompt.png)

2. V okně příkazového řádku zadejte "1" a stiskněte klávesu **Enter**. To vytvoří horizontálního oddílu správce mapy a přidá dva horizontálních oddílů server. Potom zadejte "3" a stiskněte klávesu **Enter**; čtyřikrát tuto akci zopakujte. Vloží řádky ukázková data ve vašem horizontálních oddílů.
3. [Portál Azure](https://portal.azure.com) by měl zobrazit tři nové databáze:

   ![Visual Studio potvrzení](./media/sql-database-elastic-query-getting-started/portal.png)

   Nyní vytvoříme vlastní databázi kolekce, která odráží všechny databáze v mapě horizontálního oddílu. To umožňuje vytvářet a spouštět úlohy, která přidá novou tabulku napříč horizontálních oddílů.

Zde jsme by obvykle vytvoření mapy horizontálního oddílu cíle, pomocí **New-AzureSqlJobTarget** rutiny. Databáze manager mapy horizontálního oddílu musí být nastavena jako cíl databáze a pak je konkrétní horizontálních mapy zadaný jako cíl. Místo toho bude výčet všechny databáze na serveru a přidejte databáze do nové vlastní kolekce, s výjimkou hlavní databázi.

## <a name="creates-a-custom-collection-and-add-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Vytvoří vlastní kolekce a přidejte všechny databáze na serveru k cíli vlastní kolekce s výjimkou hlavní server.
   ```
    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName
    $dbsinserver | %{
    $currentdb = $_.DatabaseName
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target."
        }

        else
        {
            throw $_
        }

    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
   }
   ```
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Vytvoření skriptu T-SQL pro provedení mezi databází
   ```
    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script
   ```

## <a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Vytvořit úlohu pro spuštění skriptu přes vlastní skupinu databází

   ```
    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="execute-the-job"></a>Provedení úlohy
Následující skript prostředí PowerShell můžete použít ke spuštění stávající úloze:

Aktualizujte tak, aby odrážela název požadované úlohy, který provedli následující proměnnou:

   ```
    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="retrieve-the-state-of-a-single-job-execution"></a>Načíst stav provádění jedné úlohy
Použijte stejný **Get-AzureSqlJobExecution** rutiny s **metoda IncludeChildren** parametr, pokud chcete zobrazit stav podřízených spuštění úlohy, konkrétně určitém stavu pro každé spuštění úlohy každou databázi cílem úlohy.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions
   ```

## <a name="view-the-state-across-multiple-job-executions"></a>Zobrazení stavu mezi jednotlivými spuštěními více úloh
**Get-AzureSqlJobExecution** rutina má více volitelné parametry, které lze použít k zobrazení více spuštění úlohy, filtrovaný pomocí zadané parametry. Následující ukazuje některé možné způsoby, jak používat Get-AzureSqlJobExecution:

Načtěte všechny aktivní nejvyšší úrovně úloha spuštění:

   ```
    Get-AzureSqlJobExecution
   ```

Načtení všech spuštěních nejvyšší úrovně úlohy, včetně spuštěních neaktivní úlohy:

   ```
    Get-AzureSqlJobExecution -IncludeInactive
   ```

Načtěte všechny podřízené úlohy spuštěních zadaná úloha spuštění ID, včetně spuštěních neaktivní úlohy:

   ```
    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren
   ```

Načíst všechny úlohy spuštěních vytvořený plán / úlohy kombinaci, včetně neaktivní úlohy:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive
   ```

Načtěte všechny úlohy cílení na mapě zadaný horizontálního oddílu, včetně neaktivní úlohy:

   ```
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Načtěte všechny úlohy cílení na vlastní kolekce, včetně neaktivní úlohy:

   ```
    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Načtení seznamu spuštěních úloh úlohy v rámci provedení určité úlohy:

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions
   ```

Načtěte podrobnosti o provádění úkolů úlohy:

Následující skript prostředí PowerShell slouží k zobrazení podrobností o provádění úloh úkolu, který je zvláště užitečná při ladění selhání spuštění.
   ```
    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution
   ```

## <a name="retrieve-failures-within-job-task-executions"></a>Načtení selhání v rámci úlohy spuštěních úloh
Objekt JobTaskExecution obsahuje vlastnost pro životní cyklus úlohy společně s vlastností zpráv. Pokud se nezdařilo provádění úloh úkolu, je nastavena životního cyklu na *se nezdařilo* a vlastnosti zprávy je nastavena na jeho zásobníku a výsledný zpráva o výjimce. Pokud úloha nebyla úspěšná, je důležité k zobrazení podrobností úlohy, které se nezdařilo pro danou úlohu.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions)
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }
   ```

## <a name="waiting-for-a-job-execution-to-complete"></a>Čekání na dokončení provedení úlohy
Následující skript prostředí PowerShell umožňuje počkejte na dokončení úlohy úlohy:

   ```
    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="create-a-custom-execution-policy"></a>Vytvořit zásadu vlastní spuštění
Elastické databáze úlohy podporuje vytváření vlastní provádění zásad, které mohou být použity při spouštění úloh.

Zásady spouštění aktuálně povolit pro definování:

* Název: Identifikátor pro zásady spouštění.
* Časový limit úlohy: Celkový čas předtím, než je úlohu zrušil úlohy elastické databáze.
* Počáteční Interval opakování: Interval čekání před první opakování.
* Maximální Interval opakování: Limitu opakování intervalů používat.
* Koeficient omezení rychlosti Interval opakování: Koeficient používá k výpočtu další interval mezi opakovanými pokusy.  Se používá následující vzorec: (počáteční opakujte Interval) * Math.pow ((Interval omezení rychlosti koeficient), (počet pokusů o) - 2).
* Maximální počet pokusů: Maximální počet opakování pokusů provést v rámci úlohy.

Výchozí zásadu spouštění používá následující hodnoty:

* Název: Zásady spouštění výchozí
* Časový limit úlohy: 1 týden
* Počáteční Interval opakování: 100 milisekund
* Maximální Interval opakování: 30 minut
* Opakujte koeficient Interval: 2
* Maximální počet pokusů: 2 147 483 647

Vytvořte zásadu požadované spouštění:

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy
   ```

### <a name="update-a-custom-execution-policy"></a>Aktualizovat zásady vlastní spuštění
Aktualizujte zásady spouštění požadované aktualizace:

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
   ```

## <a name="cancel-a-job"></a>Zrušení úlohy
Elastické databáze úlohy podporuje požadavků na zrušení úlohy.  Pokud úlohy elastické databáze zjistí žádost o zrušení úlohy se spouští, pokusí se zastavit úlohu.

Že úlohy elastické databáze můžete provést zrušení dvěma různými způsoby:

1. Zrušení aktuálně spuštěných úloh: Pokud zrušení se zjistí, zatímco úloha je aktuálně spuštěna, zrušení dojde k pokusu o v rámci aktuálně prováděné aspekt úlohy.  Například: Pokud je aktuálně provést při pokusu o zrušení dlouho spuštěných dotazu, je pokus o dotaz zrušíte.
2. Ruší opakování úkolů: V případě zrušení zjištění vlákno řízení předtím, než se spustí úloha pro spuštění, vlákno řízení zabraňuje spuštění úlohy a deklarovat požadavek, protože došlo ke zrušení.

Pokud zrušení úlohy je požadováno pro nadřazené úloze, je žádost o zrušení dodržení pro nadřazené úloze a všechny jeho podřízené úlohy.

Odeslat žádost o zrušení, použijte **Stop-AzureSqlJobExecution** rutiny a nastavte **JobExecutionId** parametr.

   ```
    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Odstranit úlohu podle názvu a historie úlohy
Elastické databáze úlohy podporuje asynchronní odstranění úloh. Úloha může být označený k odstranění a systému odstraní úlohy a všechny jeho historie úlohy po dokončení všech spuštěních úloh pro úlohu. Systém automaticky nezruší spuštěních aktivní úlohy.  

Místo toho musí být volána Stop-AzureSqlJobExecution zrušit aktivní úloha spuštění.

Chcete-li aktivovat odstranění úlohy, použijte **odebrat AzureSqlJob** rutiny a nastavte **JobName** parametr.

   ```
    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
   ```

## <a name="create-a-custom-database-target"></a>Vytvořit cíl vlastní databázi
Vlastní databázi cíle může být definován v úlohy elastické databáze, které se dají použít pro spuštění přímo nebo pro zahrnutí do skupiny vlastní databázi. Vzhledem k tomu **elastické fondy** nejsou přímo, ale podporovány prostřednictvím rozhraní API prostředí PowerShell, můžete jednoduše vytvořit vlastní databázi cíle a cílové kolekce vlastní databázi, která zahrnuje všechny databáze ve fondu.

Nastavte následující proměnné tak, aby odrážela informace o požadované databázi:

   ```
    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName
   ```

## <a name="create-a-custom-database-collection-target"></a>Vytvořit cíl kolekce vlastní databázi
Povolit spuštění v rámci více cílů definovaných databázových lze definovat cílovou kolekci vlastní databázi. Po vytvoření skupiny databáze, databáze může být přidružený k cíli vlastní kolekce.

Nastavte následující proměnné tak, aby odrážela konfigurace cílového požadovanou vlastní kolekce:

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="add-databases-to-a-custom-database-collection-target"></a>Přidání databází do kolekce cíl vlastní databázi
Cíle databáze může být přidružen cíle kolekce vlastní databázi a vytvořte skupinu databází. Vždy, když se vytvoří úloha, která zaměřena na cíl, vlastní databázi kolekce, je rozšířena do cílové databáze přidružený ke skupině v době spuštění.

Přidejte databázi požadované určité vlastní kolekci:

   ```
    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName
   ```

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Zkontrolujte databází v rámci kolekce cíl vlastní databázi
Použití **Get-AzureSqlJobTarget** rutiny načíst podřízené databází v rámci kolekce cíl vlastní databázi.

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets
   ```

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Vytvořit úlohu pro spuštění skriptu mezi cílovou kolekci vlastní databázi
Použití **New-AzureSqlJob** rutiny vytvořit úlohu pro skupinu databází definované cílovou kolekci vlastní databázi. Úlohy elastické databáze rozšíří úlohy na více podřízených úloh, každou odpovídající databázi přidruženého cílové kolekce vlastní databázi a ujistěte se, že skript se spustí na každou databázi. Znovu je důležité, aby skripty se idempotent chcete být odolní vůči opakování.

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="data-collection-across-databases"></a>Shromažďování dat mezi databázemi
**Elastické databáze úlohy** podporuje provádění dotazu napříč skupinou databází a odesílá výsledky do tabulky zadaná databáze. V tabulce můžete položit dotaz na ve skutečnosti zobrazíte výsledky dotazu z každé databáze. To poskytuje asynchronní mechanismus, při spuštění dotazu mezi mnoha databázemi. Selhání případech jako jedna z databází není dočasně k dispozici jsou automaticky zpracováván opakování.

Zadané cílové tabulky se automaticky vytvoří, pokud ještě neexistuje, odpovídající schéma vrácené výsledné sady. Pokud spuštění skriptu vrátí více sad výsledků dotazu, odešle úlohy elastické databáze pouze první z nich pro zadané cílové tabulky.

Následující skript prostředí PowerShell můžete použít ke spuštění skriptu shromažďování své výsledky do zadané tabulky. Tento skript předpokládá, že byla vytvořena skriptu T-SQL, který vrací jednu výslednou sadu a kolekce cíl vlastní databáze byla vytvořena.

Nastavte následující tak, aby odrážela požadované skriptu, přihlašovací údaje a provádění cíl:

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Vytvořte a spusťte úlohu pro scénáře kolekce dat
   ```
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Vytvoření plánu pro provádění úlohy pomocí aktivační události úlohy
Následující skript prostředí PowerShell slouží k vytvoření opakovaném plánu. Tento skript používá intervalu jednu minutu, ale nové AzureSqlJobSchedule také podporuje – DayInterval, - HourInterval, - MonthInterval a - WeekInterval parametry. Plány, které jsou spouštěny pouze jednou lze vytvořit pomocí předávání - jednorázově.

Vytvoření nového plánu:
   ```
    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime
    Write-Output $schedule
   ```

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Vytvořit aktivační událost úlohy tak, aby měl úlohu provést podle časového plánu
Aktivační události úlohy lze definovat za účelem mít úlohu provést podle časového plánu. Následující skript prostředí PowerShell slouží k vytvoření aktivační události úlohy.

Nastavte následující proměnné tak, aby odpovídaly požadované úlohy a plán:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
    Write-Output $jobTrigger
   ```

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Odebrání naplánované přidružení o zastavení úlohy ve spouštění podle plánu.
Odebrání ze opakovaném provádění úlohy prostřednictvím aktivační události úlohy, můžete odebrat aktivační události úlohy.
Odebrat aktivační události úlohy zastavení úlohy z se spouští podle plánu pomocí **odebrat AzureSqlJobTrigger** rutiny.

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
   ```

## <a name="import-elastic-database-query-results-to-excel"></a>Import výsledků dotazu elastické databáze do aplikace Excel
 Můžete importovat výsledky z dotazu do souboru aplikace Excel.

1. Spusťte aplikaci Excel 2013.
2. Přejděte na **Data** pásu karet.
3. Klikněte na tlačítko **z jiných zdrojů** a klikněte na tlačítko **z SQL serveru**.

   ![Importu pro aplikaci Excel z jiných zdrojů](./media/sql-database-elastic-query-getting-started/exel-sources.png)

4. V **Průvodce datovým připojením** zadejte název a přihlašovací údaje serveru. Pak klikněte na tlačítko **Další**.
5. V dialogovém okně **vyberte databáze, která obsahuje data, která chcete**, vyberte **ElasticDBQuery** databáze.
6. Vyberte **zákazníci** tabulky v zobrazení seznamu a klikněte na tlačítko **Další**. Pak klikněte na tlačítko **Dokončit**.
7. V **importovat Data** formuláři v části **vyberte, jak chcete zobrazit tato data v sešitu**, vyberte **tabulky** a klikněte na tlačítko **OK**.

Všechny řádky z **zákazníci** tabulky, uložené v různých horizontálních oddílů naplnit listu aplikace Excel.

## <a name="next-steps"></a>Další postup
Teď můžete použít funkce dat v aplikaci Excel. Použijte připojovací řetězec s názvem serveru, názvu databáze a pověření pro připojení k databázi elastické dotazu vaše integrace nástrojů BI a data. Ujistěte se, že systém SQL Server je podporovaný jako zdroj dat pro vaše nástroje. Viz elastické dotaz do databáze a externí tabulky stejně jako všechny ostatní databáze systému SQL Server a SQL Server tabulky, které by se připojit k vaší nástrojem.

### <a name="cost"></a>Náklady
Není k dispozici pro použití funkce dotazu elastické databáze bez dalších poplatků. Ale v tuto chvíli tato funkce je dostupná jenom na Premium a kritické obchodní (preview) databáze i elastické fondy jako koncový bod, ale horizontálních oddílů lze vrstvy jakékoli služby.

Informace o cenách najdete v části [podrobnosti o cenách na SQL databázi](https://azure.microsoft.com/pricing/details/sql-database/).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
