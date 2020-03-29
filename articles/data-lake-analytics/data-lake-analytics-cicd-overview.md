---
title: Jak nastavit kanál CI/CD pro Azure Data Lake Analytics
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
ms.openlocfilehash: b035be727df2dfecb613da79681affd740c69bec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60333820"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Jak nastavit kanál CI/CD pro Azure Data Lake Analytics  

V tomto článku se dozvíte, jak nastavit kanál průběžné integrace a nasazení (CI/CD) pro úlohy U-SQL a u-SQL databáze.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>Použití CI/CD pro úlohy U-SQL

Nástroje datového jezera Azure pro Visual Studio poskytuje typ projektu U-SQL, který vám pomůže uspořádat skripty U-SQL. Použití projektu U-SQL ke správě kódu U-SQL usnadňuje další scénáře CI/CD.

## <a name="build-a-u-sql-project"></a>Sestavení projektu U-SQL

U-SQL projekt lze sestavit pomocí Modulu pro sestavení Microsoft (MSBuild) předáním odpovídající parametry. Podle kroků v tomto článku nastavte proces sestavení pro projekt U-SQL.

### <a name="project-migration"></a>Migrace projektu

Před nastavením úlohy sestavení pro projekt U-SQL se ujistěte, že máte nejnovější verzi projektu U-SQL. Otevřete soubor projektu U-SQL v editoru a ověřte, zda máte tyto položky importu:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Pokud tomu tak není, máte dvě možnosti migrace projektu:

- Možnost 1: Změňte starou položku importu na předchozí položku.
- Možnost 2: Otevřete starý projekt v nástrojích Azure Data Lake Tools pro Visual Studio. Použijte novější verzi než 2.3.3000.0. Stará šablona projektu bude automaticky upgradována na nejnovější verzi. Nové projekty vytvořené s novějšími verzemi než 2.3.3000.0 používají novou šablonu.

### <a name="get-nuget"></a>Získat NuGet

