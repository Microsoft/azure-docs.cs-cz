---
title: Vytvoření agenta elastických úloh Azure SQL Database pomocí PowerShellu | Microsoft Docs
description: Zjistěte, jak vytvořit agenta elastických úloh pomocí PowerShellu.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 03/13/2019
ms.openlocfilehash: 0d64bd150a43666679253f8244d80411e25dfdcd
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935046"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>Vytvoření agenta elastických úloh pomocí PowerShellu

[Elastické úlohy](sql-database-job-automation-overview.md#elastic-database-jobs-preview) umožňují paralelní spouštění jednoho nebo více skriptů Transact-SQL (T-SQL) napříč mnoha databázemi.

V tomto kurzu se seznámíte s kroky potřebnými ke spuštění dotazu napříč více databázemi:

> [!div class="checklist"]
> * Vytvoření agenta elastických úloh
> * Vytvoření přihlašovacích údajů k úloze, aby úlohy mohly na svých cílech spouštět skripty
> * Definice cílů (servery, elastické fondy, databáze, mapy horizontálních oddílů), pro které chcete úlohu spustit
> * Vytvoření přihlašovacích údajů v oboru databáze v cílových databázích, aby se agent mohl připojit a spouštět úlohy
> * Vytvoří úlohu
> * Přidání kroků do úlohy
> * Spuštění provádění úlohy
> * Monitorování úlohy

## <a name="prerequisites"></a>Požadavky

Upgradovaná verze úlohy elastické databáze má novou sadu rutin PowerShellu pro použití během migrace. Tyto nové rutiny převádějí všechna vaše stávající přihlašovací údaje úlohy, cíle (včetně databází, serverů, vlastních kolekcí), triggerů úloh, plánů úloh, obsahu úloh a úloh do nového agenta elastické úlohy.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>Nainstalovat nejnovější rutiny elastických úloh

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pokud chcete získat nejnovější rutiny elastické úlohy, nainstalujte modul **AZ. SQL** 1.1.1-Preview. Spusťte následující příkazy v PowerShellu s přístupem pro správu.

```powershell
# Installs the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# Installs the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Restart your powershell session with administrative access

# Places Az.Sql preview cmdlets side by side with existing Az.Sql version
Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease

# Import the Az.Sql module
Import-Module Az.Sql -RequiredVersion 1.1.1

# Confirm if module successfully imported - if the imported version is 1.1.1, then continue
Get-Module Az.Sql
```

- Kromě modulu **AZ. SQL** 1.1.1-Preview vyžaduje tento kurz také modul *SQLServer* PowerShell. Podrobnosti najdete v tématu [Instalace modulu powershellu SQL Server](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module).


## <a name="create-required-resources"></a>Vytvoření požadovaných prostředků

K vytvoření agenta elastických úloh se vyžaduje databáze (S0 nebo vyšší), která se použije jako [databáze úloh](sql-database-job-automation-overview.md#job-database). 

*Následující skript vytvoří novou skupinu prostředků, server a databázi, která se použije jako databáze úloh. Následující skript také vytvoří druhý server se dvěma prázdnými databázemi, ve kterých se mají spouštět úlohy.*

Elastické úlohy nemají žádné specifické požadavky na pojmenování, takže můžete použít libovolné zásady vytváření názvů, pokud splňují [požadavky Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

```powershell
# Sign in to your Azure account
Connect-AzAccount

# Create a resource group
Write-Output "Creating a resource group..."
$ResourceGroupName = Read-Host "Please enter a resource group name"
$Location = Read-Host "Please enter an Azure Region"
$Rg = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
$Rg

# Create a server
Write-Output "Creating a server..."
$AgentServerName = Read-Host "Please enter an agent server name"
$AgentServerName = $AgentServerName + "-" + [guid]::NewGuid()
$AdminLogin = Read-Host "Please enter the server admin name"
$AdminPassword = Read-Host "Please enter the server admin password"
$AdminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$AdminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AdminLogin, $AdminPasswordSecure
$AgentServer = New-AzSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $AgentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$AgentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$AgentServer

# Create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$JobDatabaseName = "JobDatabase"
$JobDatabase = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $JobDatabaseName -RequestedServiceObjectiveName "S0"
$JobDatabase
```

```powershell
# Create a target server and some sample databases - uses the same admin credential as the agent server just for simplicity
Write-Output "Creating target server..."
$TargetServerName = Read-Host "Please enter a target server name"
$TargetServerName = $TargetServerName + "-" + [guid]::NewGuid()
$TargetServer = New-AzSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $TargetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set target server firewall rules to allow all Azure IPs
$TargetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$TargetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$TargetServer

# Create some sample databases to execute jobs against...
$Db1 = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb1"
$Db1
$Db2 = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb2"
$Db2
```

## <a name="enable-the-elastic-jobs-preview-for-your-subscription"></a>Povolení elastických úloh verze Preview pro předplatné

Pokud chcete použít elastické úlohy, zaregistrujte funkci v předplatném Azure spuštěním následujícího příkazu. Spusťte tento příkaz jednou pro předplatné, ve kterém máte v úmyslu zřídit agenta elastické úlohy. Odběry, které obsahují pouze databáze, které jsou cílem úlohy, nemusí být registrovány.

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

## <a name="create-the-elastic-job-agent"></a>Vytvoření agenta elastických úloh

Agent elastických úloh je prostředek Azure určený k vytváření, spouštění a správě úloh. Agent spouští úlohy na základě plánu nebo jako jednorázové úlohy.

Rutina **New-AzSqlElasticJobAgent** vyžaduje, aby databáze SQL Azure již existovala, takže parametry *ResourceGroupName*, *servername*a *DatabaseName* musí mít všechny body na stávající prostředky.

```powershell
Write-Output "Creating job agent..."
$AgentName = Read-Host "Please enter a name for your new Elastic Job agent"
$JobAgent = $JobDatabase | New-AzSqlElasticJobAgent -Name $AgentName
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
$MasterCred = $JobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $MasterCred

$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $LoginPasswordSecure
$JobCred = $JobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $JobCred
```

## <a name="define-the-target-databases-you-want-to-run-the-job-against"></a>Definice cílových databází, pro které se má úloha spustit

[Cílová skupina](sql-database-job-automation-overview.md#target-group) definuje sadu jedné nebo více databází, pro které se provede určitý krok úlohy. 

Následující fragment kódu vytvoří dvě cílové skupiny: *Server*a *ServerGroupExcludingDb2*. Cílem skupiny *ServerGroup* jsou všechny databáze, které na serveru existují v době spuštění, a cílem skupiny *ServerGroupExcludingDb2* jsou všechny databáze na serveru kromě databáze *TargetDb2*:

```powershell
Write-Output "Creating test target groups..."
# Create ServerGroup target group
$ServerGroup = $JobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$ServerGroup | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName

# Create ServerGroup with an exclusion of Db2
$ServerGroupExcludingDb2 = $JobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$ServerGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName
$ServerGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -Database $Db2.DatabaseName -Exclude
```

## <a name="create-a-job"></a>Vytvoří úlohu

```powershell
Write-Output "Creating a new job"
$JobName = "Job1"
$Job = $JobAgent | New-AzSqlElasticJob -Name $JobName -RunOnce
$Job
```

## <a name="create-a-job-step"></a>Vytvoření kroku úlohy

Tento příklad definuje dva kroky, které má úloha spustit. První krok úlohy (*step1*) ve všech databázích v cílové skupině *ServerGroup* vytvoří novou tabulku (*Step1Table*). Druhý krok úlohy (*step2*) vytvoří novou tabulku (*Step2Table*) ve všech databázích kromě databáze *TargetDb2*, protože je vyloučená v cílové skupině [definované dříve](#define-the-target-databases-you-want-to-run-the-job-against).

```powershell
Write-Output "Creating job steps"
$SqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$SqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$Job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $ServerGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText1
$Job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $ServerGroupExcludingDb2.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText2
```


## <a name="run-the-job"></a>Spuštění úlohy

Pokud chcete úlohu okamžitě spustit, spusťte následující příkaz:

```powershell
Write-Output "Start a new execution of the job..."
$JobExecution = $Job | Start-AzSqlElasticJob
$JobExecution
```

Po úspěšném dokončení by se v databázi TargetDb1 měly zobrazit dvě nové tabulky a pouze jedna nová tabulka v databázi TargetDb2:


   ![ověření nových tabulek v aplikaci SSMS](media/elastic-jobs-overview/job-execution-verification.png)




## <a name="monitor-status-of-job-executions"></a>Monitorování stavu provádění úloh

Následující fragment kódu získá podrobnosti o provádění úlohy:

```powershell
# Get the latest 10 executions run
$JobAgent | Get-AzSqlElasticJobExecution -Count 10

# Get the job step execution details
$JobExecution | Get-AzSqlElasticJobStepExecution

# Get the job target execution details
$JobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

### <a name="job-execution-states"></a>Stavy provádění úlohy

V následující tabulce jsou uvedené možné stavy provádění úloh:

|Stav|Popis|
|:---|:---|
|**Vytvářejí** | Provádění úlohy bylo právě vytvořeno a ještě neprobíhá.|
|**InProgress** | Právě probíhá provádění úlohy.|
|**WaitingForRetry** | Provádění úlohy nedokázalo dokončit svoji akci a čeká na opakování.|
|**Úspěchu** | Provádění úlohy bylo úspěšně dokončeno.|
|**SucceededWithSkipped** | Provádění úlohy se úspěšně dokončilo, ale některé z jejích podřízených objektů se přeskočily.|
|**Se nezdařilo** | Provádění úlohy selhalo a vyčerpalo své opakované pokusy.|
|**Vypršel časový limit** | Vypršel časový limit pro provedení úlohy.|
|**Zrušil** | Provádění úlohy bylo zrušeno.|
|**Vynecháno** | Provádění úlohy bylo přeskočeno, protože na stejném cíli již běželo jiné provedení stejného kroku úlohy.|
|**WaitingForChildJobExecutions** | Provádění úlohy čeká na dokončení jeho podřízeného spuštění.|

## <a name="schedule-the-job-to-run-later"></a>Naplánování pozdějšího spuštění úlohy

Pokud chcete naplánovat spuštění úlohy na určitý čas, spusťte následující příkaz:

```powershell
# Run every hour starting from now
$Job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraněním skupiny prostředků odstraňte prostředky Azure vytvořené v tomto kurzu.

> [!TIP]
> Pokud chcete pokračovat v práci s těmito úlohami, nevyčišťujte prostředky vytvořené v rámci tohoto článku. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené v rámci tohoto článku.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $ResourceGroupName
```


## <a name="next-steps"></a>Další postup

V tomto kurzu jste spustili skript Transact-SQL pro sadu databází.  Naučili jste se provádět následující úlohy:

> [!div class="checklist"]
> * Vytvoření agenta elastických úloh
> * Vytvoření přihlašovacích údajů k úloze, aby úlohy mohly na svých cílech spouštět skripty
> * Definice cílů (servery, elastické fondy, databáze, mapy horizontálních oddílů), pro které chcete úlohu spustit
> * Vytvoření přihlašovacích údajů v oboru databáze v cílových databázích, aby se agent mohl připojit a spouštět úlohy
> * Vytvoří úlohu
> * Přidání kroku do úlohy
> * Spuštění provádění úlohy
> * Monitorování úlohy

> [!div class="nextstepaction"]
>[Správa elastických úloh pomocí jazyka Transact-SQL](elastic-jobs-tsql.md)
