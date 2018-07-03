---
title: Vytvoření agenta elastických úloh Azure SQL Database pomocí PowerShellu | Microsoft Docs
description: Zjistěte, jak vytvořit agenta elastických úloh pomocí PowerShellu.
services: sql-database
author: johnpaulkee
manager: craigg
ms.service: sql-database
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: joke
ms.openlocfilehash: dc2776e0f3b14d5fd2375f735c18345c32ca743a
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37033595"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>Vytvoření agenta elastických úloh pomocí PowerShellu

[Elastické úlohy](elastic-jobs-overview.md) umožňují paralelní spouštění jednoho nebo více skriptů Transact-SQL (T-SQL) napříč mnoha databázemi.

V tomto kurzu se naučíte kroky potřebné ke spuštění dotazu napříč několika databázemi:

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

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Nainstalujte nejnovější verzi Preview modulu **AzureRM.Sql**, se kterým získáte rutiny pro elastické úlohy. Na příkazovém řádku se zvýšenými oprávněními (spuštěném jako správce) spusťte následující příkazy.

```powershell
# Installs the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# You may need to restart the powershell session
# Installs the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Places AzureRM.Sql preview cmdlets side by side with existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -Force
```

## <a name="create-required-resources"></a>Vytvoření požadovaných prostředků

