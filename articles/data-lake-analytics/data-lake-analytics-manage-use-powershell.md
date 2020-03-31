---
title: Správa Azure Data Lake Analytics pomocí Azure PowerShell
description: Tento článek popisuje, jak pomocí Azure PowerShellu spravovat účty Data Lake Analytics, zdroje dat, uživatele & úlohy.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.topic: conceptual
ms.date: 06/29/2018
ms.openlocfilehash: 4273828c9c2bdb75fcbc1de45da55c5a03dd615f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66156428"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Správa Azure Data Lake Analytics pomocí Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Tento článek popisuje, jak spravovat účty Azure Data Lake Analytics, zdroje dat, uživatele a úlohy pomocí Azure PowerShellu.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li používat PowerShell s analýzou datového jezera, shromažďujte následující informace: 

* **ID předplatného:** ID předplatného Azure, které obsahuje váš účet Data Lake Analytics.
* **Skupina prostředků:** Název skupiny prostředků Azure, která obsahuje váš účet Data Lake Analytics.
* **Název účtu Data Lake Analytics**: Název vašeho účtu Data Lake Analytics.
* **Výchozí název účtu Úložiště dat:** Každý účet Data Lake Analytics má výchozí účet Data Lake Store.
* **Poloha**: Umístění vašeho účtu Data Lake Analytics, například "Východní USA 2" nebo jiných podporovaných lokalit.

Fragment kódu PowerShellu v tomto kurzu používá následující proměnné k ukládání příslušných informací:

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

### <a name="log-in-using-interactive-user-authentication"></a>Přihlášení pomocí interaktivního ověřování uživatele

Přihlášení pomocí ID předplatného nebo podle názvu předplatného

```powershell
# Using subscription id
Connect-AzAccount -SubscriptionId $subId

# Using subscription name
Connect-AzAccount -SubscriptionName $subname 
```

## <a name="saving-authentication-context"></a>Ukládání kontextu ověřování

Rutina `Connect-AzAccount` vždy vyzve k zadání pověření. Můžete se vyhnout vyzvání pomocí následujících rutin:

```powershell
# Save login session information
Save-AzAccounts -Path D:\profile.json  

# Load login session information
Select-AzAccounts -Path D:\profile.json 
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Přihlášení pomocí identity hlavního povinného servisu (SPI)

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

### <a name="get-account-information"></a>Získání informací o účtu

Získejte podrobnosti o účtu.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Kontrola, zda účet existuje

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Správa zdrojů dat
Azure Data Lake Analytics momentálně podporuje následující zdroje dat:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Každý účet Data Lake Analytics má výchozí účet Data Lake Store. Výchozí účet Data Lake Store se používá k ukládání metadat úlohy a protokolů auditu úloh. 

### <a name="find-the-default-data-lake-store-account"></a>Vyhledání výchozího účtu Data Lake Store

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Výchozí účet Data Lake Store můžete najít filtrováním seznamu `IsDefault` zdrojů dat podle vlastnosti:

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

### <a name="list-jobs"></a>Seznam úloh

Výstup zahrnuje aktuálně spuštěné úlohy a nedávno dokončené úlohy.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Seznam nejlepších pracovních míst N

Ve výchozím nastavení je seznam úloh seřazen podle času odeslání. Takže nejnověji odeslané úlohy se objeví jako první. Ve výchozím nastavení účet ADLA pamatuje úlohy po dobu 180 dnů, ale rutina Get-AdlJob ve výchozím nastavení vrátí pouze prvních 500. Pomocí parametru -Top uveďte určitý počet úloh.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Seznam úloh podle stavu úlohy

Pomocí `-State` parametru. Můžete kombinovat některou z těchto hodnot:

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

### <a name="list-jobs-by-job-result"></a>Seznam úloh podle výsledku úlohy

Pomocí `-Result` parametru můžete zjistit, zda byly dokončeny dokončené úlohy úspěšně dokončeny. Má tyto hodnoty:

* Cancelled
* Failed
* Žádný
* Úspěch

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Seznam úloh podle předkladatele úloh

Parametr `-Submitter` vám pomůže určit, kdo odeslal úlohu.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Seznam úloh podle doby odeslání

Je `-SubmittedAfter` užitečné při filtrování do časového rozsahu.


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

### <a name="wait-for-a-job-to-finish"></a>Počkejte na dokončení úlohy

Místo opakování, `Get-AdlAnalyticsJob` dokud úloha neskončí, `Wait-AdlJob` můžete pomocí rutiny počkat na ukončení úlohy.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>Analýza historie úloh

Použití Azure PowerShellu k analýze historie úloh, které běží v analýze datového jezera, je výkonná technika. Můžete ji použít k získání přehledu o využití a nákladech. Další informace naleznete v [repo části Analýzy historie úloh](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="list-job-pipelines-and-recurrences"></a>Seznam kanálů úloh a opakování

Pomocí `Get-AdlJobPipeline` rutiny zobrazíte informace o kanálu, které byly dříve odeslány.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Pomocí `Get-AdlJobRecurrence` rutiny zobrazíte informace o opakování dříve odeslaných úloh.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```


