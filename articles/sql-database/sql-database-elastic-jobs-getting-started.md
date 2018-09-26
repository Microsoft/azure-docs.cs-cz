---
title: Začínáme s úlohy elastické databáze | Dokumentace Microsoftu
description: Úlohy elastické databáze použijte ke spuštění skriptů T-SQL, které zahrnují více databází.
services: sql-database
ms.service: sql-database
subservice: sacoperations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: e760aee960cf9d84cd5076c993b1a9583b045860
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159274"
---
# <a name="getting-started-with-elastic-database-jobs"></a>Začínáme s úlohy elastické databáze


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


Úlohy elastic Database (preview) pro službu Azure SQL Database umožňuje spolehlivě spouštět skripty T-SQL, které zahrnují více databází při automatické opakování a poskytuje záruky konečné dokončení. Další informace o funkci úlohy elastické databáze najdete v tématu [Elastic jobs](sql-database-elastic-jobs-overview.md).

Tento článek rozšiřuje ukázku v [Začínáme s nástroji Elastic Database](sql-database-elastic-scale-get-started.md). Po dokončení se dozvíte, jak vytvářet a spravovat úlohy, které spravovat skupiny souvisejících databází. Není třeba použít nástroje pro elastické škálování pro využití výhod Elastických úloh.