K vytvoření agenta elastických úloh se vyžaduje databáze (S0 nebo vyšší), která se použije jako [databáze úloh](elastic-jobs-overview.md#job-database). 

*Následující skript vytvoří novou skupinu prostředků, server a databázi, která se použije jako databáze úloh. Následující skript také vytvoří druhý server se 2 prázdnými databázemi, pro které se budou spouštět úlohy.*

Elastické úlohy nemají žádné specifické požadavky na pojmenování, takže můžete použít libovolné zásady vytváření názvů, pokud splňují [požadavky Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

```powershell
# Sign in to your Azure account
Connect-AzureRmAccount

# Create a resource group
Write-Output "Creating a resource group..."
$ResourceGroupName = Read-Host "Please enter a resource group name"
$Location = Read-Host "Please enter an Azure Region"
$Rg = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
$Rg

# Create a server
Write-Output "Creating a server..."
$AgentServerName = Read-Host "Please enter an agent server name"
$AgentServerName = $AgentServerName + "-" + [guid]::NewGuid()
$AdminLogin = Read-Host "Please enter the server admin name"
$AdminPassword = Read-Host "Please enter the server admin password"
$AdminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$AdminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AdminLogin, $AdminPasswordSecure
$AgentServer = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $AgentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$AgentServer | New-AzureRmSqlServerFirewallRule -AllowAllAzureIPs
$AgentServer

# Create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$JobDatabaseName = "JobDatabase"
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $JobDatabaseName -RequestedServiceObjectiveName "S0"
$JobDatabase
```

```powershell
# Create a target server and some sample databases - uses the same admin credential as the agent server just for simplicity
Write-Output "Creating target server..."
$TargetServerName = Read-Host "Please enter a target server name"
$TargetServerName = $TargetServerName + "-" + [guid]::NewGuid()
$TargetServer = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $TargetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set target server firewall rules to allow all Azure IPs
$TargetServer | New-AzureRmSqlServerFirewallRule -AllowAllAzureIPs
$TargetServer | New-AzureRmSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$TargetServer

# Create some sample databases to execute jobs against...
$Db1 = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb1"
$Db1
$Db2 = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb2"
$Db2
```

## <a name="enable-the-elastic-jobs-preview-for-your-subscription"></a>Povolení elastických úloh verze Preview pro předplatné

Pokud chcete používat elastické úlohy, zaregistrujte tuto funkci ve svém předplatném Azure spuštěním následujícího příkazu (v každém předplatném, ve kterém chcete používat elastické úlohy, stačí tento příkaz spustit pouze jednou):

```powershell
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

## <a name="create-the-elastic-job-agent"></a>Vytvoření agenta elastických úloh

Agent elastických úloh je prostředek Azure určený k vytváření, spouštění a správě úloh. Agent spouští úlohy na základě plánu nebo jako jednorázové úlohy.

Rutina **New-AzureRmSqlElasticJobAgent** vyžaduje, aby již existovala databáze SQL Azure, takže parametry *ResourceGroupName*, *ServerName* a *DatabaseName* musí odkazovat na existující prostředky.

```powershell
Write-Output "Creating job agent..."
$AgentName = Read-Host "Please enter a name for your new Elastic Job agent"
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $AgentName
$JobAgent
```

## <a name="create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets"></a>Vytvoření přihlašovacích údajů k úloze, aby úlohy mohly na svých cílech spouštět skripty

Úlohy se při spuštění připojují k cílovým databázím určeným cílovou skupinou pomocí přihlašovacích údajů v oboru databáze. Tyto přihlašovací údaje v oboru databáze slouží také k připojení k hlavní databázi za účelem výčtu všech databází na serveru nebo v elastickém fondu, když se jako typ člena cílové skupiny použije jedna z těchto možností.

Přihlašovací údaje v oboru databáze se musí vytvořit v databázi úloh.  
Všechny cílové databáze musí mít přihlašovací účet s dostatečnými oprávněními pro úspěšné dokončení úlohy.

![Přihlašovací údaje k elastickým úlohám](media/elastic-jobs-overview/job-credentials.png)

Kromě přihlašovacích údajů na obrázku si všimněte přidání příkazů *GRANT* v následujícím skriptu. Tato oprávnění se vyžadují pro skript, který jsme zvolili pro tuto ukázkovou úlohu. Vzhledem k tomu, že tento příklad v cílových databázích vytvoří novou tabulku, potřebují všechny cílové databáze příslušná oprávnění pro úspěšné spuštění.

Pokud chcete vytvořit požadované přihlašovací údaje k úloze (v databázi úloh), spusťte následující skript:

```powershell
# In the master database (target server)
# - Create the master user login
# - Create the master user from master user login
# - Create the job user login
$Params = @{
  'Database' = 'master'
  'ServerInstance' =  $TargetServer.ServerName + '.database.windows.net'
  'Username' = $AdminLogin
  'Password' = $AdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @Params
$Params.Query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @Params
$Params.Query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @Params

# For each of the target databases
# - Create the jobuser from jobuser login
# - Make sure they have the right permissions for successful script execution
$TargetDatabases = @( $Db1.DatabaseName, $Db2.DatabaseName )
$CreateJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$GrantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$GrantCreateScript = "GRANT CREATE TABLE TO jobuser"

$TargetDatabases | % {
  $Params.Database = $_

  $Params.Query = $CreateJobUserScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantAlterSchemaScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantCreateScript
  Invoke-SqlCmd @Params
}

# Create job credential in Job database for master user
Write-Output "Creating job credentials..."
$LoginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$MasterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $LoginPasswordSecure
$MasterCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "masteruser" -Credential $MasterCred

$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $LoginPasswordSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "jobuser" -Credential $JobCred
```

## <a name="define-the-target-databases-you-want-to-run-the-job-against"></a>Definice cílových databází, pro které se má úloha spustit

[Cílová skupina](elastic-jobs-overview.md#target-group) definuje sadu jedné nebo více databází, pro které se provede určitý krok úlohy. 

Následující fragment kódu vytvoří dvě cílové skupiny: *ServerGroup* a *ServerGroupExcludingDb2*. Cílem skupiny *ServerGroup* jsou všechny databáze, které na serveru existují v době spuštění, a cílem skupiny *ServerGroupExcludingDb2* jsou všechny databáze na serveru kromě databáze *TargetDb2*:

```powershell
Write-Output "Creating test target groups..."
# Create ServerGroup target group
$ServerGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name 'ServerGroup'
$ServerGroup | Add-AzureRmSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName

# Create ServerGroup with an exclusion of Db2
$ServerGroupExcludingDb2 = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$ServerGroupExcludingDb2 | Add-AzureRmSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName
$ServerGroupExcludingDb2 | Add-AzureRmSqlElasticJobTarget -ServerName $TargetServerName -Database $Db2.DatabaseName -Exclude
```

## <a name="create-a-job"></a>Vytvoření úlohy

```powershell
Write-Output "Creating a new job"
$JobName = "Job1"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job
```

## <a name="create-a-job-step"></a>Vytvoření kroku úlohy

Tento příklad definuje dva kroky, které má úloha spustit. První krok úlohy (*step1*) ve všech databázích v cílové skupině *ServerGroup* vytvoří novou tabulku (*Step1Table*). Druhý krok úlohy (*step2*) vytvoří novou tabulku (*Step2Table*) ve všech databázích kromě databáze *TargetDb2*, protože je vyloučená v cílové skupině [definované dříve](#define-the-target-databases-you-want-to-run-the-job-against).

```powershell
Write-Output "Creating job steps"
$SqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$SqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$Job | Add-AzureRmSqlElasticJobStep -Name "step1" -TargetGroupName $ServerGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText1
$Job | Add-AzureRmSqlElasticJobStep -Name "step2" -TargetGroupName $ServerGroupExcludingDb2.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText2
```


## <a name="run-the-job"></a>Spuštění úlohy

Pokud chcete úlohu okamžitě spustit, spusťte následující příkaz:

```powershell
Write-Output "Start a new execution of the job..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
```

Po úspěšném dokončení by se v databázi TargetDb1 měly zobrazit dvě nové tabulky a pouze jedna nová tabulka v databázi TargetDb2:


   ![ověření nových tabulek v aplikaci SSMS](media/elastic-jobs-overview/job-execution-verification.png)




## <a name="monitor-status-of-job-executions"></a>Monitorování stavu provádění úloh

Následující fragment kódu získá podrobnosti o provádění úlohy:

```powershell
# Get the latest 10 executions run
$JobAgent | Get-AzureRmSqlElasticJobExecution -Count 10

# Get the job step execution details
$JobExecution | Get-AzureRmSqlElasticJobStepExecution

# Get the job target execution details
$JobExecution | Get-AzureRmSqlElasticJobTargetExecution -Count 2
```

## <a name="schedule-the-job-to-run-later"></a>Naplánování pozdějšího spuštění úlohy

Pokud chcete naplánovat spuštění úlohy na určitý čas, spusťte následující příkaz:

```powershell
# Run every hour starting from now
$Job | Set-AzureRmSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraněním skupiny prostředků odstraňte prostředky Azure vytvořené v tomto kurzu.

> [!TIP]
> Pokud chcete pokračovat v práci s těmito úlohami, nevyčišťujte prostředky vytvořené v rámci tohoto článku. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené v rámci tohoto článku.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $ResourceGroupName
```


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste spustili skript Transact-SQL pro sadu databází.  Naučili jste se provádět následující úlohy:

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
>[Správa elastických úloh pomocí jazyka Transact-SQL](elastic-jobs-tsql.md)