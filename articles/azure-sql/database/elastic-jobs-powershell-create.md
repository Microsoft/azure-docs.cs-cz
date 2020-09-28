---
title: Vytvoření agenta elastické úlohy pomocí prostředí PowerShell (Preview)
description: Zjistěte, jak vytvořit agenta elastických úloh pomocí PowerShellu.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 03/13/2019
ms.openlocfilehash: aaf749708b49c57d08a63581f3d911b04aba2103
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2020
ms.locfileid: "91408663"
---
# <a name="create-an-elastic-job-agent-using-powershell-preview"></a>Vytvoření agenta elastické úlohy pomocí prostředí PowerShell (Preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Elastické úlohy (Preview)](job-automation-overview.md#elastic-database-jobs-preview) umožňují paralelní spuštění jednoho nebo více skriptů jazyka Transact-SQL (T-SQL) paralelně napříč mnoha databázemi.

V tomto kurzu se seznámíte s kroky potřebnými ke spuštění dotazu napříč více databázemi:

> [!div class="checklist"]
> * Vytvoření agenta elastických úloh
> * Vytvoření přihlašovacích údajů k úloze, aby úlohy mohly na svých cílech spouštět skripty
> * Definice cílů (servery, elastické fondy, databáze, mapy horizontálních oddílů), pro které chcete úlohu spustit
> * Vytvoření přihlašovacích údajů v oboru databáze v cílových databázích, aby se agent mohl připojit a spouštět úlohy
> * Vytvoření úlohy
> * Přidání kroků do úlohy
> * Spuštění provádění úlohy
> * Monitorování úlohy

## <a name="prerequisites"></a>Požadavky

Upgradovaná verze úlohy elastické databáze má novou sadu rutin PowerShellu pro použití během migrace. Tyto nové rutiny převádějí všechna vaše stávající přihlašovací údaje úlohy, cíle (včetně databází, serverů, vlastních kolekcí), triggerů úloh, plánů úloh, obsahu úloh a úloh do nového agenta elastické úlohy.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>Nainstalovat nejnovější rutiny elastických úloh

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pokud chcete získat nejnovější rutiny elastické úlohy, nainstalujte modul **AZ. SQL** . V PowerShellu spusťte následující příkazy s přístupem správce.

```powershell
# installs the latest PackageManagement and PowerShellGet packages
Find-Package PackageManagement | Install-Package -Force
Find-Package PowerShellGet | Install-Package -Force

# Restart your powershell session with administrative access

# Install and import the Az.Sql module, then confirm
Install-Module -Name Az.Sql
Import-Module Az.Sql

Get-Module Az.Sql
```

Kromě modulu **AZ. SQL** vyžaduje tento kurz také modul *SQL SQLServer* . Podrobnosti najdete v tématu [Instalace modulu powershellu SQL Server](/sql/powershell/download-sql-server-ps-module).

## <a name="create-required-resources"></a>Vytvoření požadovaných prostředků