## <a name="manage-compute-policies"></a>Správa výpočetních zásad

### <a name="list-existing-compute-policies"></a>Seznam existujících zásad výpočetních prostředků

Rutina `Get-AdlAnalyticsComputePolicy` načte informace o zásadách výpočetních prostředků pro účet Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Vytvoření zásad výpočetních prostředků

Rutina `New-AdlAnalyticsComputePolicy` vytvoří nové výpočetní zásady pro účet Data Lake Analytics. Tento příklad nastaví maximální au, které jsou k dispozici pro zadaného uživatele 50 a minimální prioritu úlohy na 250.

```powershell
$userObjectId = (Get-AzAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```
## <a name="manage-files"></a>Správa souborů

### <a name="check-for-the-existence-of-a-file"></a>Zkontrolujte, jestli soubor existuje.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Nahrávání a stahování

Nahrajte soubor.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Nahrajte celou složku rekurzivně.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Stáhněte soubor.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Stáhněte si celou složku rekurzivně.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Pokud je proces nahrávání nebo stahování přerušen, můžete se pokusit proces obnovit ``-Resume`` znovu spuštěním rutiny s příznakem.

## <a name="manage-the-u-sql-catalog"></a>Správa katalogu U-SQL

Katalog U-SQL se používá ke strukturování dat a kódu, takže je mohou sdílet skripty U-SQL. Katalog umožňuje nejvyšší možný výkon s daty v Azure Data Lake. Další informace najdete v tématu [Použití katalogu U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Seznam položek v katalogu U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>Seznam všech sestavení katalogu U-SQL

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

### <a name="get-details-about-a-catalog-item"></a>Získání podrobností o položce katalogu

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>Uložení přihlašovacích údajů v katalogu

V rámci databáze U-SQL vytvořte objekt pověření pro databázi hostovanou v Azure. V současné době u-SQL pověření jsou jediným typem položky katalogu, které můžete vytvořit prostřednictvím prostředí PowerShell.

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

## <a name="manage-firewall-rules"></a>Správa pravidel brány firewall

### <a name="list-firewall-rules"></a>Seznam pravidel brány firewall

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>Přidání pravidla brány firewall

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>Změna pravidla brány firewall

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

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Ověření, zda používáte jako správce počítače se systémem Windows

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Vyhledání id klienta

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

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Seznam všech předplatných a ID klienta

```powershell
$subs = Get-AzSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Vytvoření účtu Data Lake Analytics pomocí šablony

Šablonu Skupiny prostředků Azure můžete taky použít pomocí následující ukázky: [Vytvoření účtu Data Lake Analytics pomocí šablony.](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>Další kroky
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Začínáme s Analýzou datových jezer pomocí azure [portálu](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [Azure CLI](data-lake-analytics-get-started-cli.md)
* Správa Azure Data Lake Analytics pomocí [Azure Portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) 