## <a name="prerequisites"></a>Požadavky
Stáhněte a spusťte [Začínáme s ukázkou nástrojů Elastic Database](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Vytvořte ukázkovou aplikaci pomocí Správce mapování horizontálního oddílu
Zde vytvořit mapy horizontálních oddílů manager společně s několika horizontálními oddíly, za nímž následuje vložení dat do horizontální oddíly. Pokud už máte s horizontálně dělená data v těchto horizontálních oddílech, můžete přeskočit následující kroky a přejít k další části.

1. Sestavit a spustit **Začínáme s nástroji Elastic Database** ukázkovou aplikaci. Postupujte podle pokynů až do kroku 7 v části [stažení a spuštění ukázkové aplikace](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). Na konci kroku 7 najdete v příkazovém řádku následující:

   ![Příkazový řádek](./media/sql-database-elastic-query-getting-started/cmd-prompt.png)

2. V příkazovém okně zadejte "1" a stiskněte klávesu **Enter**. To vytvoří správce mapování horizontálního oddílu a přidá dvě horizontálních oddílů k serveru. Potom zadejte "3" a stiskněte klávesu **Enter**; čtyřikrát opakujte tuto akci. To vloží ukázková data řádků vaše horizontálních oddílů.
3. [Webu Azure portal](https://portal.azure.com) by se měla zobrazit tři nové databáze:

   ![Visual Studio potvrzení](./media/sql-database-elastic-query-getting-started/portal.png)

   Teď vytvoříme vlastní databázi kolekce, která odpovídá všem databázím na mapy horizontálních oddílů. To umožňuje vytvořit a spustit úlohu, která přidá novou tabulku napříč horizontálními oddíly.

Zde by obvykle vytvoříme mapy horizontálních oddílů cílit pomocí **New-AzureSqlJobTarget** rutiny. Databáze správce mapování horizontálních oddílů musí být nastavena jako cílovou databázi a poté je mapy horizontálních oddílů konkrétní zadané jako cíl. Místo toho budeme možné vytvořit výčet všech databází na serveru a přidejte databáze do nové vlastní kolekce s výjimkou hlavní databázi.

## <a name="creates-a-custom-collection-and-add-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Vytvoří vlastní kolekce a přidat všechny databáze na serveru do vlastní kolekce cíle s výjimkou hlavní server.
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
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Vytvoření skriptu T-SQL pro provádění napříč databázemi
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

## <a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Vytvoření úlohy pro spuštění skriptu ve vlastní skupinu databází

   ```
    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="execute-the-job"></a>Provádění úlohy
Následující příkaz powershellu je možné provádět existující úlohy:

Aktualizujte tak, aby odrážely název požadované úlohy, který jste spustili následující proměnnou:

   ```
    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="retrieve-the-state-of-a-single-job-execution"></a>Načíst stav spuštění jedné úlohy
Použijte stejný **Get-AzureSqlJobExecution** rutinu s **metoda IncludeChildren** parametr zobrazit stav podřízených se prováděné úlohy, konkrétně určitý stav pro každé spuštění úlohy každou databázi cílem pro úlohu.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions
   ```

## <a name="view-the-state-across-multiple-job-executions"></a>Zobrazit stav napříč několika se prováděné úlohy
**Get-AzureSqlJobExecution** rutina má více volitelné parametry, které lze použít k zobrazení více spuštění úlohy, filtrovaný pomocí zadaných parametrů. Následující příklad ukazuje některé možné způsoby, jak použít Get-AzureSqlJobExecution:

Načtěte všechny aktivní úlohy nejvyšší úrovně spuštění:

   ```
    Get-AzureSqlJobExecution
   ```

Načtěte všechny nejvyšší úrovně se prováděné úlohy, včetně spuštění neaktivní úlohy:

   ```
    Get-AzureSqlJobExecution -IncludeInactive
   ```

Načtěte všechny podřízené se prováděné úlohy ID zadaná úloha spuštění, včetně spuštění neaktivní úlohy:

   ```
    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren
   ```

Načíst všechny prováděné úlohy vytvořené podle určeného plánu na úlohu kombinaci, včetně neaktivní úlohy:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive
   ```

Načtěte všechny úlohy, které cílí na mapě určený horizontální oddíl, včetně neaktivní úlohy:

   ```
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Načtěte všechny úlohy, které cílí na vlastní kolekce, včetně neaktivní úlohy:

   ```
    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Získání seznamu úkolů prováděné úlohy v rámci konkrétní úlohy spuštění:

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions
   ```

Načtěte podrobnosti provádění úlohy úlohy:

Následující skript prostředí PowerShell slouží k zobrazení podrobností o provádění úloh úkolu, který je zvláště užitečné při ladění selhání spuštění.
   ```
    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution
   ```

## <a name="retrieve-failures-within-job-task-executions"></a>Načíst selhání v rámci úlohy prováděné úlohy
Objekt JobTaskExecution obsahuje vlastnosti pro životní cyklus úloh společně s vlastností zprávy. Pokud provádění úloh úkolu se nezdařilo, životní cyklus vlastnost nastavená na *neúspěšné* a vlastnost Message je nastavená na výslednou zprávu o výjimce a svůj zásobník. Pokud úloha nebyla úspěšná, je důležité k zobrazení podrobností úlohy, které se nezdařila pro danou úlohu.

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

## <a name="waiting-for-a-job-execution-to-complete"></a>Čekání na spuštění úlohy k dokončení
Následující skript prostředí PowerShell slouží k čekání na dokončení úkolu úlohy:

   ```
    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="create-a-custom-execution-policy"></a>Vytvořit zásadu vlastní spuštění
Úlohy elastic Database podporuje vytváření vlastní spuštění zásady, které lze použít při spuštění úlohy.

Zásady spouštění aktuálně umožňují definovat:

* Název: Identifikátor pro zásady spouštění.
* Časový limit úlohy: Celkový čas předtím, než úloha zruší úlohy Elastic Database.
* Počáteční Interval opakování: Interval čekání před prvním opakováním.
* Maximální Interval opakování: Zakončení intervalů opakování, které chcete použít.
* Opakovaný pokus o Interval omezení rychlosti koeficient: Koeficient pro výpočet na další interval mezi opakovanými pokusy.  Se používá tento vzorec: (počátečního intervalu opakování) * Math.Pow – (Interval omezení rychlosti koeficient (), (počet pokusů o) - 2).
* Maximální počet pokusů o: Maximální počet opakovaných pokusů provést v rámci úlohy.

Výchozí zásadu spouštění používá následující hodnoty:

* Název: Výchozí zásadu spouštění
* Časový limit úlohy: 1 týden
* Počátečního intervalu opakování: 100 milisekund
* Maximální Interval opakování: 30 minut
* Koeficient Interval opakování: 2
* Maximální počet pokusů o: 2 147 483 647

Vytvoření zásady požadovaného spouštění:

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
Úlohy elastic Database podporuje požadavky zrušení úlohy.  Pokud úlohy Elastic Database zjistí požadavek na zrušení úlohy se spouští, pokusí se po zastavení úlohy.

Existují dva různé způsoby, že úlohy Elastic Database můžete provádět zrušení:

1. Ruší se právě spouští úlohy: Pokud se zjistí zrušením zatímco úloha je aktuálně spuštěná, zrušení, dojde k pokusu o v rámci aktuálně prováděné aspekt úlohy.  Příklad: Pokud je dlouho probíhající dotazy aktuálně prováděné při pokusu o zrušení, při pokusu o dotaz zrušíte.
2. Zrušení úloh opakovaných pokusů: V případě zrušení zjištění vláknem ovládací prvek předtím, než se spustí úloha pro spuštění, vlákno ovládací prvek zabraňuje spuštění úlohy a deklarovat žádost jako zrušená.

Pokud je úloha zrušení požadováno pro nadřazenou úlohou, žádost o zrušení zachovaný nadřazená úloha a všechny jeho podřízené úlohy.

Chcete-li odeslat žádost o zrušení, použijte **Stop-AzureSqlJobExecution** rutiny a nastavit **JobExecutionId** parametr.

   ```
    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Odstranit úlohu podle názvu a historie úlohy
Úlohy elastic Database podporuje asynchronní odstraňování úloh. Úloha může být označený k odstranění a systému odstranění úlohy a všechny jeho historie úloh po dokončení všech provedení úloh pro úlohu. Systém automaticky nezruší prováděné aktivní úlohy.  

Místo toho Stop-AzureSqlJobExecution je nutné volat se zrušit spuštění aktivní úloha.

K aktivaci úlohy odstranění, použijte **odebrat AzureSqlJob** rutiny a nastavit **JobName** parametru.

   ```
    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
   ```

## <a name="create-a-custom-database-target"></a>Vytvořte vlastní databázi cíl
Vlastní databázi cíle lze definovat v úlohy elastické databáze, které se dají použít pro spuštění přímo nebo zahrnutí v rámci skupiny vlastní databázi. Protože **elastické fondy** přímo, ale neumožňují přes rozhraní API prostředí PowerShell, můžete jednoduše vytvořit vlastní databázi cíle a cílové kolekce vlastní databázi, která zahrnuje všechny databáze ve fondu.

Nastavte následující proměnné tak, aby odrážely informace o požadované databáze:

   ```
    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName
   ```

## <a name="create-a-custom-database-collection-target"></a>Vytvořte vlastní databázi kolekce cíl
Cílové kolekce vlastní databázi lze definovat za účelem povolení spuštění napříč několika cíly definovaných databázových. Po vytvoření databáze skupiny, dá se přidružit k vlastní kolekce cílové databáze.

Nastavte následující proměnné tak, aby odrážely konfiguraci cílů požadované vlastní kolekce:

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="add-databases-to-a-custom-database-collection-target"></a>Přidání databáze do cílové kolekce vlastní databáze
Cíle databáze můžou být spojené s vlastní databázi kolekce cíle můžete vytvořit skupinu databází. Pokaždé, když se vytvoří úloha, která se zaměřuje cílové kolekce vlastní databázi, je rozbalen do cílové databáze přidružený ke skupině v době spuštění.

Přidáte požadovanou databázi do konkrétního vlastní kolekce:

   ```
    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName
   ```

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Zkontrolujte databází v rámci cílové kolekce vlastní databáze
Použití **Get-AzureSqlJobTarget** rutina pro načtení podřízené databáze v cílové kolekci vlastní databázi.

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets
   ```

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Vytvoření úlohy pro spuštění skriptu v cílové kolekci vlastní databáze
Použití **New-AzureSqlJob** rutina pro vytvoření úlohy pro skupinu databází určené cílové kolekce vlastní databázi. Úlohy elastic Database rozšíří úlohy na několika podřízené úlohy, které každý odpovídající databázi přidružené k cílové kolekce vlastní databázi a ujistěte se, že je skript spuštěn na každou databázi. Znovu je důležité, že skripty jsou idempotentní chcete být odolní vůči opakování.

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="data-collection-across-databases"></a>Shromažďování dat napříč databázemi
**Úlohy elastic Database** podporuje provádění dotazu napříč skupinou databází a odesílá výsledky do tabulky zadané databáze. Zobrazení výsledků dotazu z každé databáze ve skutečnosti může být dotazována v tabulce. To poskytuje asynchronní mechanismus při spuštění dotazu v rozsáhlé skupině databází. Případy selhání, jako je jedna z databází není dočasně k dispozici jsou automaticky zpracovány prostřednictvím opakovaných pokusů.

Zadané cílové tabulky se automaticky vytvoří, pokud ještě neexistuje, odpovídající schématu sady výsledků vrácené. Pokud provádění skriptu vrátí více sad výsledků dotazu, odešle úlohy elastické databáze pouze první z nich do zadané cílové tabulky.

Následující skript prostředí PowerShell můžete použít ke spuštění skriptu shromažďování jeho výsledky do zadané tabulky. Tento skript předpokládá, že byl vytvořen skriptu T-SQL, která vytvoří výstup jedna sada výsledků dotazu a je vytvořený cíl kolekce vlastní databázi.

Nastavte následující tak, aby odrážely požadované skriptu, přihlašovací údaje a cíl spuštění:

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

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Vytvoření a spuštění úlohy pro scénáře shromažďování dat
   ```
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Vytvořit plán pro provádění úloh pomocí úloh triggeru
Následující příkaz powershellu je možné vytvořit opakované plán. Tento skript používá intervalem jedna minuta, ale také podporuje parametry - DayInterval, - HourInterval, - MonthInterval a - WeekInterval AzureSqlJobSchedule nový. Je možné vytvořit plány, které jsou spouštěny pouze jednou za předávání - jednorázové.

Vytvoření nového plánu:
   ```
    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime
    Write-Output $schedule
   ```

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Vytvoření aktivační události úlohy má úloha spustit podle časového plánu
Aktivační události úlohy je možné definovat tak mít úlohu provést podle časového plánu. Následující skript prostředí PowerShell slouží k vytvoření aktivační události úlohy.

Nastavte následující proměnné tak, aby odpovídaly požadované úlohy a plán:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
    Write-Output $jobTrigger
   ```

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Odebrání přidružení naplánované o zastavení úlohy spuštění podle plánu
Přestat opakované úlohy spuštění pomocí aktivační události úlohy, je možné odebrat aktivační událost úlohy.
Odebrání aktivační procedury úlohy zastavení úlohy z podle plánu pomocí provádí **odebrat AzureSqlJobTrigger** rutiny.

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
   ```

## <a name="import-elastic-database-query-results-to-excel"></a>Importovat výsledky dotazu elastické databáze do Excelu
 Můžete importovat z výsledků dotazu do Excelového souboru.

1. Spuštění Excelu 2013.
2. Přejděte **Data** pásu karet.
3. Klikněte na tlačítko **z jiných zdrojů** a klikněte na tlačítko **z SQL serveru**.

   ![Import z aplikace Excel z jiných zdrojů](./media/sql-database-elastic-query-getting-started/exel-sources.png)

4. V **Průvodce datovým připojením** zadejte název a přihlašovací údaje serveru. Pak klikněte na tlačítko **Další**.
5. V dialogovém okně **vyberte databázi, která obsahuje data, která chcete**, vyberte **ElasticDBQuery** databáze.
6. Vyberte **zákazníkům** tabulky v zobrazení seznamu a klikněte na tlačítko **Další**. Pak klikněte na tlačítko **Dokončit**.
7. V **Import dat** formuláře, v části **vyberte požadovaný způsob zobrazení dat v sešitu**vyberte **tabulky** a klikněte na tlačítko **OK**.

Všechny řádky z **zákazníkům** tabulky, uložené v různých horizontálních oddílech naplnit do Excelového souboru.

## <a name="next-steps"></a>Další postup
Teď můžete použít funkce dat v Excelu. Použijte připojovací řetězec s názvem serveru, název databáze a přihlašovací údaje pro připojení k databázi elastický dotaz integrace nástroje pro BI a data. Ujistěte se, že systém SQL Server je podporovaný jako zdroj dat pro nástroj. Odkazovat na dotaz na elastic database a externí tabulky, stejně jako jakékoli jiné databáze systému SQL Server a tabulek systému SQL Server, které by se připojit s nástrojem.

### <a name="cost"></a>Náklady
Neexistuje žádné další poplatky za využívání funkce dotazu elastické databáze. V tuto chvíli tato funkce je dostupná jenom na úrovně Premium a pro důležité obchodní informace databáze a elastické fondy jako koncový bod, ale žádné vrstvy služby může být horizontální oddíly.

Informace o cenách najdete v části [podrobnosti o cenách na SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
