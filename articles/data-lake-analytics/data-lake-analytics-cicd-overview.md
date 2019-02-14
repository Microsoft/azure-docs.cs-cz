---
title: Jak vytvořit kanál CI/CD pro Azure Data Lake Analytics
description: Zjistěte, jak nastavit průběžnou integraci a průběžné nasazování pro Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 09/14/2018
ms.openlocfilehash: 937e261405634e88ab234d2fe43ee660a3acc417
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233655"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Jak vytvořit kanál CI/CD pro Azure Data Lake Analytics  

V tomto článku se dozvíte, jak nastavit průběžnou integraci a nasazování (CI/CD) kanálů pro úloh U-SQL a databáze U-SQL.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>Použití CI/CD pro úlohy U-SQL

Azure Data Lake Tools pro Visual Studio poskytuje typ projektu U-SQL, který vám pomůže organizovat skriptů U-SQL. Použití projekt U-SQL ke správě kódu U-SQL usnadňuje další scénáře CI/CD.

## <a name="build-a-u-sql-project"></a>Sestavit projekt U-SQL

Předáním odpovídající parametry se dají vytvářet projekt v U-SQL pomocí Microsoft Build Engine (MSBuild). Postupujte podle kroků v tomto článku nastavte proces sestavení pro projekt v U-SQL.

### <a name="project-migration"></a>Migrace projektu

Před nastavením úlohu sestavení pro projekt v U-SQL, ujistěte se, že máte nejnovější verzi projekt v U-SQL. Otevřete soubor projektu U-SQL ve svém editoru a ověřte, že máte tyto položky importovat:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Pokud ne, máte dvě možnosti, jak migrovat projekt:

- Option 1: Změňte staré položky importu s předchozím histogramem.
- Option 2: Původní projekt otevřete v Azure Data Lake Tools pro Visual Studio. Použijte verzi novější než 2.3.3000.0. Starou šablonu projektu budou automaticky upgradovat na nejnovější verzi. Nové projekty vytvořené pomocí verze novější než 2.3.3000.0 pomocí nové šablony.

### <a name="get-nuget"></a>Get NuGet

Nástroj MSBuild neposkytuje integrovanou podporu pro projekty v U-SQL. Pokud chcete získat tuto podporu, budete muset přidat odkaz pro vaše řešení na [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) balíčku NuGet, který přidá službu požadovaný jazyk.

Chcete-li přidat odkaz na balíček NuGet, klikněte pravým tlačítkem na řešení v Průzkumníku řešení sady Visual Studio a zvolte **spravovat balíčky NuGet**. Nebo můžete přidat soubor s názvem `packages.config` ve složce řešení a vložit následující obsah do ní:

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Spravovat odkazy na databázi U-SQL

Skripty U-SQL v projektu U-SQL pravděpodobně příkazy dotazu pro objekty databáze U-SQL. V takovém případě budete muset odkaz odpovídající projekt databáze U-SQL, který zahrnuje definice objektů před sestavením projektu U-SQL. Příkladem je při dotazování tabulky U-SQL nebo odkazovat na sestavení. 

