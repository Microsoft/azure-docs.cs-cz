---
title: Správa Azure Data Lake Analytics pomocí Azure PowerShell
description: Tento článek popisuje, jak pomocí Azure PowerShell spravovat účty Data Lake Analytics, zdroje dat, uživatele a & úlohy.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/29/2018
ms.openlocfilehash: afa21e6aae769e69e8bc83b9fa0d4f9b76396f7e
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220308"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Správa Azure Data Lake Analytics pomocí Azure PowerShell

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Tento článek popisuje, jak spravovat účty Azure Data Lake Analytics, zdroje dat, uživatele a úlohy pomocí Azure PowerShell.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud chcete používat PowerShell s Data Lake Analytics, shromážděte následující informace:

* **ID předplatného**: ID předplatného Azure, které obsahuje váš účet Data Lake Analytics.
* **Skupina prostředků**: název skupiny prostředků Azure, která obsahuje váš účet Data Lake Analytics.
* **Data Lake Analytics název účtu**: název vašeho účtu Data Lake Analytics.
* **Výchozí název Data Lake Store účtu**: každý Data Lake Analytics účet má výchozí data Lake Store účet.
* **Umístění**: umístění účtu Data Lake Analytics, například "východní USA 2" nebo jiná podporovaná umístění.

Fragment kódu PowerShellu v tomto kurzu používá následující proměnné k ukládání příslušných informací:

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

### <a name="log-in-using-interactive-user-authentication"></a>Přihlášení pomocí interaktivního ověřování uživatelů

Přihlaste se pomocí ID předplatného nebo podle názvu předplatného.

```powershell
# Using subscription id
Connect-AzAccount -SubscriptionId $subId

# Using subscription name
Connect-AzAccount -SubscriptionName $subname
```

## <a name="saving-authentication-context"></a>Ukládá se kontext ověřování.

`Connect-AzAccount`Rutina vždy vyzve k zadání přihlašovacích údajů. K tomu se můžete vyhnout, když použijete následující rutiny:

```powershell
# Save login session information
Save-AzAccounts -Path D:\profile.json  

# Load login session information
Select-AzAccounts -Path D:\profile.json
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Přihlaste se pomocí identity objektu služby (SPI).

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname"
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>Správa účtů

### <a name="list-accounts"></a>Výpis účtů

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>Vytvoření účtu

Každý účet Data Lake Analytics vyžaduje výchozí účet Data Lake Store, který slouží k ukládání protokolů. Můžete znovu použít existující účet nebo vytvořit účet.

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>Získat informace o účtu

Získejte podrobnosti o účtu.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Zjistit, jestli účet existuje

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Správa zdrojů dat

Azure Data Lake Analytics aktuálně podporuje následující zdroje dat:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Každý Data Lake Analytics účet má výchozí účet Data Lake Store. Výchozí účet Data Lake Store slouží k ukládání metadat úlohy a protokolů auditu úlohy.

### <a name="find-the-default-data-lake-store-account"></a>Najít výchozí účet Data Lake Store

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Výchozí účet Data Lake Store můžete najít tak, že filtrujete seznam zdrojů dat pomocí `IsDefault` vlastnosti:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault }
```

### <a name="add-a-data-source"></a>Přidání zdroje dat

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName
```

### <a name="list-data-sources"></a>Seznam zdrojů dat

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Odesílání úloh U-SQL

### <a name="submit-a-string-as-a-u-sql-job"></a>Odeslání řetězce jako úlohy U-SQL

```powershell
$script = @"
@a  =
    SELECT * FROM
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-job"></a>Odeslání souboru jako úlohy U-SQL

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>Vypsat úlohy

Výstup zahrnuje aktuálně spuštěné úlohy a nedávno dokončené úlohy.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Seznam úloh prvních N

Ve výchozím nastavení se seznam úloh seřadí podle doby odeslání. Proto se poslední odeslané úlohy zobrazí jako první. Ve výchozím nastavení účet ADLA pamatuje úlohy po dobu 180 dnů, ale rutina Get-AdlJob ve výchozím nastavení vrátí pouze prvních 500. K vypsání konkrétního počtu úloh použijte parametr-top.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Výpis úloh podle stavu úlohy

Použití `-State` parametru. Můžete kombinovat libovolnou z těchto hodnot:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

### <a name="list-jobs-by-job-result"></a>Vypíše úlohy podle výsledku úlohy.

Pomocí `-Result` parametru zjistíte, zda ukončené úlohy byly úspěšně dokončeny. Má tyto hodnoty:

* Cancelled
* Neúspěšný
* Žádné
* Úspěšný

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Výpis úloh podle odesílatele úlohy

`-Submitter`Parametr vám pomůže zjistit, kdo úlohu odeslal.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Vypíše úlohy podle času odeslání.

