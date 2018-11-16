---
title: Správa Azure Data Lake Analytics pomocí Azure PowerShell
description: Tento článek popisuje, jak pomocí Azure Powershellu ke správě účtů Data Lake Analytics, zdroje dat, uživatele a úlohy.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.topic: conceptual
ms.date: 06/29/2018
ms.openlocfilehash: 5bd8763234aa02d68b6e86b7259fcf10b4ef4ac5
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684271"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Správa Azure Data Lake Analytics pomocí Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Tento článek popisuje, jak spravovat účtů Azure Data Lake Analytics, zdroje dat, uživatele a úlohy pomocí Azure Powershellu.

## <a name="prerequisites"></a>Požadavky

Jak pomocí prostředí PowerShell s Data Lake Analytics, shromážděte následující druhy údajů: 

* **ID předplatného**: ID předplatného Azure, která obsahuje váš účet Data Lake Analytics.
* **Skupina prostředků**: název skupiny prostředků Azure, která obsahuje váš účet Data Lake Analytics.
* **Název účtu data Lake Analytics**: název účtu Data Lake Analytics.
* **Název účtu výchozí Data Lake Store**: účet každý Data Lake Analytics má výchozí účet Data Lake Store.
* **Umístění**: umístění účtu Data Lake Analytics, jako je například "East US 2" nebo jiné podporované umístění.

Fragment kódu PowerShellu v tomto kurzu používá následující proměnné k ukládání příslušných informací:

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Přihlášení k Azure

### <a name="log-in-using-interactive-user-authentication"></a>Přihlaste se pomocí interaktivním ověřování uživatelů

Přihlásit pomocí ID předplatného nebo podle názvu předplatného

```powershell
# Using subscription id
Connect-AzureRmAccount -SubscriptionId $subId

# Using subscription name
Connect-AzureRmAccount -SubscriptionName $subname 
```

## <a name="saving-authentication-context"></a>Ukládá se kontext ověřování

`Connect-AzureRmAccount` Rutiny vždycky zobrazí výzvu k zadání pověření. Můžete se vyhnout, zobrazování výzev pomocí následující rutiny:

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Přihlaste se pomocí instančního objektu Identity služby (SPI)

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname" 
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" 
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" 

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzureRmAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
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

Každý účet Data Lake Analytics vyžaduje výchozí účet Data Lake Store, který slouží k ukládání protokolů. Můžete použít existující účet nebo vytvořit účet. 

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>Získejte informace o účtu

Získáte podrobné informace o účtu.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Zkontrolujte, jestli existuje účet

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Správa zdrojů dat
Azure Data Lake Analytics aktuálně podporuje následující zdroje dat:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Každý účet Data Lake Analytics má výchozí účet Data Lake Store. Výchozí účet Data Lake Store se používá k ukládání protokolů auditu úlohy metadata a úlohy. 

### <a name="find-the-default-data-lake-store-account"></a>Najít výchozí účet Data Lake Store

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Výchozí účet Data Lake Store najdete pomocí filtrování seznamu zdrojů dat pomocí `IsDefault` vlastnost:

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

### <a name="submit-a-string-as-a-u-sql-job"></a>Odeslat řetězec jako úlohu U-SQL

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

### <a name="submit-a-file-as-a-u-sql-job"></a>Odeslat soubor jako úlohu U-SQL

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

### <a name="list-the-top-n-jobs"></a>Zobrazit seznam hlavních úloh

Čas odeslání ve výchozím nastavení seřazeny v seznamu úloh. Takže nedávno odeslané úlohy se zobrazují první. Ve výchozím nastavení i účet The ADLA pamatuje úlohy po dobu 180 dnů, ale Rutina Get-AdlJob ve výchozím nastavení vrací jenom prvních 500. Pomocí parametru - horní parametru do seznamu určitý počet úloh.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Seznam úloh podle stavu úlohy

Použití `-State` parametru. Můžete kombinovat některou z těchto hodnot:

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

### <a name="list-jobs-by-job-result"></a>Seznam úloh podle výsledek úlohy

Použití `-Result` parametr ke zjištění, zda byl ukončen úlohy byla úspěšně dokončena. Obsahuje tyto hodnoty:

* Zrušeno
* Selhalo
* Žádný
* Úspěch

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Seznam úloh podle odesílatel úlohy

`-Submitter` Parametr pomáhá identifikovat, kdo odeslání úlohy.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Seznam úloh podle čas odeslání

`-SubmittedAfter` Je užitečný při filtrování časový rozsah.


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

Namísto opakování `Get-AdlAnalyticsJob` dokud se úloha nedokončí, můžete použít `Wait-AdlJob` rutiny čekání úlohy na konec.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>Analýza historie úlohy

K analýze historie úloh spouštěných ve službě Data Lake analytics pomocí Azure Powershellu je výkonná technika. Slouží k získání přehledu o využití a nákladů. Další informace zobrazením [analýzy historie úlohy ukázkového úložiště](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="list-job-pipelines-and-recurrences"></a>Seznam úloh kanálech a opakováních

Použití `Get-AdlJobPipeline` rutiny zobrazíte informace o kanálu dříve odeslaných úloh.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Použití `Get-AdlJobRecurrence` rutiny zobrazíte informace o opakování dříve odeslaných úloh.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```


## <a name="manage-compute-policies"></a>Správa zásad compute

### <a name="list-existing-compute-policies"></a>Seznam existujících zásad compute

`Get-AdlAnalyticsComputePolicy` Rutina načte informace o zásadách výpočetní prostředky pro účet Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Vytvoření zásad compute

`New-AdlAnalyticsComputePolicy` Rutina vytvoří novou zásadu výpočetní prostředky pro účet Data Lake Analytics. V tomto příkladu nastaví maximální počet jednotek AU, k dispozici pro zadaného uživatele na 50 a Priorita minimální úlohy na 250.

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

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

Stáhněte si soubor.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Stáhněte si celou složku rekurzivně.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Pokud dojde k procesu nahrávání a stahování může pokusit obnovit proces spuštění rutiny. znovu se ``-Resume`` příznak.

## <a name="manage-the-u-sql-catalog"></a>Správa katalogu U-SQL

Katalog U-SQL se používá ke strukturování dat a kód, takže může být sdílen skriptů U-SQL. Katalog umožňuje s daty ve službě Azure Data Lake je to možné nejvyšší výkon. Další informace najdete v tématu [Použití katalogu U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Seznam položek v katalogu U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>Seznam všech sestavení U-SQL katalogu

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

### <a name="get-details-about-a-catalog-item"></a>Získat podrobné informace o vytvoření položky katalogu

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>Store přihlašovací údaje v katalogu

V databázi U-SQL vytvořte objekt přihlašovacích údajů pro databázi hostované v Azure. Přihlašovací údaje U-SQL v současné době jsou jediným typem položek katalogu, které můžete vytvořit pomocí prostředí PowerShell.

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

### <a name="list-firewall-rules"></a>Výpis pravidel brány firewall

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

### <a name="modify-a-firewall-rule"></a>Upravit pravidlo brány firewall

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Odebrat pravidlo brány firewall

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Povolit Azure IP adresy

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```


## <a name="working-with-azure"></a>Práce s Azure

### <a name="get-details-of-azurerm-errors"></a>Získat podrobnosti o chybách AzureRm

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Ověřte, že je spuštěný jako správce na svém počítači s Windows

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Najít ID Tenanta

Z názvu předplatného:

```powershell
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

Z ID předplatného:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
```

Z adresy domény, jako je například "contoso.com"

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Vypsat všechna předplatná a ID tenanta

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Vytvoření účtu Data Lake Analytics pomocí šablony

Můžete také použít šablonu skupiny prostředků Azure pomocí následující ukázky: [vytvoření účtu Data Lake Analytics pomocí šablony](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>Další postup
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Začínáme s Data Lake Analytics pomocí [webu Azure portal](data-lake-analytics-get-started-portal.md) | [prostředí Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [rozhraní příkazového řádku Azure](data-lake-analytics-get-started-cli.md)
* Správa Azure Data Lake Analytics pomocí [webu Azure portal](data-lake-analytics-manage-use-portal.md) | [prostředí Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [rozhraní příkazového řádku](data-lake-analytics-manage-use-cli.md) 