MSBuild neposkytuje integrovanou podporu pro projekty U-SQL. Chcete-li získat tuto podporu, musíte přidat odkaz pro vaše řešení do balíčku [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet, který přidá požadovanou jazykovou službu.

Chcete-li přidat odkaz na balíček NuGet, klepněte pravým tlačítkem myši na řešení v Průzkumníkovi řešení sady Visual Studio a zvolte **Spravovat balíčky NuGet**. Nebo můžete přidat soubor `packages.config` snázvem ve složce řešení a dát do něj následující obsah:

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Správa odkazů na databázi U-SQL

Skripty U-SQL v projektu U-SQL mohou mít příkazy dotazu pro databázové objekty U-SQL. V takovém případě je třeba odkazovat na odpovídající u-SQL databázový projekt, který obsahuje definici objektů před sestavením projektu U-SQL. Příkladem je, když dotaz u-SQL tabulky nebo odkaz na sestavení. 

Další informace o [projektu databáze U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
>DROP prohlášení může způsobit problém s odstraněním nehody. Chcete-li povolit příkaz DROP, musíte explicitně zadat argumenty MSBuild. **AllowDropStatement** povolí operaci DROP, která nesouvisí s daty, jako je funkce přetažení sestavení a tabulky přetažení. **AllowDataDropStatement** povolí operaci drop související s daty, jako je schéma přetažení a přetažení. Před použitím příkazu AllowDataDropStatement musíte povolit funkci AllowDropStatement.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Vytvoření projektu U-SQL pomocí příkazového řádku MSBuild

Nejprve migrujte projekt a získejte balíček NuGet. Potom zavolejte standardní příkazový řádek MSBuild s následujícími dalšími argumenty pro sestavení projektu U-SQL: 

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

Definice argumentů a hodnoty jsou následující:

* **USQLSDKPath=\<Balíček U-SQL Nuget>\build\runtime**. Tento parametr odkazuje na cestu instalace balíčku NuGet pro jazykovou službu U-SQL.
* **USQLTargetType=Sloučit nebo SyntaxCheck**:
    * **Sloučit**. Režim sloučení zkompiluje soubory na pozadí kódu. Příkladem jsou soubory **.cs**, **.py**a **.r.** Zapisuje výslednou uživatelsky definovanou knihovnu kódu do skriptu U-SQL. Příklady jsou dll binární, Python nebo R kód.
    * **SyntaxCheck**. Režim SyntaxCheck nejprve sloučí soubory s kódem na pozadí do skriptu U-SQL. Pak zkompiluje skript U-SQL k ověření kódu.
* **DataRoot\<= cesta DataRoot>**. DataRoot je potřeba pouze pro režim SyntaxCheck. Když vytvoří skript s režimem SyntaxCheck, MSBuild zkontroluje odkazy na databázové objekty ve skriptu. Před sestavením nastavte odpovídající místní prostředí, které obsahuje odkazované objekty z databáze U-SQL ve složce DataRoot sestavení počítače. Tyto závislosti databáze můžete také spravovat [odkazem na databázový projekt U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). MSBuild kontroluje pouze odkazy na databázové objekty, nikoli soubory.
* **EnableDeployment=true** nebo **false**. EnableDeployment označuje, pokud je povoleno nasadit odkazované databáze U-SQL během procesu sestavení. Pokud odkazujete na databázový projekt U-SQL a spotřebováváte databázové objekty ve skriptu U-SQL, nastavte tento parametr na **hodnotu true**.

### <a name="continuous-integration-through-azure-pipelines"></a>Průběžná integrace prostřednictvím Azure Pipelines

Kromě příkazového řádku můžete také použít visual studio sestavení nebo úlohu MSBuild k vytváření projektů U-SQL v Azure Pipelines. Chcete-li nastavit kanál sestavení, nezapomeňte přidat dva úkoly v kanálu sestavení: nuget obnovení úlohy a úlohy MSBuild.

![Úkol MSBuild pro projekt U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

1.  Přidejte úlohu obnovení NuGet získat balíček NuGet `Azure.DataLake.USQL.SDK`s odkazem na řešení, který obsahuje , aby MSBuild mohl najít cíle jazyka U-SQL. Pokud chcete použít `$(Build.SourcesDirectory)/packages` ukázku argumentů MSBuild přímo v kroku 2, nastavte adresář **Upřesnit** > **cíl** na hodnotu MSBuild.

    ![NuGet obnovení úkolu pro projekt U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Nastavte argumenty MSBuild v nástrojích sestavení sady Visual Studio nebo v úloze MSBuild, jak je znázorněno v následujícím příkladu. Nebo můžete definovat proměnné pro tyto argumenty v kanálu sestavení Azure Pipelines.

    ![Definování ci/CD MSBuild proměnných pro projekt U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

### <a name="u-sql-project-build-output"></a>Výstup sestavení projektu U-SQL

Po spuštění sestavení jsou vytvořeny všechny skripty v projektu U-SQL a `USQLProjectName.usqlpack`výstup do souboru ZIP s názvem . Struktura složek v projektu je uložena ve výstupu sestavení zip.

> [!NOTE]
>
> Soubory s kódem na pozadí pro každý skript U-SQL budou sloučeny jako vložkový příkaz do výstupu sestavení skriptu.
>

## <a name="test-u-sql-scripts"></a>Testování skriptů U-SQL

Azure Data Lake poskytuje testovací projekty pro u-SQL skripty a C# UDO/UDAG/UDF:
* Zjistěte, jak [přidat testovací případy pro u-SQL skripty a rozšířený kód C#](data-lake-analytics-cicd-test.md#test-u-sql-scripts).
* Zjistěte, jak [spustit testovací případy v Azure Pipelines](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Nasazení úlohy U-SQL

Po ověření kódu prostřednictvím procesu sestavení a testování můžete odeslat úlohy U-SQL přímo z Azure Pipelines prostřednictvím úlohy Azure PowerShell. Skript můžete nasadit taky do Azure Data Lake Store nebo úložiště objektů blob Azure a [spustit naplánované úlohy prostřednictvím Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Odeslání úloh U-SQL prostřednictvím Azure Pipelines

Výstup sestavení projektu U-SQL je soubor zip s názvem **USQLProjectName.usqlpack**. Soubor zip obsahuje všechny u-SQL skripty v projektu. [Úlohu Azure PowerShellu](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) v kanálech můžete použít s následujícím ukázkovým skriptem PowerShellu k odeslání úloh U-SQL přímo z Azure Pipelines.

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

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Nasazení úloh U-SQL prostřednictvím Azure Data Factory

Můžete odeslat u-SQL úlohy přímo z Azure Pipelines. Nebo můžete nahrát vytvořené skripty do Azure Data Lake Store nebo úložiště objektů blob Azure a [spustit naplánované úlohy prostřednictvím Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Pomocí [úlohy Azure PowerShell](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) u Azure Pipelines s následujícím ukázkovým skriptem PowerShellu nahrajte skripty U-SQL do účtu Azure Data Lake Store:

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

Nástroje datového jezera Azure pro Visual Studio poskytují šablony projektů databáze U-SQL, které vám pomůžou vyvíjet, spravovat a nasazovat databáze U-SQL. Další informace o [databázovém projektu U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Sestavení databázového projektu U-SQL

### <a name="get-the-nuget-package"></a>Získání balíčku NuGet

MSBuild neposkytuje integrovanou podporu pro databázové projekty U-SQL. Chcete-li získat tuto možnost, musíte přidat odkaz pro vaše řešení do balíčku [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet, který přidá požadovanou jazykovou službu.

Chcete-li přidat odkaz na balíček NuGet, klepněte pravým tlačítkem myši na řešení v Průzkumníku řešení sady Visual Studio. Zvolte **Spravovat balíčky NuGet**. Pak vyhledejte a nainstalujte balíček NuGet. Nebo můžete přidat soubor s názvem **packages.config** ve složce řešení a vložit do něj následující obsah:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Vytvoření databázového projektu U-SQL pomocí příkazového řádku MSBuild

Chcete-li vytvořit databázový projekt U-SQL, zavolejte standardní příkazový řádek MSBuild a předejte odkaz na balíček U-SQL SDK NuGet jako další argument. Prohlédněte si následující příklad: 

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Argument `USQLSDKPath=<U-SQL Nuget package>\build\runtime` odkazuje na instalační cestu balíčku NuGet pro službu jazyka U-SQL.

### <a name="continuous-integration-with-azure-pipelines"></a>Průběžná integrace s využitím Azure Pipelines

Kromě příkazového řádku můžete k vytváření databázových projektů U-SQL v Azure Pipelines použít visual studio build nebo úlohu MSBuild. Chcete-li nastavit úlohu sestavení, nezapomeňte přidat dva úkoly v kanálu sestavení: nuget obnovení úlohy a úlohy MSBuild.

   ![CI/CD MSBuild úkol pro projekt U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 


1. Přidejte úlohu obnovení NuGet získat balíček NuGet `Azure.DataLake.USQL.SDK`s odkazem na řešení, který obsahuje , aby MSBuild mohl najít cíle jazyka U-SQL. Pokud chcete použít `$(Build.SourcesDirectory)/packages` ukázku argumentů MSBuild přímo v kroku 2, nastavte adresář **Upřesnit** > **cíl** na hodnotu MSBuild.

   ![ÚKOL CI/CD NuGet pro projekt U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Nastavte argumenty MSBuild v nástrojích sestavení sady Visual Studio nebo v úloze MSBuild, jak je znázorněno v následujícím příkladu. Nebo můžete definovat proměnné pro tyto argumenty v kanálu sestavení Azure Pipelines.

   ![Definování ci/CD msbuild proměnných pro databázový projekt U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

   ```
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```
 
### <a name="u-sql-database-project-build-output"></a>Výstup sestavení projektu databáze U-SQL

Výstup sestavení pro databázový projekt U-SQL je balíček nasazení databáze U-SQL s názvem s příponou `.usqldbpack`. Balíček `.usqldbpack` je soubor ZIP, který obsahuje všechny příkazy DDL v jednom skriptu U-SQL ve složce DDL. Obsahuje všechny **dlls** a další soubory pro sestavení v dočasné složce.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Testování funkcí s hodnotou tabulky a uložených procedur

Přidání testovacích případů pro funkce s hodnotou tabulky a přímo uložené procedury není aktuálně podporováno. Jako řešení můžete vytvořit projekt U-SQL, který má skripty U-SQL, které tyto funkce volají a píší pro ně testovací případy. Postupujte podle následujících kroků k nastavení testovacích případů pro funkce s hodnotou tabulky a uložené procedury definované v databázovém projektu U-SQL:

1.  Vytvořte projekt U-SQL pro testovací účely a napište skripty U-SQL, které volají funkce s hodnotou tabulky a uložené procedury.
2.  Přidejte odkaz na databázi projektu U-SQL. Chcete-li získat funkci s hodnotou tabulky a definici uložené procedury, musíte odkazovat na databázový projekt, který obsahuje příkaz DDL. Další informace o [odkazech na databázi](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Přidejte testovací případy pro skripty U-SQL, které volají funkce s hodnotou tabulky a uložené procedury. Přečtěte si, jak [přidat testovací případy pro skripty U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Nasazení databáze U-SQL prostřednictvím Azure Pipelines

`PackageDeploymentTool.exe`Poskytuje rozhraní pro programování a příkazový řádek, která pomáhají nasadit balíčky pro nasazení databáze U-SQL **.usqldbpack**. Sada SDK je součástí [balíčku U-SQL SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), který se nachází na **adrese build/runtime/PackageDeploymentTool.exe**. Pomocí `PackageDeploymentTool.exe`aplikace můžete nasadit databáze U-SQL do Azure Data Lake Analytics i do místních účtů.

> [!NOTE]
>
> Podpora příkazového řádku prostředí PowerShell a podpora úloh uvolnění azure kanálu pro nasazení databáze U-SQL je aktuálně čeká na vyřízení.
>

Pomocí následujících kroků nastavte úlohu nasazení databáze v Azure Pipelines:

1. Přidejte úlohu skriptu prostředí PowerShell do kanálu sestavení nebo vydání a spusťte následující skript Prostředí PowerShell. Tato úloha pomáhá získat závislosti sady `PackageDeploymentTool.exe` `PackageDeploymentTool.exe`Azure SDK pro a . Můžete nastavit parametry **-AzureSDK** a **-DBDeploymentTool** pro načtení závislostí a nástroje pro nasazení do konkrétních složek. Předaj cestu **-AzureSDK** jako `PackageDeploymentTool.exe` parametr **-AzureSDKPath** v kroku 2. 

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

2. Přidejte **úlohu příkazového řádku** do kanálu sestavení nebo `PackageDeploymentTool.exe`vydání a vyplňte skript voláním . `PackageDeploymentTool.exe`je umístěna pod definovanou **$DBDeploymentTool** složce. Ukázkový skript je následující: 

    * Místní nasazení databáze U-SQL:

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Pomocí režimu interaktivního ověřování můžete nasadit databázi U-SQL do účtu Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Pomocí ověřování **v ylokaka** nasadit databázi U-SQL do účtu Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Pomocí ověřování **certFile** můžete nasadit databázi U-SQL do účtu Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>Popis y parametrů PackageDeploymentTool.exe

#### <a name="common-parameters"></a>Běžné parametry

| Parametr | Popis | Výchozí hodnota | Požaduje se |
|---------|-----------|-------------|--------|
|Balíček|Cesta balíčku nasazení databáze U-SQL, který má být nasazen.|null|true|
|Databáze|Název databáze, který má být nasazen nebo vytvořen.|master|false (nepravda)|
|Logfile|Cesta k souboru pro protokolování. Výchozí standard out (konzola).|null|false (nepravda)|
|LogLevel|Úroveň protokolu: Podrobné, Normální, Upozornění nebo Chyba.|LogLevel.Normal|false (nepravda)|

#### <a name="parameter-for-local-deployment"></a>Parametr pro místní nasazení

|Parametr|Popis|Výchozí hodnota|Požaduje se|
|---------|-----------|-------------|--------|
|Kořenová_složka_dat|Cesta kořenové složky místních dat.|null|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parametry pro nasazení Azure Data Lake Analytics

|Parametr|Popis|Výchozí hodnota|Požaduje se|
|---------|-----------|-------------|--------|
|Účet|Určuje, do kterého účtu Azure Data Lake Analytics se má nasadit podle názvu účtu.|null|true|
|ResourceGroup|Název skupiny prostředků Azure pro účet Azure Data Lake Analytics.|null|true|
|SubscriptionId|ID předplatného Azure pro účet Azure Data Lake Analytics.|null|true|
|Tenant|Název klienta je název domény Azure Active Directory (Azure AD). Najdete ji na stránce správy předplatného na webu Azure Portal.|null|true|
|AzureSDKPath|Cesta k hledání závislých sestavení v azure sdk.|null|true|
|Interaktivní|Určuje, zda má být k ověřování používán interaktivní režim.|false (nepravda)|false (nepravda)|
|ClientId|ID aplikace Azure AD požadované pro neinteraktivní ověřování.|null|Vyžadováno pro neinteraktivní ověřování.|
|Vylučují|Vykreslovač nebo heslo pro neinteraktivní ověřování. Měl by být používán pouze v důvěryhodném a zabezpečeném prostředí.|null|Vyžadováno pro neinteraktivní ověřování, jinak použijte SecreteFile.|
|Soubor Vykreslován|Soubor uloží vykreslovače nebo heslo pro neinteraktivní ověřování. Ujistěte se, že je čitelný pouze aktuálním uživatelem.|null|Vyžadováno pro neinteraktivní ověřování, jinak použijte Vykreslovače.|
|Soubor CertFile|Soubor ukládá certifikaci X.509 pro neinteraktivní ověřování. Ve výchozím nastavení je použití ověřování vykreslovat klienta.|null|false (nepravda)|
| JobPrefix | Předpona pro nasazení databáze úlohy U-SQL DDL. | Deploy_ + DateTime.Now | false (nepravda) |

## <a name="next-steps"></a>Další kroky

- [Jak otestovat kód Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
- [Spusťte skript U-SQL v místním počítači](data-lake-analytics-data-lake-tools-local-run.md).
- [Pomocí databázového projektu U-SQL můžete vyvinout databázi U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).
