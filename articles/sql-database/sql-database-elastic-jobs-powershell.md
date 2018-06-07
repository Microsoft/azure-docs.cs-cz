---
title: Vytvářet a spravovat elastické úlohy pomocí prostředí PowerShell | Microsoft Docs
description: Prostředí PowerShell použít ke správě fondů databáze SQL Azure
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: d1869dd689ef090978c3835e0a16fec82dfb5c05
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648682"
---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>Vytvářet a spravovat úlohy elastické databáze SQL pomocí prostředí PowerShell (preview)

Rozhraní API prostředí PowerShell pro **úlohy elastické databáze** (ve verzi preview), umožňují definovat skupiny databází, na které budou spuštěny skripty. Tento článek ukazuje, jak vytvořit a spravovat **úlohy elastické databáze** pomocí rutin prostředí PowerShell. V tématu [elastické úlohy přehled](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Bezplatná zkušební verze, najdete v části [bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).
* Sadu databází, které jsou vytvořené pomocí nástrojů pro elastické databáze. V tématu [začít pracovat s nástroji elastické databáze](sql-database-elastic-scale-get-started.md).
* Azure Powershell Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* **Elastické databáze úlohy** balíček prostředí PowerShell: najdete v části [úlohy instalace elastické databáze](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Vyberte předplatné Azure
Vyberte předplatné, je třeba Id předplatného (**- SubscriptionId**) nebo název odběru (**- Název_předplatného**). Pokud máte více předplatných můžete spustit **Get-AzureRmSubscription** rutiny a zkopírujte nastavit informace o požadované předplatné od výsledku. Až budete mít informace o vašem předplatném, spusťte následující příkaz pro nastavení toto předplatné jako výchozí, konkrétně cíle pro vytváření a Správa úloh:

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

[Prostředí PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) se doporučuje pro použití při vývoji a spustit skripty prostředí PowerShell pro úlohy elastické databáze.

## <a name="elastic-database-jobs-objects"></a>Objekty úlohy elastické databáze.
V následující tabulce jsou uvedeny na všechny typy objektů z **úlohy elastické databáze** spolu s jeho popis a příslušná rozhraní API prostředí PowerShell.

<table style="width:100%">
  <tr>
    <th>Typ objektu</th>
    <th>Popis</th>
    <th>Související rozhraní API prostředí PowerShell</th>
  </tr>
  <tr>
    <td>Přihlašovací údaj</td>
    <td>Uživatelské jméno a heslo používané k připojení k databázím pro spouštění skriptů nebo aplikace DACPACs. <p>Heslo je zašifrováno před odesláním a ukládání do databáze elastické databáze úlohy.  Heslo se dešifruje pomocí služby úlohy elastické databáze pomocí přihlašovacích údajů vytvořen a odesláno z instalační skript.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Skript</td>
    <td>Skript Transact-SQL, který se má použít pro provedení mezi databázemi.  Skript by měl být vytvořené jako idempotent vzhledem k tomu, že služba bude opakovat akci během provádění skriptu při selhání.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Aplikace na datové vrstvě </a> balíčku má být použita mezi databázemi.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Cílové databáze</td>
    <td>Název databáze a serveru odkazující na Azure SQL Database.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Cíl horizontálního oddílu mapy</td>
    <td>Kombinace cílové databáze a pověření pro použije k určení informací uložených v rámci mapování horizontálních elastické databáze.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    <p>Set-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Cíl vlastní kolekce</td>
    <td>Definované skupiny databází souhrnně používat pro provedení.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Cíl podřízené vlastní kolekce</td>
    <td>Cílové databáze, který se odkazuje z vlastní kolekce.</td>
    <td>
    <p>Add-AzureSqlJobChildTarget</p>
    <p>Remove-AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Úloha</td>
    <td>
    <p>Definice parametrů pro úlohu, která lze použít k aktivaci provádění nebo ke splnění plánu.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>New-AzureSqlJob</p>
    <p>Set-AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Provádění úlohy</td>
    <td>
    <p>Kontejner úlohy, které jsou nutné ke splnění provádění skriptu nebo použití DACPAC k cíli pomocí přihlašovacích údajů pro připojení databáze s chybami zpracovány v souladu zásady spouštění.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Provádění úloh úkolu</td>
    <td>
    <p>Jedné jednotky práce ke splnění úlohy.</p>
    <p>Pokud úkol není schopna úspěšně provést, bude do protokolu výsledné zpráva o výjimce a nové odpovídající úkol bude vytvořen a provést v souladu zásady zadaný spouštění.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Zásady spouštění úlohy</td>
    <td>
    <p>Ovládací prvky úlohy vypršení časových limitů provádění, limity opakování a intervaly mezi opakovanými pokusy.</p>
    <p>Elastické databáze úlohy obsahuje výchozí zásady spouštění úlohy, což způsobí, že v podstatě nekonečné opakování selhání úkolů úloh s exponenciálního omezení rychlosti intervalů mezi každou opakování.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>New-AzureSqlJobExecutionPolicy</p>
    <p>Set-AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Plán</td>
    <td>
    <p>Čas na základě specifikace pro provedení proběhla v opakovaném intervalu nebo najednou.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>New-AzureSqlJobSchedule</p>
    <p>Set-AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Aktivační události úlohy</td>
    <td>
    <p>Mapování mezi úlohy a plán, který chcete provádění aktivační události úlohy podle plánu.</p>
    </td>
    <td>
    <p>New-AzureSqlJobTrigger</p>
    <p>Remove-AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Podporované úlohy elastické databáze skupiny typy
Úloha spustí skripty jazyka Transact-SQL (T-SQL) nebo aplikace DACPACs napříč skupinou databází. Když je úloha odeslána spouštění napříč skupinou databází, úloha "rozšíří" do podřízené úlohy, kde každá má požadovaný spuštění na jedné databáze ve skupině. 

Existují dva typy skupin, které můžete vytvořit: 

* [Mapování horizontálních](sql-database-elastic-scale-shard-map-management.md) skupiny: když je úloha odeslána cílovou mapu horizontálního oddílu, dotazuje mapy horizontálního oddílu k určení jeho aktuální sadu horizontálních oddílů úlohy a potom vytvoří podřízené úlohy pro každý horizontálního oddílu v mapě horizontálního oddílu.
* Vlastní skupiny kolekce: definované vlastní sadu databází. Když úloha cílem vlastní kolekce, vytvoří podřízené úlohy pro každou databázi aktuálně v vlastní kolekci.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Chcete-li nastavit elastické databáze úlohy připojení
Připojení musí být nastavena na úlohy *řízení databáze* před použitím úlohy rozhraní API. Tuto rutinu spustíte aktivuje okno pověření objevil požaduje uživatelské jméno a heslo, které vytvořili při instalaci úlohy elastické databáze. Všechny příklady uvedené v tomto tématu předpokládají, že už jsou hotové tento první krok.

Otevření připojení do úlohy elastické databáze:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Zašifrované přihlašovací údaje v rámci úlohy elastické databáze
Přihlašovací údaje databáze lze vložit do úlohy *řízení databáze* s jeho heslo šifrované. Je nezbytné k ukládání pověření a umožněte úloh provést později, (pomocí plány úloh).

Šifrování funguje prostřednictvím certifikát vytvořen jako součást instalační skript. Instalační skript se vytvoří a odešle certifikát do cloudové služby Azure pro dešifrování uložené šifrovaná hesla. Cloudová služba Azure později ukládá veřejný klíč v rámci úlohy *řízení databáze* což umožňuje rozhraní API prostředí PowerShell nebo Azure rozhraní portálu pro zašifrování zadaného hesla bez nutnosti certifikát, který chcete nainstalovat místně .

Hesla přihlašovací údaje jsou šifrované a zabezpečení od uživatelů s přístupem jen pro čtení k objektům úlohy elastické databáze. Ale je možné, uživatel se zlými úmysly přístup pro čtení a zápis k objektům elastické databáze úlohy extrahování heslo. Přihlašovací údaje jsou navrženy pro opětovné použití mezi jednotlivými spuštěními úlohy. Přihlašovací údaje jsou předány k cílovým databázím při navazování připojení. Aktuálně neexistují žádná omezení na cílové databáze používané pro každý přihlašovací údaje, uživatel se zlými úmysly může přidat cíl databáze pro databázi pod kontrolou uživateli se zlými úmysly. Uživatel může následně spustit úlohu cílení na tuto databázi k získání hesla přihlašovací údaje.

Osvědčené postupy zabezpečení pro úlohy elastické databáze patří:

* Omezit využití rozhraní API důvěryhodným osobám.
* Přihlašovací údaje by měl mít alespoň oprávnění potřebná k provedení úlohy projektu.  Další informace si můžete prohlédnout v rámci to [autorizace a oprávnění](https://msdn.microsoft.com/library/bb669084.aspx) článku na webu MSDN SQL Server.

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Chcete-li vytvořit šifrovaný pověření pro provádění úlohy mezi databázemi
K vytvoření nových přihlašovacích údajů šifrovaná, [ **rutiny Get-Credential** ](https://technet.microsoft.com/library/hh849815.aspx) vyzve k zadání uživatelského jména a hesla, které lze předat [ **rutiny New-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Chcete-li aktualizovat přihlašovací údaje
Při změně hesla, použijte [ **rutiny Set-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential) a nastavte **CredentialName** parametr.

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Chcete-li definovat cíl elastické databáze horizontálního oddílu mapy
K provedení úlohy pro všechny databáze v sadě horizontálního oddílu (vytvořený [klientské knihovny pro elastické databáze](sql-database-elastic-database-client-library.md)), použít mapování horizontálních jako cílové databáze. Tento příklad vyžaduje horizontálně dělené aplikace vytvořené pomocí klientské knihovny pro elastické databáze. V tématu [Začínáme s ukázkou nástroje elastické databáze](sql-database-elastic-scale-get-started.md).

Databáze manager mapy horizontálního oddílu musí být nastavena jako databáze cíl a poté mapy konkrétní horizontálního oddílu musí být zadány jako cíl.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Vytvoření skriptu T-SQL pro provedení mezi databází
Při vytváření skriptů T-SQL pro spuštění, důrazně doporučujeme vytvořit mají být [idempotent](https://en.wikipedia.org/wiki/Idempotence) a odolné proti selhání. Vždy, když dojde k selhání, bez ohledu na klasifikaci selhání spuštění, bude opakovat úlohy elastické databáze provádění skriptu.

Použití [ **rutiny New-AzureSqlJobContent** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent) vytvoříte a uložíte skript pro spuštění a sadu **- ContentName** a **- CommandText** Parametry.

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Vytvořit nový skript ze souboru
Pokud skriptu T-SQL je definována v souboru, můžete to použijte import skript:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Chcete-li aktualizovat skriptu T-SQL pro provedení mezi databázemi
Tento skript prostředí PowerShell aktualizuje text příkazu T-SQL pro existující skript.

Nastavte následující proměnné tak, aby odrážela definici skriptu požadované nastavení:

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>Aktualizovat definici do existujícího skriptu
    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Chcete-li vytvořit úlohu pro spuštění skriptu napříč horizontálního oddílu mapy
Tento skript prostředí PowerShell spustí úlohu pro spuštění skriptu mezi každou horizontálního oddílu v mapě horizontálního oddílu služby elastické škálování.

Nastavte následující proměnné tak, aby odrážela požadované skriptu a cíle:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>K provedení úlohy
Tento skript PowerShell spouští stávající úloze:

Aktualizujte tak, aby odrážela název požadované úlohy, který provedli následující proměnnou:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Při načítání stavu provádění jedné úlohy
Použití [ **rutiny Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution) a nastavte **JobExecutionId** parametr, pokud chcete zobrazit stav provádění úlohy.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Použijte stejný **Get-AzureSqlJobExecution** rutiny s **metoda IncludeChildren** parametr, pokud chcete zobrazit stav podřízených spuštění úlohy, konkrétně určitém stavu pro každé spuštění úlohy každou databázi cílem úlohy.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Pokud chcete zobrazit stav mezi jednotlivými spuštěními více úloh
[ **Rutiny Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) má více volitelné parametry, které lze použít k zobrazení více spuštění úlohy, filtrovaný pomocí zadané parametry. Následující ukazuje některé možné způsoby, jak používat Get-AzureSqlJobExecution:

Načtěte všechny aktivní nejvyšší úrovně úloha spuštění:

    Get-AzureSqlJobExecution

Načtení všech spuštěních nejvyšší úrovně úlohy, včetně spuštěních neaktivní úlohy:

    Get-AzureSqlJobExecution -IncludeInactive

Načtěte všechny podřízené úlohy spuštěních zadaná úloha spuštění ID, včetně spuštěních neaktivní úlohy:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

Načíst všechny úlohy spuštěních vytvořený plán / úlohy kombinaci, včetně neaktivní úlohy:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Načtěte všechny úlohy cílení na mapě zadaný horizontálního oddílu, včetně neaktivní úlohy:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Načtěte všechny úlohy cílení na vlastní kolekce, včetně neaktivní úlohy:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Načtení seznamu spuštěních úloh úlohy v rámci provedení určité úlohy:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Načtěte podrobnosti o provádění úkolů úlohy:

Následující skript prostředí PowerShell slouží k zobrazení podrobností o provádění úloh úkolu, který je zvláště užitečná při ladění selhání spuštění.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>Načtení selhání v rámci úlohy spuštěních úloh
**JobTaskExecution objekt** zahrnuje vlastnost pro životní cyklus úlohy společně s vlastností zpráv. Pokud se nezdařilo provádění úloh úkolu, vlastnost životního cyklu bude nutné nastavit *se nezdařilo* a vlastnosti zprávy se nastaví výsledné zpráva o výjimce a jeho zásobníku. Pokud úloha nebyla úspěšná, je důležité k zobrazení podrobností úlohy, které se nezdařilo pro danou úlohu.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Čekání na dokončení provedení úlohy
Následující skript prostředí PowerShell umožňuje počkejte na dokončení úlohy úlohy:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Vytvořit zásadu vlastní spuštění
Elastické databáze úlohy podporuje vytváření vlastní provádění zásad, které mohou být použity při spouštění úloh.

Zásady spouštění aktuálně povolit pro definování:

* Název: Identifikátor pro zásady spouštění.
* Časový limit úlohy: Celkový čas před úlohy budou zrušeny úlohami elastické databáze.
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

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Aktualizovat zásady vlastní spuštění
Aktualizujte zásady spouštění požadované aktualizace:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy

## <a name="cancel-a-job"></a>Zrušení úlohy
Elastické databáze úlohy podporuje požadavků na zrušení úloh.  Pokud elastické databáze úlohy zjistí žádost o zrušení úlohy se spouští, se ho pokusí zastavit úlohu.

Že úlohy elastické databáze můžete provést zrušení dvěma různými způsoby:

1. Zrušit aktuálně spuštěných úloh: Pokud zrušení se zjistí, zatímco úloha je aktuálně spuštěna, zrušení se pokusí v rámci aktuálně prováděné aspekt úlohy.  Například: Pokud je aktuálně provést při pokusu o zrušení dlouho spuštěných dotazu, bude pokus o dotaz zrušíte.
2. Zrušení úloh opakování: v případě zrušení zjištění vlákno řízení předtím, než se spustí úloha pro spuštění, bude vlákno řízení vyhnout, spouští se úloha a deklarovat požadavek, protože došlo ke zrušení.

Pokud zrušení úlohy je požadováno pro nadřazené úloze, bude pro nadřazené úloze a všechny jeho podřízené úlohy dodržet žádost o zrušení.

Odeslat žádost o zrušení, použijte [ **rutinu Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) a nastavte **JobExecutionId** parametr.

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>K odstranění úlohy a úlohy historie asynchronně
Elastické databáze úlohy podporuje asynchronní odstranění úloh. Úloha může být označený k odstranění a systém bude odstranění úlohy a všechny jeho historie úlohy po dokončení všech spuštěních úloh pro úlohu. Systém nebude automaticky zrušit spuštěních aktivní úlohy.  

Vyvolání [ **Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) zrušit spuštěních aktivní úlohy.

Chcete-li aktivovat odstranění úlohy, použijte [ **rutinu Remove-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/remove-azuresqljob) a nastavte **JobName** parametr.

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>Chcete-li vytvořit cíl vlastní databázi
Můžete definovat vlastní databázi cíle pro přímé spouštění nebo pro zahrnutí do skupiny vlastní databázi. Například protože **elastické fondy** není dosud podporován přímo pomocí rozhraní API prostředí PowerShell, můžete vytvořit vlastní databázi cíle a cílové kolekce vlastní databázi, která zahrnuje všechny databáze ve fondu.

Nastavte následující proměnné tak, aby odrážela informace o požadované databázi:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Chcete-li vytvořit cíl kolekce vlastní databázi
Použití [ **New-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) rutiny můžete definovat vlastní databázi kolekce cíl povolit spuštění v rámci více definovaných databázových cílů. Po vytvoření skupiny databáze, databáze může být přidružený k cíli vlastní kolekce.

Nastavte následující proměnné tak, aby odrážela konfigurace cílového požadovanou vlastní kolekce:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Přidání databází do kolekce cíl vlastní databázi
Přidání databáze pro použití konkrétní vlastní kolekce [ **přidat AzureSqlJobChildTarget** ](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget) rutiny.

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Zkontrolujte databází v rámci kolekce cíl vlastní databázi
Použití [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) rutiny načíst podřízené databází v rámci kolekce cíl vlastní databázi. 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Vytvořit úlohu pro spuštění skriptu mezi cílovou kolekci vlastní databázi
Použití [ **New-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) rutiny vytvořit úlohu pro skupinu databází definované cílovou kolekci vlastní databázi. Elastické databáze úlohy se úloha rozšířit více podřízených úloh, každou odpovídající databázi přidruženého cílové kolekce vlastní databázi a ujistěte se, že skript se spustí na každou databázi. Znovu je důležité, aby skripty se idempotent chcete být odolní vůči opakování.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Shromažďování dat mezi databázemi
Úlohu můžete použít k provedení dotazu napříč skupinou databází a odešlou výsledky do určité tabulky. V tabulce můžete položit dotaz na ve skutečnosti zobrazíte výsledky dotazu z každé databáze. To poskytuje asynchronní metodu při spuštění dotazu mezi mnoha databázemi. Neúspěšných pokusů o přihlášení se zpracovávají automaticky prostřednictvím opakování.

Zadané cílové tabulky se automaticky vytvoří, pokud ještě neexistuje. Nová tabulka odpovídá schématu vrácené výsledné sady. Pokud skript vrátí více sad výsledků dotazu, odeslat úlohy elastické databáze pouze první do cílové tabulky.

Následující skript PowerShell spouští skript a shromažďuje své výsledky do zadané tabulky. Tento skript předpokládá, že skriptu T-SQL byl vytvořen který výstupy jedné sadě výsledků a vytvořený cíl kolekce vlastní databázi.

Tento skript používá [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) rutiny. Nastavte parametry skriptu, přihlašovací údaje a provádění cíle:

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Vytvoření a spuštění úlohy scénáře kolekce dat
Tento skript používá [ **Start-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/start-azuresqljobexecution) rutiny.

    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Při plánování provádění aktivační události úlohy
Následující skript prostředí PowerShell můžete použít k vytvoření plánu opakování. Tento skript používá intervalu minutu, ale [ **New-AzureSqlJobSchedule** ](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule) také podporuje – DayInterval, - HourInterval, - MonthInterval a - WeekInterval parametry. Plány, které jsou spouštěny pouze jednou lze vytvořit pomocí předávání - jednorázově.

Vytvoření nového plánu:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>K aktivaci provést podle časového plánu úlohy
Aktivační události úlohy lze definovat za účelem mít úlohu provést podle časového plánu. Následující skript prostředí PowerShell slouží k vytvoření aktivační události úlohy.

Použití [New-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) a nastavte následující proměnné tak, aby odpovídaly požadované úlohy a plán:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Odebrání naplánované přidružení o zastavení úlohy ve spouštění podle plánu.
Odebrání ze opakovaném provádění úlohy prostřednictvím aktivační události úlohy, můžete odebrat aktivační události úlohy. Odebrat aktivační události úlohy zastavení úlohy z se spouští podle plánu pomocí [ **rutinu Remove-AzureSqlJobTrigger**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Načíst aktivační události úlohy vázána na časového plánu
Následující skript prostředí PowerShell slouží k získání a zobrazení aktivačních událostí úlohy registrované na konkrétní časového plánu.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Načíst aktivační události úlohy vázána na úlohu
Použití [Get-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) můžete získat a zobrazit plány obsahující úlohu registrované.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Vytvořit aplikace na datové vrstvě (DACPAC) pro spuštění v rámci databáze
Vytvoření DACPAC naleznete v tématu [aplikace datové vrstvy](https://msdn.microsoft.com/library/ee210546.aspx). Chcete-li nasadit DACPAC, použijte [rutiny New-AzureSqlJobContent](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent). DACPAC musí být přístupný pro službu. Doporučuje se nahrát vytvořený DACPAC do služby Azure Storage a vytvoření [sdíleného přístupového podpisu](../storage/common/storage-dotnet-shared-access-signature-part-1.md) pro DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Aktualizace aplikace na datové vrstvě (DACPAC) pro provedení mezi databázemi
Existující DACPACs zaregistrován v rámci úlohy elastické databáze můžete aktualizovat tak, aby odkazoval na nový identifikátory URI. Použití [ **rutiny Set-AzureSqlJobContentDefinition** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition) aktualizovat identifikátor URI DACPAC na existujícím zaregistrován DACPAC:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Chcete-li vytvořit úlohu použít aplikaci na datové vrstvě (DACPAC) mezi databázemi
Po vytvoření DACPAC v rámci úlohy elastické databáze, lze vytvořit úlohu pro použití DACPAC napříč skupinou databází. Následující skript prostředí PowerShell slouží k vytvoření úlohy DACPAC v kolekci vlastních databází:

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
