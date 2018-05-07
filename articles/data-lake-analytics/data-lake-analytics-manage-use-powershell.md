---
title: Správa Azure Data Lake Analytics pomocí Azure PowerShell | Microsoft Docs
description: 'Naučte se spravovat účty Data Lake Analytics, zdrojů dat, úlohy a položky katalogu. '
services: data-lake-analytics
documentationcenter: ''
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/23/2017
ms.author: mahi
ms.openlocfilehash: 96360eabefcbbdf36ef3bd83b0c6de45c1a6f3cc
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Správa Azure Data Lake Analytics pomocí Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Naučte se spravovat účty Azure Data Lake Analytics, zdrojů dat, úlohy a položky katalogu pomocí prostředí Azure PowerShell. 

## <a name="prerequisites"></a>Požadavky

Při vytváření účtu Data Lake Analytics, musíte znát:

* **ID předplatného**: ID předplatného Azure, ve kterém se nachází váš účet Data Lake Analytics.
* **Skupina prostředků**: název skupiny prostředků Azure, která obsahuje váš účet Data Lake Analytics.
* **Název účtu data Lake Analytics**: název účtu musí obsahovat jenom malá písmena a číslice.
* **Výchozí účet Data Lake Store:** Každý účet Data Lake Analytics má výchozí účet Data Lake Store. Tyto účty musí být ve stejném umístění.
* **Umístění**: umístění vašeho účtu Data Lake Analytics, jako je například "East US 2" nebo jiné podporované umístění. Podporovaná umístění můžete zobrazit na našem [stránce s cenami](https://azure.microsoft.com/pricing/details/data-lake-analytics/).

Fragment kódu PowerShellu v tomto kurzu používá následující proměnné k ukládání příslušných informací:

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>Přihlásit se

Přihlaste se pomocí id předplatného.

```powershell
Connect-AzureRmAccount -SubscriptionId $subId
```

Přihlaste se pomocí název odběru.

```
Connect-AzureRmAccount -SubscriptionName $subname 
```

`Connect-AzureRmAccount` Rutiny vždycky zobrazí výzvu k zadání pověření. Můžete vyhnout se zobrazí výzva, a to pomocí následujících rutin:

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

## <a name="manage-accounts"></a>Správa účtů

### <a name="create-a-data-lake-analytics-account"></a>Vytvoření účtu Data Lake Analytics

Pokud ještě nemáte [skupiny prostředků](../azure-resource-manager/resource-group-overview.md#resource-groups) Pokud chcete používat, vytvořte jeden. 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Každý účet Data Lake Analytics vyžaduje výchozí účet Data Lake Store, který slouží k ukládání protokolů. Můžete znovu použít existující účet nebo si vytvořit účet. 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

Jakmile budete mít k dispozici skupinu prostředků a účet Data Lake Store, vytvořte účet Data Lake Analytics.

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-acount-information"></a>Získání informací o účet

Získejte podrobnosti o účtu.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

Zkontrolujte existenci určitého účtu Data Lake Analytics. Vrátí rutina `$true` nebo `$false`.

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

Zkontrolujte existenci určitého účtu Data Lake Store. Vrátí rutina `$true` nebo `$false`.

```powershell
Test-AdlStoreAccount -Name $adls
```

### <a name="list-accounts"></a>Výpis účtů

Seznam Data Lake Analytics účty v aktuálním předplatném.

```powershell
Get-AdlAnalyticsAccount
```

Seznam Data Lake Analytics účtů v rámci určité skupiny zdrojů.

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="manage-data-sources"></a>Správa zdrojů dat
Azure Data Lake Analytics teď podporuje následující zdroje dat:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Když vytvoříte účet Analytics, je třeba určit účet Data Lake Store jako zdroj dat výchozí. Výchozí účet Data Lake Store se používá k ukládání metadat a úlohy auditu v protokolech úloh. Po vytvoření účtu Data Lake Analytics, můžete přidat další účty Data Lake Store a účty úložiště. 

### <a name="find-the-default-data-lake-store-account"></a>Najít výchozího účtu Data Lake Store

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Výchozí účet Data Lake Store můžete najít pomocí filtrování seznamu zdrojů dat pomocí `IsDefault` vlastnost:

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
Get-AdlAnalyticsDataSource -Name $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Odesílání úloh U-SQL

### <a name="submit-a-string-as-a-u-sql-script"></a>Odeslat řetězec jako skript U-SQL

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

### <a name="submit-a-file-as-a-u-sql-script"></a>Odeslat soubor jako skript U-SQL

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

## <a name="list-jobs-in-an-account"></a>Seznam úloh v účtu

### <a name="list-all-the-jobs-in-the-account"></a>Vypište všechny úlohy v rámci účtu. 

Výstup zahrnuje aktuálně spuštěné úlohy a nedávno dokončené úlohy.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Seznam hlavních úloh

Ve výchozím nastavení seřazeny seznam úloh na odeslání čas. Proto zobrazí první naposledy odeslaných úlohy. Ve výchozím nastavení ADLA účet pamatuje úlohy na 180 dní, ale vrátí Rutina Get-AdlJob ve výchozím nastavení pouze první 500. Pomocí - nejvyšší parametr seznam určitý počet úloh.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-based-on-the-value-of-job-property"></a>Seznam úloh na základě hodnoty vlastnosti úlohy

Pomocí `-State` parametr. Zkombinováním některou z těchto hodnot:

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

Použití `-Result` parametr ke zjištění, zda zakončeno úlohy byl úspěšně dokončen. Obsahuje tyto hodnoty:

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

`-Submitter` Parametr pomáhá identifikovat, kdo odeslání úlohy.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

`-SubmittedAfter` Je užitečný při filtrování časový rozsah.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="analyzing-job-history"></a>Analýza historie úlohy

Použití Azure PowerShell k analýze historii úlohy, které mají běžet v Data Lake analytics je výkonný technika. Můžete ho a získáte přehled o využití a náklady. Další informace prohlížením [úložišti ukázka Analysis historie úlohy](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="get-information-about-pipelines-and-recurrences"></a>Získání informací o kanálů a opakování

Použití `Get-AdlJobPipeline` rutiny, které chcete zobrazit informace o kanálu předchozích odeslaných úlohy.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Použití `Get-AdlJobRecurrence` rutiny zobrazíte informace o opakování pro dříve odeslání úlohy.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="get-information-about-a-job"></a>Získání informací o úlohy

### <a name="get-job-status"></a>Získat stav úlohy

Získejte stav konkrétní úlohy.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="examine-the-job-outputs"></a>Zkontrolujte výstupy úlohy

Po ukončení úlohy, zkontrolujte, jestli výstupní soubor existuje tak, že uvedete souborů ve složce.

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

## <a name="manage-running-jobs"></a>Spravovat spuštěné úlohy

### <a name="cancel-a-job"></a>Zrušení úlohy

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Počkejte na dokončení úlohy

Místo opakující se `Get-AdlAnalyticsJob` , dokud nebude dokončeno úlohu, můžete použít `Wait-AdlJob` rutiny čekat na ukončení úlohy.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="manage-compute-policies"></a>Správa zásad výpočetní

### <a name="list-existing-compute-policies"></a>Seznam existujících zásad výpočetní

`Get-AdlAnalyticsComputePolicy` Rutina načte informace o výpočetní zásady pro účet Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Vytvořit zásadu výpočetní

`New-AdlAnalyticsComputePolicy` Rutina vytvoří novou zásadu výpočetní pro účet Data Lake Analytics. Tento příklad nastaví maximální Austrálie k dispozici pro určeného uživatele na 50 a priority minimální úloh na 250.

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="check-for-the-existence-of-a-file"></a>Zkontrolujte, jestli soubor existuje.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading"></a>Odesílání a stahování

Nahrajte soubor.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Nahrajte rekurzivnímu celou složku.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Stažení souboru.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Stáhněte si rekurzivnímu celou složku.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Pokud dojde k přerušení procesu nahrávání nebo stahování, můžete se pokusit obnovit proces spuštěním rutiny znovu s ``-Resume`` příznak.

## <a name="manage-catalog-items"></a>Spravovat položky katalogu

Katalogu U-SQL se používá k struktury kódu a dat, může být sdílen skriptů U-SQL. Katalogu umožňuje nejvyšší možný s daty v Azure Data Lake výkon. Další informace najdete v tématu [Použití katalogu U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Položky seznamu v katalogu U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

Zobrazí seznam všech sestaveních ve všech databází na ADLA účtu.

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

### <a name="get-details-about-a-catalog-item"></a>Získání podrobností o položek katalogu

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-credentials-in-a-catalog"></a>Vytvořit přihlašovací údaje v katalogu

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

### <a name="get-basic-information-about-an-adla-account"></a>Získejte základní informace o účtu ADLA

Zadaný název účtu, vyhledá následující kód základní informace o účtu

```
$adla_acct = Get-AdlAnalyticsAccount -Name "saveenrdemoadla"
$adla_name = $adla_acct.Name
$adla_subid = $adla_acct.Id.Split("/")[2]
$adla_sub = Get-AzureRmSubscription -SubscriptionId $adla_subid
$adla_subname = $adla_sub.Name
$adla_defadls_datasource = Get-AdlAnalyticsDataSource -Account $adla_name  | ? { $_.IsDefault } 
$adla_defadlsname = $adla_defadls_datasource.Name

Write-Host "ADLA Account Name" $adla_name
Write-Host "Subscription Id" $adla_subid
Write-Host "Subscription Name" $adla_subname
Write-Host "Defautl ADLS Store" $adla_defadlsname
Write-Host 

Write-Host '$subname' " = ""$adla_subname"" "
Write-Host '$subid' " = ""$adla_subid"" "
Write-Host '$adla' " = ""$adla_name"" "
Write-Host '$adls' " = ""$adla_defadlsname"" "
```

## <a name="manage-firewall-rules"></a>Spravovat pravidla brány firewall

### <a name="list-firewall-rules"></a>Seznam pravidel brány firewall

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

### <a name="change-a-firewall-rule"></a>Změna pravidla brány firewall

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Odebrat pravidlo brány firewall

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Povolit Azure IP adresy.

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

### <a name="verify-if-you-are-running-as-an-administrator"></a>Ověřte, zda používáte jako správce

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
function Get-TenantIdFromSubcriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubcriptionName "ADLTrainingMS"
```

Z id předplatného:

```powershell
function Get-TenantIdFromSubcriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubcriptionId $subid
```

Z adresy domény jako je například "contoso.com"


```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Zobrazí seznam všech odběrů a ID klienta

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Vytvoření účtu Data Lake Analytics pomocí šablony

Můžete také použít šablonu skupiny prostředků Azure pomocí následující ukázka: [vytvoření účtu Data Lake Analytics pomocí šablony](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)


## <a name="next-steps"></a>Další postup
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Začínáme s Data Lake Analytics pomocí [portál Azure](data-lake-analytics-get-started-portal.md) | [prostředí Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [2.0 rozhraní příkazového řádku](data-lake-analytics-get-started-cli2.md)
* Správa Azure Data Lake Analytics pomocí [portál Azure](data-lake-analytics-manage-use-portal.md) | [prostředí Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [rozhraní příkazového řádku](data-lake-analytics-manage-use-cli.md) 