`-SubmittedAfter`Je užitečné při filtrování do časového rozsahu.

```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>Získat stav úlohy

Získejte stav konkrétní úlohy.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="cancel-a-job"></a>Zrušení úlohy

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Počkejte na dokončení úlohy.

Místo opakování `Get-AdlAnalyticsJob` úlohy můžete použít `Wait-AdlJob` rutinu, která čeká na dokončení úlohy.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>Analýza historie úloh

Pomocí Azure PowerShell můžete analyzovat historii úloh, které jsou spuštěné v Data Lake Analytics, což je výkonná technika. Můžete ji použít k získání přehledu o využití a nákladech. Další informace najdete v [úložišti ukázek analýzy historie úloh](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis) .  

## <a name="list-job-pipelines-and-recurrences"></a>Seznam kanálů a opakování úloh

Pomocí `Get-AdlJobPipeline` rutiny můžete zobrazit informace o kanálu dříve odeslaných úloh.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Pomocí `Get-AdlJobRecurrence` rutiny můžete zobrazit informace o opakování dříve odeslaných úloh.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="manage-compute-policies"></a>Správa výpočetních zásad

### <a name="list-existing-compute-policies"></a>Vypsat existující výpočetní zásady

`Get-AdlAnalyticsComputePolicy`Rutina načte informace o zásadách výpočtů pro účet Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Vytvoření výpočetních zásad

`New-AdlAnalyticsComputePolicy`Rutina vytvoří novou výpočetní zásadu pro účet Data Lake Analytics. V tomto příkladu se nastaví maximální jednotky Austrálie dostupné pro zadaného uživatele na 50 a minimální priorita úlohy na 250.

```powershell
$userObjectId = (Get-AzAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="manage-files"></a>Správa souborů

### <a name="check-for-the-existence-of-a-file"></a>Kontrolovat existenci souboru

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Nahrávání a stahování

Nahrajte soubor.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv"
```

Nahrajte rekurzivně celou složku.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Stáhněte si soubor.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Stáhnout celou složku rekurzivně.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Pokud je proces nahrávání nebo stahování přerušený, můžete se pokusit proces obnovit spuštěním rutiny s ``-Resume`` příznakem.

## <a name="manage-the-u-sql-catalog"></a>Správa katalogu U-SQL

Katalog U-SQL slouží ke strukturování dat a kódu, aby je bylo možné sdílet se skripty U-SQL. Katalog umožňuje nejvyšší možný výkon s daty v Azure Data Lake. Další informace najdete v tématu [Použití katalogu U-SQL](./data-lake-analytics-u-sql-get-started.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Seznam položek v katalogu U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>Vypíše všechna sestavení v katalogu U-SQL.

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Získat podrobnosti o položce katalogu

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>Ukládat přihlašovací údaje v katalogu

V rámci databáze U-SQL vytvořte objekt přihlašovacích údajů pro databázi hostovanou v Azure. V současné době jsou přihlašovací údaje U-SQL jediným typem položky katalogu, kterou můžete vytvořit prostřednictvím PowerShellu.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

## <a name="manage-firewall-rules"></a>Správa pravidel firewallu

### <a name="list-firewall-rules"></a>Vypsat pravidla brány firewall

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>Přidat pravidlo brány firewall

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>Úprava pravidla brány firewall

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Odebrání pravidla brány firewall

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Povolení IP adres Azure

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="working-with-azure"></a>Práce s Azure

### <a name="get-error-details"></a>Získat podrobnosti o chybě

```powershell
Resolve-AzError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Ověřte, jestli na počítači s Windows běží jako správce.

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Najít TenantID

Z názvu předplatného:

```powershell
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

Z ID předplatného:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
```

Z adresy domény, například "contoso.com"

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Vypíše všechna Vaše předplatná a ID tenantů.

```powershell
$subs = Get-AzSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Vytvoření účtu Data Lake Analytics pomocí šablony

Šablonu skupiny prostředků Azure můžete použít také v následující ukázce: [Vytvoření účtu Data Lake Analytics pomocí šablony](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template) .

## <a name="next-steps"></a>Další kroky
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Začínáme s Data Lake Analytics pomocí rozhraní příkazového [Azure portal](data-lake-analytics-get-started-portal.md)  |  [Azure PowerShell](data-lake-analytics-get-started-powershell.md)  |  [řádku Azure](data-lake-analytics-get-started-cli.md) Azure Portal Azure PowerShell
* Správa Azure Data Lake Analytics pomocí [Azure portal](data-lake-analytics-manage-use-portal.md)rozhraní příkazového  |  [Azure PowerShell](data-lake-analytics-manage-use-powershell.md)  |  [řádku](data-lake-analytics-manage-use-cli.md) Azure Portal Azure PowerShell