Další informace o [projekt v U-SQL databáze](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
>Projekt U-SQL database je aktuálně ve verzi public preview. Pokud máte příkazu PŘETAŽENÍ v projektu, sestavení selže. Příkaz DROP bude brzy povolená.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Projekt U-SQL pomocí příkazového řádku MSBuild sestavit

Nejprve projekt migrovat a získat balíček NuGet. Potom zavolejte standardní příkazového řádku MSBuild s následujícími další argumenty k sestavení projektu U-SQL: 

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

Definice argumentů a hodnoty jsou následující:

* **USQLSDKPath = < balíček Nuget U-SQL > \build\runtime**. Tento parametr odkazuje na cestu instalace balíčku NuGet pro služby jazyka U-SQL.
* **USQLTargetType=Merge or SyntaxCheck**:
    * **Sloučit**. Sloučení režimu zkompiluje soubory kódu na pozadí. Mezi příklady patří **.cs**, **.py**, a **.r** soubory. To inlines výslednou knihovnu uživatelský kód do skriptu U-SQL. Mezi příklady patří binární soubor knihovny dll, Python nebo R kódu.
    * **SyntaxCheck**. Režim SyntaxCheck nejprve sloučí soubory kódu na pozadí skript U-SQL. Pak zkompiluje skript U-SQL k ověření kódu.
* **DataRoot=<DataRoot path>**. Pouze u SyntaxCheck režimu, je potřeba DataRoot. Při vytváření skriptu s režimem SyntaxCheck, zkontroluje MSBuild odkazy na objekty databáze ve skriptu. Před sestavením, nastavte odpovídající místní prostředí, který obsahuje odkazované objekty z databáze U-SQL ve složce DataRoot počítač sestavení. Můžete také spravovat tyto databáze závislostí podle [odkazování na projekt U-SQL database](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). Nástroj MSBuild zkontroluje pouze odkazy na objekty databáze, nikoli soubory.
* **EnableDeployment = true** nebo **false**. EnableDeployment Určuje, zda je povolen nasadit odkazované databáze U-SQL během procesu sestavení. Je-li odkazovat na databázový projekt U-SQL a využívat databázových objektů ve vašem skriptu U-SQL, nastavte tento parametr na **true**.

### <a name="continuous-integration-through-azure-pipelines"></a>Průběžná integrace pomocí kanálů Azure

Kromě příkazového řádku můžete také použít Visual Studio Build nebo úlohu nástroje MSBuild k sestavení projektů U-SQL v Azure kanály. Nastavení kanálu sestavení, nezapomeňte přidat dvě úlohy v kanálu sestavení: Úloha obnovení NuGet a úlohu nástroje MSBuild.

![MSBuild – úloha pro projekt v U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

1.  Přidat úlohu obnovení NuGet pro balíček NuGet odkazované řešení, zahrnující `Azure.DataLake.USQL.SDK`tak, aby MSBuild najdete cíle jazyk U-SQL. Nastavte **Upřesnit** > **cílový adresář** k `$(Build.SourcesDirectory)/packages` Pokud budete chtít použít ukázkový argumenty nástroje MSBuild přímo v kroku 2.

    ![Úloha obnovení NuGet pro projekt v U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Nastavte argumenty nástroje MSBuild v nástrojích pro sestavení sady Visual Studio nebo v úkolu MSBuild, jak je znázorněno v následujícím příkladu. Nebo můžete definovat proměnné pro tyto argumenty v kanálu Azure kanály sestavení.

    ![Definujte proměnné, MSBuild CI/CD pro projekt v U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

### <a name="u-sql-project-build-output"></a>Výstupní sestavení projektu U-SQL

Po spuštění sestavení všechny skripty v projektu U-SQL Sestavujeme a výstup do souboru zip s názvem `USQLProjectName.usqlpack`. Struktura složek ve vašem projektu, zůstane ve výstupu ZIP sestavení.

> [!NOTE]
>
> Jako příkaz vložené do skriptu sestavení výstupu se sloučí soubory kódu na pozadí pro každý skript U-SQL.
>

## <a name="test-u-sql-scripts"></a>Testování skriptů U-SQL

Azure Data Lake nabízí projektů testování skriptů U-SQL a C# UDO/UDAG/UDF:
* Zjistěte, jak [přidat testovací případy pro skripty U-SQL a rozšířené kód jazyka C#](data-lake-analytics-cicd-test.md#test-u-sql-scripts).
* Zjistěte, jak [spouštění testovacích případů v kanálech Azure](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Nasazení úlohy U-SQL

Po ověření kódu pomocí procesu sestavení a testování, je odesílání úloh U-SQL přímo z Azure kanály prostřednictvím úlohu prostředí Azure PowerShell. Skript můžete také nasadit do Azure Data Lake Store nebo Azure Blob storage a [spouštět plánované úlohy prostřednictvím Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Odesílání úloh U-SQL pomocí Azure kanály

Výstupní sestavení projektu U-SQL je soubor zip se nazývá **USQLProjectName.usqlpack**. Tento zazipovaný soubor obsahuje všechny skripty U-SQL v projektu. Můžete použít [úloh prostředí Azure PowerShell](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) v kanálech s následující ukázkový skript prostředí PowerShell k odesílání úloh U-SQL přímo z Azure kanály.

```powershell
<#
    This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and submit all scripts one-by-one.

    Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.
          
    Example :
        USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

    return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."

    # Submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
    }
}

Function LogJobInformation($jobInfo)
{
    Write-Output "************************************************************************"
    Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
    Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
    Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
    Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
    Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
    Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
    Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
    if ($item -ne $null)
    {
        return $item
    }
    return ""
}

Function Main()
{
    Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
    Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
    Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

    Write-Output "Starting USQL script deployment..."
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Nasazení úloh U-SQL pomocí Azure Data Factory

Můžete odeslat úloh U-SQL přímo z Azure kanály. Nebo můžete nahrát připravených skriptů do služby Azure Data Lake Store nebo Azure Blob storage a [spouštět plánované úlohy prostřednictvím Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Použití [úloh prostředí Azure PowerShell](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) v kanálech Azure s následující ukázkový skript Powershellu pro odeslání skriptů U-SQL do účtu služby Azure Data Lake Store:

```powershell
<#
    This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.
          
    Example :
        FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DestinationFolder = "USQLScriptSource" # Destination folder in ADLS
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles

    $files = @(get-childitem $usqlScripts -recurse)
    foreach($file in $files)
    {
        Write-Host "Uploading file: $($file.Name)"
        Import-AzDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DestinationFolder $file)" -Force
    }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-a-u-sql-database"></a>CI/CD pro databázi U-SQL

Azure Data Lake Tools pro Visual Studio obsahuje šablony projektů databáze U-SQL, které vám pomůžou vývoj, Správa a nasazení databáze U-SQL. Další informace [projekt v U-SQL databáze](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Sestavit projekt U-SQL database

### <a name="get-the-nuget-package"></a>Získat balíček NuGet.

Nástroj MSBuild neposkytuje integrovanou podporu pro projekty databáze U-SQL. Pokud chcete získat tuto možnost, budete muset přidat odkaz pro řešení [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) balíčku NuGet, který přidá službu požadovaný jazyk.

Chcete-li přidat odkaz na balíček NuGet, klikněte pravým tlačítkem na řešení v Průzkumníku řešení sady Visual Studio. Zvolte **spravovat balíčky NuGet**. Vyhledejte a nainstalujte balíček NuGet. Nebo můžete přidat soubor s názvem **souboru packages.config** ve složce řešení a vložit následující obsah do ní:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Sestavení projektu databáze pomocí příkazového řádku MSBuild U-SQL

K sestavení projektu U-SQL databáze, volání standardní příkazový řádek MSBuild a jako další argument se předá odkaz na balíček NuGet sady SDK pro U-SQL. Prohlédněte si následující příklad: 

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Argument `USQLSDKPath=<U-SQL Nuget package>\build\runtime` odkazuje na cestu instalace balíčku NuGet pro služby jazyka U-SQL.

### <a name="continuous-integration-with-azure-pipelines"></a>Průběžná integrace se sadou Azure kanály

Kromě příkazového řádku můžete použít Visual Studio Build nebo úlohu nástroje MSBuild k sestavení projektů U-SQL database v kanálech Azure. Pokud chcete nastavit úlohu sestavení, nezapomeňte přidat dvě úlohy v kanálu sestavení: Úloha obnovení NuGet a úlohu nástroje MSBuild.

   ![Úlohy MSBuild CI/CD pro projekt v U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 


1.  Přidat úlohu obnovení NuGet pro balíček NuGet odkazované řešení, která zahrnuje získáte `Azure.DataLake.USQL.SDK`tak, aby MSBuild najdete cíle jazyk U-SQL. Nastavte **Upřesnit** > **cílový adresář** k `$(Build.SourcesDirectory)/packages` Pokud budete chtít použít ukázkový argumenty nástroje MSBuild přímo v kroku 2.

    ![Úloha NuGet CI/CD pro projekt v U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Nastavte argumenty nástroje MSBuild v nástrojích pro sestavení sady Visual Studio nebo v úkolu MSBuild, jak je znázorněno v následujícím příkladu. Nebo můžete definovat proměnné pro tyto argumenty v kanálu Azure kanály sestavení.

   ![Definujte proměnné, MSBuild CI/CD pro projekt databáze U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
    ```
 
### <a name="u-sql-database-project-build-output"></a>Výstupní sestavení projektu databáze U-SQL

Výstup pro databázový projekt U-SQL je U-SQL database balíček pro nasazení s názvem s příponou sestavení `.usqldbpack`. `.usqldbpack` Balíček je soubor zip, který obsahuje všechny příkazy DDL v jeden skript U-SQL ve složce DDL. Obsahuje všechny **knihovny DLL debuggle** a dalších souborů sestavení do dočasné složky.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Testování funkce hodnot tabulky a uložené procedury

Přidávání testovacích případů pro funkce hodnot tabulky a uložené procedury přímo se momentálně nepodporuje. Jako alternativní řešení můžete vytvořit projekt U-SQL, který má skriptů U-SQL, které volání těchto funkcí a jejich zápis testovací případy. Proveďte následující kroky k nastavení testovacích případů pro funkce hodnot tabulky a uložené procedury, které jsou definované v projektu databáze U-SQL:

1.  Vytvořte projekt U-SQL pro účely testování a psaní skriptů U-SQL, volání funkce hodnot tabulky a uložené procedury.
2.  Přidáte databázový odkaz na projekt U-SQL. Chcete-li získat funkce vracející tabulku a definice uložené procedury, budete muset odkaz databázový projekt, který obsahuje příkaz DDL. Další informace o [databáze odkazy](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Přidáte testovací případy pro skripty U-SQL, které volají funkce hodnot tabulky a uložené procedury. Zjistěte, jak [přidat testovací případy pro skripty U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Nasazení databáze U-SQL pomocí Azure kanály

`PackageDeploymentTool.exe` poskytuje programování a rozhraní příkazového řádku, které pomáhají nasadit balíčky pro nasazení databáze U-SQL, **.usqldbpack**. Je součástí sady SDK [balíček NuGet sady SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), který je umístěn na **build/runtime/PackageDeploymentTool.exe**. S použitím `PackageDeploymentTool.exe`, databáze U-SQL můžete nasadit do Azure Data Lake Analytics a místními účty.

> [!NOTE]
>
> Podporu příkazového řádku Powershellu a úkol uvolnění kanály Azure podporují pro nasazení databáze U-SQL čeká na vyřízení.
>

Následujícím postupem nastavit úlohu nasazení databáze v kanálech Azure:

1. Přidat Powershellový skript úkolu v sestavení a kanál verze a spusťte následující skript prostředí PowerShell. Umožňuje získat závislosti sady Azure SDK pro tuto úlohu `PackageDeploymentTool.exe` a `PackageDeploymentTool.exe`. Můžete nastavit **- AzureSDK** a **- DBDeploymentTool** parametry pro načtení závislostí a nástroj pro nasazení do určených složek. Předání **- AzureSDK** cestu k `PackageDeploymentTool.exe` jako **- AzureSDKPath** parametr v kroku 2. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
        [string]$workingfolder = "" # Folder to execute these command lines
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.5.1-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.4.1-preview -outf Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.4.1-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.4.1-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. Přidat **příkazového řádku úkolu** v kanálu pro sestavování nebo vydávání a vyplňte skript voláním `PackageDeploymentTool.exe`. `PackageDeploymentTool.exe` se nachází pod definovanou **$DBDeploymentTool** složky. Ukázkový skript vypadá takto: 

    * Nasazení databáze U-SQL místně:

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Nasazení databáze U-SQL k účtu Azure Data Lake Analytics pomocí ověřování interaktivní režim:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Použití **secrete** ověřování nasazení databáze U-SQL k účtu Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Použití **Soubor_certifikátu** ověřování nasazení databáze U-SQL k účtu Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>Popisy parametrů PackageDeploymentTool.exe

#### <a name="common-parameters"></a>Společné parametry

| Parametr | Popis | Výchozí hodnota | Požaduje se |
|---------|-----------|-------------|--------|
|Balíček|Cesta k balíčku pro nasazení databáze U-SQL k nasazení.|null|true (pravda)|
|Databáze|Název databáze a nasazené nebo vytvořen.|master|false (nepravda)|
|LogFile|Cestu k souboru pro protokolování. Standardní navýšení kapacity (konzola) ve výchozím nastavení.|null|false (nepravda)|
|LogLevel|Úroveň protokolu: Podrobné nastavení, Normální, upozornění nebo chyby.|LogLevel.Normal|false (nepravda)|

#### <a name="parameter-for-local-deployment"></a>{{Parametr pro místní nasazení

|Parametr|Popis|Výchozí hodnota|Požaduje se|
|---------|-----------|-------------|--------|
|Kořenová datová složka|Cesta kořenové složce místní data.|null|true (pravda)|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parametry pro nasazení Azure Data Lake Analytics

|Parametr|Popis|Výchozí hodnota|Požaduje se|
|---------|-----------|-------------|--------|
|Účet|Určuje, které účtu Azure Data Lake Analytics k nasazení podle názvu účtu.|null|true (pravda)|
|ResourceGroup|Název skupiny prostředků Azure pro účet Azure Data Lake Analytics.|null|true (pravda)|
|SubscriptionId|ID předplatného Azure pro účet Azure Data Lake Analytics.|null|true (pravda)|
|Tenant|Název tenanta je název domény služby Azure Active Directory (Azure AD). Nachází se na stránku Správa předplatného na webu Azure Portal.|null|true (pravda)|
|AzureSDKPath|Cesty hledání závislých sestavení v sadě Azure SDK.|null|true (pravda)|
|Interaktivní|Jestli se mají použít interaktivní režim ověřování.|false (nepravda)|false (nepravda)|
|ClientId|Vyžaduje se ID aplikace Azure AD pro neinteraktivní ověřování.|null|Vyžaduje se pro neinteraktivní ověřování.|
|Secrete|Secrete nebo heslo pro neinteraktivní ověřování. Byste měli použít pouze ve důvěryhodné a zabezpečené prostředí.|null|Vyžaduje se pro neinteraktivní ověřování, jinak použijte SecreteFile.|
|SecreteFile|Soubor uloží secrete nebo heslo pro neinteraktivní ověřování. Ujistěte se, že zajistit jeho číst pouze od aktuálního uživatele.|null|Vyžaduje se pro neinteraktivní ověřování, jinak použijte Secrete.|
|CertFile|Soubor uloží certifikace X.509 pro neinteraktivní ověřování. Ve výchozím nastavení je použití klienta secrete ověřování.|null|false (nepravda)|
| JobPrefix | Předpona pro nasazení databáze U-SQL DDL úlohy. | Deploy_ + DateTime.Now | false (nepravda) |

## <a name="next-steps"></a>Další postup

- [Tom, jak testovat kód Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
- [Spusťte skript U-SQL na místním počítači](data-lake-analytics-data-lake-tools-local-run.md).
- [Použít projekt databáze U-SQL pro vývoj databáze U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).
