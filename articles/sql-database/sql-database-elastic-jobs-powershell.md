---
title: Vytvoření a správa elastických úloh pomocí Powershellu | Dokumentace Microsoftu
description: Prostředí PowerShell použít ke správě fondů Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: powershell
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 06/14/2018
ms.openlocfilehash: de395dc4f862e57030fba1d77de78eabe44a3da8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53278453"
---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>Vytvoření a správa elastických úloh SQL Database pomocí Powershellu (preview)


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


Rozhraní API prostředí PowerShell pro **úlohy elastické databáze** (ve verzi preview) umožňují definovat skupiny databází, u kterých se spustí skripty. Tento článek popisuje, jak vytvořit a spravovat **úlohy elastické databáze** pomocí rutin prostředí PowerShell. Zobrazit [přehled úloh Elastic](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Bezplatná zkušební verze, najdete v části [zkušební verze na měsíc zdarma](https://azure.microsoft.com/pricing/free-trial/).
* Sada databáze vytvořené s nástroji Elastic Database. Zobrazit [Začínáme s nástroji Elastic Database](sql-database-elastic-scale-get-started.md).
* Azure PowerShell Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* **Úlohy elastic Database** balíčků v Powershellu: Zobrazit [úlohy instalace elastické databáze](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Vyberte své předplatné Azure
Vyberte předplatné, je třeba Id předplatného (**- SubscriptionId**) nebo název předplatného (**- SubscriptionName**). Pokud máte více předplatných, můžete spustit **Get-AzureRmSubscription** rutiny a kopírovat nastavit informace o požadované předplatné z výsledku. Jakmile budete mít informace o vašem předplatném, spusťte následující rutiny můžete nastavit toto předplatné jako výchozí, konkrétně cíl pro vytváření a Správa úloh:

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

[Prostředí PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) se doporučuje pro využití pro vývoj a spouštění skriptů prostředí PowerShell pro úlohy elastické databáze.

## <a name="elastic-database-jobs-objects"></a>Objekty úlohy elastické databáze
V následující tabulce jsou uvedeny na všechny typy objektů z **úlohy elastické databáze** spolu s jeho popis a relevantní rozhraní API Powershellu.

<table style="width:100%">
  <tr>
    <th>Typ objektu</th>
    <th>Popis</th>
    <th>Související rozhraní API prostředí PowerShell</th>
  </tr>
  <tr>
    <td>Přihlašovací údaj</td>
    <td>Uživatelské jméno a heslo mají používat při připojování k databázím pro spouštění skriptů nebo aplikací DACPACs. <p>Heslo je zašifrováno před odesláním a uložení do databáze úlohy Elastic Database.  Úlohy Elastic Database služby přes přihlašovací údaje, které vytvoří a nahraje ze skriptu instalace je dešifrovat heslo.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Skript</td>
    <td>Příkaz Transact-SQL skript, který má použít pro spuštění napříč databázemi.  Skript by měl být vytvořen jako idempotentní, protože služba bude opakovat akci během provádění skriptu při selhání.
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
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Aplikace datové vrstvy </a> balíček, který chcete použít napříč databázemi.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Cílové databáze</td>
    <td>Název databáze a serveru přejděte ke službě Azure SQL Database.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Cíl mapy horizontálních oddílů</td>
    <td>Kombinace cílové databáze a přihlašovacích údajů se použije k určení informací uložených v rámci mapy horizontálních oddílů Elastic Database.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    <p>Set-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Cíl vlastní shromažďování</td>
    <td>Definované skupiny databází společně použít pro spuštění.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Vlastní kolekce podřízených cíl</td>
    <td>Cílové databáze, který se odkazuje z vlastní kolekce.</td>
    <td>
    <p>Add-AzureSqlJobChildTarget</p>
    <p>Remove-AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Úloha</td>
    <td>
    <p>Definice parametrů pro úlohu, která lze použít, chcete aktivovat provedení té nebo ke splnění plánu.</p>
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
    <p>Kontejner úloh, potřebnou ke splnění provádění skriptu nebo aplikování DACPAC k cíli pomocí přihlašovacích údajů pro připojení k databázi s chybami zpracování podle zásadu spouštění.</p>
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
    <p>Jednu jednotku práce ke splnění úlohy.</p>
    <p>Pokud úlohu není možné úspěšně provést, se zapíše výsledný zpráva výjimky a novou úlohu odpovídající bude vytvořena a provedeny podle zadaného spuštění zásad.</p></p>
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
    <p>Ovládací prvky úlohy vypršení časového limitu pro spuštění, limitu opakování a intervalů mezi opakovanými pokusy.</p>
    <p>Úlohy elastic Database zahrnuje výchozí zásadu spouštění úlohy, což způsobí, že v podstatě nekonečné opakované pokusy o selhání úkolů úlohy pomocí exponenciálního omezení rychlosti intervalů mezi opakováními.</p>
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
    <p>Čas na základě specifikace pro spuštění k provedení opakované interval nebo najednou.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>New-AzureSqlJobSchedule</p>
    <p>Set-AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Aktivuje úlohu</td>
    <td>
    <p>Mapování mezi úlohu a plán, který chcete spuštění aktivační události úlohy podle plánu.</p>
    </td>
    <td>
    <p>New-AzureSqlJobTrigger</p>
    <p>Remove-AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Podporované úlohy elastické databáze seskupení typů
Úloha spustí skriptů Transact-SQL (T-SQL) nebo aplikaci DACPACs napříč skupinou databází. Když je úloha odeslána provádět v rámci skupiny databází, úloha "rozbalí" o úlohách podřízený, kde každá provádí požadované spuštění na jednu databázi ve skupině. 

Existují dva typy skupin, které můžete vytvořit: 

* [Mapy horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md) skupiny: Když je úloha odeslána cílovou mapy horizontálních oddílů, úloha dotazuje mapy horizontálních oddílů k určení jeho aktuální sadu horizontálních oddílů a pak vytvoří podřízené úlohy pro každý horizontální oddíl v mapě horizontálních oddílů.
* Vlastní skupiny kolekcí: Vlastní definovanou sadu databází. Když projekt cílí na vlastní kolekce, vytvoří podřízené úlohy pro každou databázi aktuálně do vlastní kolekce.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Chcete-li nastavit Elastic Database úlohy připojení
Připojení musí být nastavena na úlohy, které *databáze správy* před použitím úlohy, které rozhraní API. Tuto rutinu spustíte aktivuje okno přihlašovacích údajů překryvné požadujícím uživatelské jméno a heslo vytvořené při instalaci úlohy elastické databáze. Všechny příklady v tomto tématu se předpokládá, že tento první krok už jsou hotové.

Otevření připojení do úlohy elastické databáze:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Zašifrované přihlašovací údaje v rámci úlohy elastické databáze
Databázová pověření mohou být zařazeny do úlohy *databáze správy* s jeho heslo šifrovaná. Je potřeba ukládat přihlašovací údaje pro aktivaci úlohy má být proveden později, (pomocí plánů úloh).

Šifrování funguje prostřednictvím certifikát vytvořenou jako součást instalační skript. Instalační skript vytvoří a nahraje certifikát do cloudové služby Azure pro dešifrování uložené šifrovaná hesla. Azure Cloud Service později uloží veřejný klíč v rámci úlohy *databáze správy* umožňující rozhraní API prostředí PowerShell nebo Azure portal rozhraní k zašifrování zadaného hesla bez vyžadování certifikátů místně nainstalovaný .

Hesla přihlašovací údaje jsou zašifrované a zabezpečené od uživatelů s přístupem jen pro čtení k objektům úlohy elastické databáze. Ale je možné, uživatel se zlými úmysly s přístupem pro čtení a zápis na objekty úlohy Elastic Database extrahovat hesla. Přihlašovací údaje jsou navrženy pro opětovné používání pro spuštění úlohy. Přihlašovací údaje jsou předány do cílové databáze při navazování připojení. Aktuálně neexistují žádná omezení na cílové databáze používané pro každý přihlašovacích údajů, uživatel se zlými úmysly může přidat cíl databáze pro databázi v rámci řízení uživateli se zlými úmysly. Uživatel může následně spustit úlohu, která cílí na tuto databázi k získání hesla přihlašovací údaje.

Osvědčené postupy zabezpečení pro úlohy elastické databáze patří:

* Omezte možnost používat rozhraní API na důvěryhodné osoby.
* Přihlašovací údaje by měl mít alespoň oprávnění potřebná k provedení úlohy.  Další informace můžete zobrazit v rámci této [autorizace a oprávnění](https://msdn.microsoft.com/library/bb669084.aspx) článku na webu MSDN SQL Server.

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Chcete-li vytvořit šifrované přihlašovací údaje pro provádění úlohy napříč databázemi
K vytvoření nových přihlašovacích údajů šifrované, [ **rutiny Get-Credential** ](/powershell/module/microsoft.powershell.security/get-credential) vyzve k zadání uživatelského jména a hesla, které mohou být předány [ **rutiny New-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Chcete-li aktualizovat přihlašovací údaje
Při změně hesla, použijte [ **rutiny Set-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential) a nastavit **CredentialName** parametru.

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Chcete-li definovat cíl mapy horizontálních oddílů elastické databáze
K provedení úlohy pro všechny databáze v sadě horizontálních oddílů (vytvořené pomocí [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md)), použijte mapu horizontálního oddílu jako cílové databáze. Tento příklad vyžaduje horizontálně dělené aplikace vytvořené využitím klientské knihovny pro elastické databáze. Zobrazit [Začínáme s ukázkou nástrojů Elastic Database](sql-database-elastic-scale-get-started.md).

Databáze správce mapování horizontálních oddílů musí být nastavena jako cílovou databázi a potom mapy horizontálních oddílů konkrétní musí být zadán jako cíl.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Vytvoření skriptu T-SQL pro provádění napříč databázemi
Při vytváření skriptů T-SQL pro spuštění, důrazně doporučujeme vytvářet mohly [idempotentní](https://en.wikipedia.org/wiki/Idempotence) a odolné proti chybám. Úlohy elastic Database bude opakovat spuštění skriptu při každém spuštění dojde k selhání, bez ohledu na to klasifikace selhání.

Použití [ **rutiny New-AzureSqlJobContent** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent) vytvoříte a uložíte skript pro spuštění a nastavte **- ContentName** a **- CommandText** Parametry.

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
Pokud skript T-SQL je definován v rámci souboru, to pomocí importujte skript:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Chcete-li aktualizovat pro spuštění skriptu T-SQL napříč databázemi
Tento skript Powershellu aktualizuje text příkazu T-SQL pro existující skript.

Nastavte následující proměnné tak, aby odrážely definici skriptu požadované nastavení:

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

### <a name="to-update-the-definition-to-an-existing-script"></a>Aktualizovat definici existující skript
    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>K vytvoření úlohy pro spuštění skriptu napříč horizontálními oddíly mapy
Tento skript Powershellu spustí úlohu pro spuštění skriptu v jednotlivých horizontálních oddílů v mapě horizontálních oddílů elastické škálování.

Nastavte následující proměnné tak, aby odrážely požadované skriptu a cíl:

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
Tento skript prostředí PowerShell spustí existující úlohy:

Aktualizujte tak, aby odrážely název požadované úlohy, který jste spustili následující proměnnou:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Načíst stav spuštění jedné úlohy
Použití [ **rutiny Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution) a nastavit **JobExecutionId** parametr, chcete-li zobrazit stav provádění úlohy.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Použijte stejný **Get-AzureSqlJobExecution** rutinu s **metoda IncludeChildren** parametr zobrazit stav podřízených se prováděné úlohy, konkrétně určitý stav pro každé spuštění úlohy každou databázi cílem pro úlohu.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Chcete-li zobrazit stav napříč několika se prováděné úlohy
[ **Rutiny Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) má více volitelné parametry, které lze použít k zobrazení více spuštění úlohy, filtrovaný pomocí zadaných parametrů. Následující příklad ukazuje některé možné způsoby, jak použít Get-AzureSqlJobExecution:

Načtěte všechny aktivní úlohy nejvyšší úrovně spuštění:

    Get-AzureSqlJobExecution

Načtěte všechny nejvyšší úrovně se prováděné úlohy, včetně spuštění neaktivní úlohy:

    Get-AzureSqlJobExecution -IncludeInactive

Načtěte všechny podřízené se prováděné úlohy ID zadaná úloha spuštění, včetně spuštění neaktivní úlohy:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

Načíst všechny prováděné úlohy vytvořené podle určeného plánu na úlohu kombinaci, včetně neaktivní úlohy:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Načtěte všechny úlohy, které cílí na mapě určený horizontální oddíl, včetně neaktivní úlohy:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Načtěte všechny úlohy, které cílí na vlastní kolekce, včetně neaktivní úlohy:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Získání seznamu úkolů prováděné úlohy v rámci konkrétní úlohy spuštění:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Načtěte podrobnosti provádění úlohy úlohy:

Následující skript prostředí PowerShell slouží k zobrazení podrobností o provádění úloh úkolu, který je zvláště užitečné při ladění selhání spuštění.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>K načtení selhání v rámci úlohy prováděné úlohy
**JobTaskExecution objekt** obsahují vlastnost pro životní cyklus úloh společně s vlastností zprávy. Pokud provádění úloh úkolu se nezdařilo, vlastnost životní cyklus bude nastavena na *neúspěšné* a nastaví vlastnost zprávy na výslednou zprávu o výjimce a svůj zásobník. Pokud úloha nebyla úspěšná, je důležité k zobrazení podrobností úlohy, které se nezdařila pro danou úlohu.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Čekání na spuštění úlohy k dokončení
Následující skript prostředí PowerShell slouží k čekání na dokončení úkolu úlohy:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Vytvořit zásadu vlastní spuštění
Úlohy elastic Database podporuje vytváření vlastní spuštění zásady, které lze použít při spuštění úlohy.

Zásady spouštění aktuálně umožňují definovat:

* Název: Identifikátor pro zásady spouštění.
* Časový limit úlohy: Celkový čas předtím, než úloha se zruší úlohy Elastic Database.
* Interval opakování počáteční: Interval čekání před prvním opakováním.
* Maximální počet opakovaných Interval: Zakončení intervalů opakování používat.
* Koeficient omezení rychlosti Interval opakování: Koeficient pro výpočet na další interval mezi opakovanými pokusy.  Se používá tento vzorec: (Počáteční Interval opakování) * Math.Pow – (Interval omezení rychlosti koeficient (), (počet opakování) - 2). 
* Maximální počet pokusů: Maximální počet opakovaných pokusů provést v rámci úlohy.

Výchozí zásadu spouštění používá následující hodnoty:

* Název: Výchozí zásadu spouštění
* Časový limit úlohy: 1 týden
* Interval opakování počáteční:  100 milisekund
* Maximální počet opakovaných Interval: 30 minut
* Koeficient Interval opakování: 2
* Maximální počet pokusů: 2,147,483,647

Vytvoření zásady požadovaného spouštění:

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
Úlohy elastic Database podporuje požadavky zrušení úloh.  Pokud úlohy Elastic Database zjistí požadavek na zrušení úlohy se spouští, se pokusí o zastavení úlohy.

Existují dva různé způsoby, že úlohy Elastic Database můžete provádět zrušení:

1. Zrušit běžící úlohy: Pokud zrušením se zjistí, zatímco úloha je aktuálně spuštěná, zrušení se provedou v rámci aktuálně prováděné aspekt úlohy.  Příklad: Pokud je dlouho probíhající dotazy aktuálně prováděné při pokusu o zrušení, bude pokus o zrušení dotazu.
2. Ruší se úloha opakování: Pokud vlákno ovládací prvek zjistí zrušením předtím, než se spustí úloha pro spuštění, vlákno ovládací prvek se zabránilo spouštění úlohy a deklarovat žádost jako zrušená.

Pokud je požadováno zrušení úlohy k nadřazené úloze se žádost o zrušení dodržet nadřazená úloha a všechny jeho podřízené úlohy.

Pokud chcete odeslat žádost o zrušení, použijte [ **rutinu Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) a nastavit **JobExecutionId** parametru.

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>K odstranění úlohy a asynchronně historie úlohy
Úlohy elastic Database podporuje asynchronní odstraňování úloh. Úloha může být označený k odstranění a systém se odstranění úlohy a všechny jeho historie úloh po dokončení všech provedení úloh pro úlohu. Systém nebude automaticky zrušit spuštění aktivní úloha.  

Vyvolání [ **Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) zrušit spuštění aktivní úloha.

K aktivaci úlohy odstranění, použijte [ **rutiny Remove-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/remove-azuresqljob) a nastavit **JobName** parametru.

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>Chcete-li vytvořit cíl vlastní databáze
Můžete definovat vlastní databázi cíle pro přímé spouštění nebo zahrnutí v rámci skupiny vlastní databázi. Například protože **elastické fondy** jsou ještě není podporovaný přímo pomocí rozhraní API prostředí PowerShell, můžete vytvořit vlastní databázi cíle a cílové kolekce vlastní databázi, která zahrnuje všechny databáze ve fondu.

Nastavte následující proměnné tak, aby odrážely informace o požadované databáze:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Chcete-li vytvořit cílové kolekce vlastní databáze
Použití [ **New-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) rutiny k definování vlastní databázi kolekce cíl k povolení spuštění napříč několika cíly definovaných databázových. Po vytvoření skupiny databáze, databáze může být přidružený k cíli vlastní kolekce.

Nastavte následující proměnné tak, aby odrážely konfiguraci cílů požadované vlastní kolekce:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Přidání databáze do cílové kolekce vlastní databáze
Chcete-li přidat databázi použít konkrétní vlastní kolekce [ **přidat AzureSqlJobChildTarget** ](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget) rutiny.

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Zkontrolujte databází v rámci cílové kolekce vlastní databáze
Použití [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) rutina pro načtení podřízené databáze v cílové kolekci vlastní databázi. 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Vytvoření úlohy pro spuštění skriptu v cílové kolekci vlastní databáze
Použití [ **New-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) rutina pro vytvoření úlohy pro skupinu databází určené cílové kolekce vlastní databázi. Úlohy elastic Database bude rozšíření úlohy do více podřízené úlohy, které každý odpovídající databázi přidružené k cílové kolekce vlastní databázi a ujistěte se, že je skript spuštěn na každou databázi. Znovu je důležité, že skripty jsou idempotentní chcete být odolní vůči opakování.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Shromažďování dat napříč databázemi
Úlohu můžete použít k provedení dotazu v rámci skupiny databází a odešlou výsledky do určité tabulky. Zobrazení výsledků dotazu z každé databáze ve skutečnosti může být dotazována v tabulce. To poskytuje asynchronní metodě k provedení dotazu v rozsáhlé skupině databází. Neúspěšných pokusů o přihlášení jsou automaticky zpracovány prostřednictvím opakovaných pokusů.

Zadané cílové tabulky se automaticky vytvoří, pokud ještě neexistuje. Nová tabulka odpovídá schématu sady výsledků dotazu vrácená. Pokud skript vrátí více sad výsledků dotazu, odeslat úlohy elastické databáze pouze první do cílové tabulky.

Následující skript prostředí PowerShell spustí skript a shromažďuje výsledky do zadané tabulky. Tento skript předpokládá, že skript T-SQL se vytvořila která vytvoří výstup jedna sada výsledků dotazu a vytvoření cílové kolekce vlastní databázi.

Tento skript používá [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) rutiny. Nastavte parametry pro skript, přihlašovací údaje a cíl spuštění:

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Vytvoření a spuštění úlohy pro scénáře shromažďování dat
Tento skript používá [ **Start AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/start-azuresqljobexecution) rutiny.

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

## <a name="to-schedule-a-job-execution-trigger"></a>Naplánování spuštění aktivační události úlohy
Následující skript prostředí PowerShell slouží k vytvoření plánu opakování. Tento skript používá minutovém intervalu, ale [ **New-AzureSqlJobSchedule** ](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule) také podporuje parametry - DayInterval, - HourInterval, - MonthInterval a - WeekInterval. Je možné vytvořit plány, které jsou spouštěny pouze jednou za předávání - jednorázové.

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
Aktivační události úlohy je možné definovat tak mít úlohu provést podle časového plánu. Následující skript prostředí PowerShell slouží k vytvoření aktivační události úlohy.

Použití [New-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) a nastavte následující proměnné tak, aby odpovídaly požadované úlohy a plán:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Chcete-li odebrat naplánované přidružení o zastavení úlohy spuštění podle plánu
Přestat opakované úlohy spuštění pomocí aktivační události úlohy, je možné odebrat aktivační událost úlohy. Odebrání aktivační procedury úlohy zastavení úlohy z podle plánu pomocí provádí [ **rutiny Remove-AzureSqlJobTrigger**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Načtení aktivační události úlohy vázán na časový plán
Následující skript prostředí PowerShell slouží k získání a zobrazení úloh aktivačních událostí zaregistrované u konkrétního časového plánu.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Načtení aktivační události úlohy vázán na úlohu
Použití [Get-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) můžete získat a zobrazit plány obsahující úlohu registrovaný.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Vytvoření aplikace datové vrstvy (DACPAC) pro spuštění napříč databázemi
Vytvoření DACPAC najdete v tématu [aplikace datové vrstvy](https://msdn.microsoft.com/library/ee210546.aspx). Chcete-li nasadit DACPAC, použijte [rutiny New-AzureSqlJobContent](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent). DACPAC musí být přístupná pro službu. Doporučuje se nahrání vytvořený balíčku DACPAC do služby Azure Storage a vytvoření [sdílený přístupový podpis](../storage/common/storage-dotnet-shared-access-signature-part-1.md) pro DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Aktualizace aplikace datové vrstvy (DACPAC) pro spuštění napříč databázemi
Existující DACPACs zaregistrován v rámci úlohy Elastic Database můžete aktualizovat tak, aby odkazoval na nové identifikátory URI. Použití [ **rutiny Set-AzureSqlJobContentDefinition** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition) aktualizace identifikátoru URI balíčku DACPAC do stávajícího zaregistrovaný DACPAC:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Vytvořte úlohu chcete-li použít aplikace datové vrstvy (DACPAC) napříč databázemi
Po vytvoření DACPAC v rámci úlohy Elastic Database můžete používat DACPAC napříč skupinou databází vytvořen projektu. Následující skript prostředí PowerShell slouží k vytvoření balíčku DACPAC úlohy v kolekci vlastních databází:

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