K vytvoření agenta elastických úloh se vyžaduje databáze (S0 nebo vyšší), která se použije jako [databáze úloh](job-automation-overview.md#job-database).

Následující skript vytvoří novou skupinu prostředků, server a databázi, která se použije jako databáze úloh. Druhý skript vytvoří druhý server se dvěma prázdnými databázemi pro provádění úloh s.

Elastické úlohy nemají žádné specifické požadavky na pojmenování, takže můžete použít libovolné zásady vytváření názvů, pokud splňují [požadavky Azure](/azure/architecture/best-practices/resource-naming).

```powershell
# sign in to Azure account
Connect-AzAccount

# create a resource group
Write-Output "Creating a resource group..."
$resourceGroupName = Read-Host "Please enter a resource group name"
$location = Read-Host "Please enter an Azure Region"
$rg = New-AzResourceGroup -Name $resourceGroupName -Location $location
$rg

# create a server
Write-Output "Creating a server..."
$agentServerName = Read-Host "Please enter an agent server name"
$agentServerName = $agentServerName + "-" + [guid]::NewGuid()
$adminLogin = Read-Host "Please enter the server admin name"
$adminPassword = Read-Host "Please enter the server admin password"
$adminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$adminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $adminLogin, $adminPasswordSecure
$agentServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $agentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$agentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$agentServer

# create the job database
Write-Output "Creating a blank database to be used as the Job Database..."
$jobDatabaseName = "JobDatabase"
$jobDatabase = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $agentServerName -DatabaseName $jobDatabaseName -RequestedServiceObjectiveName "S0"
$jobDatabase
```

```powershell
# create a target server and sample databases - uses the same credentials
Write-Output "Creating target server..."
$targetServerName = Read-Host "Please enter a target server name"
$targetServerName = $targetServerName + "-" + [guid]::NewGuid()
$targetServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $targetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set target server firewall rules to allow all Azure IPs
$targetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$targetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$targetServer

# create sample databases to execute jobs against
$db1 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database1"
$db1
$db2 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database2"
$db2
```

## <a name="use-elastic-jobs"></a>Použití elastických úloh

Pokud chcete použít elastické úlohy, zaregistrujte funkci v předplatném Azure spuštěním následujícího příkazu. Spusťte tento příkaz jednou pro předplatné, ve kterém máte v úmyslu zřídit agenta elastické úlohy. Odběry, které obsahují pouze databáze, které jsou cílem úlohy, nemusí být registrovány.

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

### <a name="create-the-elastic-job-agent"></a>Vytvoření agenta elastických úloh

Agent elastických úloh je prostředek Azure určený k vytváření, spouštění a správě úloh. Agent spouští úlohy na základě plánu nebo jako jednorázové úlohy.

Rutina **New-AzSqlElasticJobAgent** vyžaduje databázi v Azure SQL Database, aby již existovala, takže parametry *resourceGroupName*, *servername*a *DatabaseName* musí všechny odkazovat na stávající prostředky.

```powershell
Write-Output "Creating job agent..."
$agentName = Read-Host "Please enter a name for your new Elastic Job agent"
$jobAgent = $jobDatabase | New-AzSqlElasticJobAgent -Name $agentName
$jobAgent
```

### <a name="create-the-job-credentials"></a>Vytvoření přihlašovacích údajů úlohy

Úlohy používají přihlašovací údaje v oboru databáze pro připojení k cílovým databázím zadaným cílovou skupinou při spuštění a spuštění skriptů. Tyto přihlašovací údaje v oboru databáze slouží také k připojení k hlavní databázi za účelem výčtu všech databází na serveru nebo v elastickém fondu, když se jako typ člena cílové skupiny použije jedna z těchto možností.

Přihlašovací údaje v oboru databáze se musí vytvořit v databázi úloh. Všechny cílové databáze musí mít přihlašovací účet s dostatečnými oprávněními pro úspěšné dokončení úlohy.

![Přihlašovací údaje k elastickým úlohám](./media/elastic-jobs-powershell-create/job-credentials.png)

Kromě přihlašovacích údajů na obrázku si všimněte přidání příkazů **GRANT** v následujícím skriptu. Tato oprávnění se vyžadují pro skript, který jsme zvolili pro tuto ukázkovou úlohu. Vzhledem k tomu, že příklad vytvoří novou tabulku v cílových databázích, musí mít každá cílová databáze správná oprávnění k úspěšnému spuštění.

Pokud chcete vytvořit požadované přihlašovací údaje k úloze (v databázi úloh), spusťte následující skript:

```powershell
# in the master database (target server)
# create the master user login, master user, and job user login
$params = @{
  'database' = 'master'
  'serverInstance' =  $targetServer.ServerName + '.database.windows.net'
  'username' = $adminLogin
  'password' = $adminPassword
  'outputSqlErrors' = $true
  'query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @params
$params.query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @params
$params.query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @params

# for each target database
# create the jobuser from jobuser login and check permission for script execution
$targetDatabases = @( $db1.DatabaseName, $Db2.DatabaseName )
$createJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$grantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$grantCreateScript = "GRANT CREATE TABLE TO jobuser"

$targetDatabases | % {
  $params.database = $_
  $params.query = $createJobUserScript
  Invoke-SqlCmd @params
  $params.query = $grantAlterSchemaScript
  Invoke-SqlCmd @params
  $params.query = $grantCreateScript
  Invoke-SqlCmd @params
}

# create job credential in Job database for master user
Write-Output "Creating job credentials..."
$loginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$masterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $loginPasswordSecure
$masterCred = $jobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $masterCred

$jobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $loginPasswordSecure
$jobCred = $jobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $jobCred
```

### <a name="define-the-target-databases-to-run-the-job-against"></a>Definujte cílové databáze, u kterých se má úloha spustit.

[Cílová skupina](job-automation-overview.md#target-group) definuje sadu jedné nebo více databází, pro které se provede určitý krok úlohy.

Následující fragment kódu vytvoří dvě cílové skupiny: skupiny *serverů*a *serverGroupExcludingDb2*. *serverová* aplikace cílí na všechny databáze, které existují na serveru v době spuštění, a *serverGroupExcludingDb2* cílí na všechny databáze na serveru s výjimkou *targetDb2*:

```powershell
Write-Output "Creating test target groups..."
# create ServerGroup target group
$serverGroup = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$serverGroup | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName

# create ServerGroup with an exclusion of db2
$serverGroupExcludingDb2 = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -Database $db2.DatabaseName -Exclude
```

### <a name="create-a-job-and-steps"></a>Vytvoření úlohy a kroků

Tento příklad definuje úlohu a dva kroky úlohy pro spuštění úlohy. První krok úlohy (*step1*) ve všech databázích v cílové skupině *ServerGroup* vytvoří novou tabulku (*Step1Table*). Druhý krok úlohy (*step2*) vytvoří novou tabulku (*Step2Table*) ve všech databázích kromě databáze *TargetDb2*, protože je vyloučená v cílové skupině definované dříve.

```powershell
Write-Output "Creating a new job..."
$jobName = "Job1"
$job = $jobAgent | New-AzSqlElasticJob -Name $jobName -RunOnce
$job

Write-Output "Creating job steps..."
$sqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$sqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $serverGroup.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText1
$job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $serverGroupExcludingDb2.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText2
```

### <a name="run-the-job"></a>Spuštění úlohy

Pokud chcete úlohu okamžitě spustit, spusťte následující příkaz:

```powershell
Write-Output "Start a new execution of the job..."
$jobExecution = $job | Start-AzSqlElasticJob
$jobExecution
```

Po úspěšném dokončení by se v databázi TargetDb1 měly zobrazit dvě nové tabulky a pouze jedna nová tabulka v databázi TargetDb2:

   ![ověření nových tabulek v aplikaci SSMS](./media/elastic-jobs-powershell-create/job-execution-verification.png)

Můžete také naplánovat, aby se úloha spouštěla později. Pokud chcete naplánovat spuštění úlohy na určitý čas, spusťte následující příkaz:

```powershell
# run every hour starting from now
$job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

### <a name="monitor-status-of-job-executions"></a>Monitorování stavu provádění úloh

Následující fragment kódu získá podrobnosti o provádění úlohy:

```powershell
# get the latest 10 executions run
$jobAgent | Get-AzSqlElasticJobExecution -Count 10

# get the job step execution details
$jobExecution | Get-AzSqlElasticJobStepExecution

# get the job target execution details
$jobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

V následující tabulce jsou uvedené možné stavy provádění úloh:

|State|Popis|
|:---|:---|
|**Vytvořeno** | Provádění úlohy bylo právě vytvořeno a ještě neprobíhá.|
|**InProgress** | Právě probíhá provádění úlohy.|
|**WaitingForRetry** | Provádění úlohy nedokázalo dokončit svoji akci a čeká na opakování.|
|**Úspěšný** | Provádění úlohy bylo úspěšně dokončeno.|
|**SucceededWithSkipped** | Provádění úlohy se úspěšně dokončilo, ale některé z jejích podřízených objektů se přeskočily.|
|**Neúspěšný** | Provádění úlohy selhalo a vyčerpalo své opakované pokusy.|
|**Vypršel časový limit** | Vypršel časový limit pro provedení úlohy.|
|**Zrušeno** | Provádění úlohy bylo zrušeno.|
|**Přeskočeno** | Provádění úlohy bylo přeskočeno, protože na stejném cíli již běželo jiné provedení stejného kroku úlohy.|
|**WaitingForChildJobExecutions** | Provádění úlohy čeká na dokončení jeho podřízeného spuštění.|

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraněním skupiny prostředků odstraňte prostředky Azure vytvořené v tomto kurzu.

> [!TIP]
> Pokud budete chtít pokračovat v práci s těmito úlohami, neprovádějte čištění prostředků vytvořených v tomto článku.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste spustili skript Transact-SQL pro sadu databází. Naučili jste se provádět následující úlohy:

> [!div class="checklist"]
> * Vytvoření agenta elastických úloh
> * Vytvoření přihlašovacích údajů k úloze, aby úlohy mohly na svých cílech spouštět skripty
> * Definice cílů (servery, elastické fondy, databáze, mapy horizontálních oddílů), pro které chcete úlohu spustit
> * Vytvoření přihlašovacích údajů v oboru databáze v cílových databázích, aby se agent mohl připojit a spouštět úlohy
> * Vytvoření úlohy
> * Přidání kroku do úlohy
> * Spuštění provádění úlohy
> * Monitorování úlohy

> [!div class="nextstepaction"]
> [Správa elastických úloh pomocí jazyka Transact-SQL](elastic-jobs-tsql-create-manage.md)
