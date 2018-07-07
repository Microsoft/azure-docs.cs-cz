---
title: Jak vytvořit kanál CI/CD pro Azure Data Lake Analytics | Dokumentace Microsoftu
description: Zjistěte, jak nastavit průběžnou integraci a průběžné nasazování pro Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a55c8fd95409de4fb1ea725d234de907f79d3c7b
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889517"
---
# <a name="how-to-set-up-cicd-pipeline-for-azure-data-lake-analytics"></a>Jak vytvořit kanál CI/CD pro Azure Data Lake Analytics

V tomto dokumentu zjistíte, jak vytvořit kanál CI/CD pro úlohy U-SQL a databáze U-SQL.

## <a name="cicd-for-u-sql-job"></a>CI/CD pro úlohy U-SQL

Azure Data Lake Tools pro Visual Studio poskytuje typ projektu U-SQL, který vám pomůže orgnize U-SQL skriptů. Další scénáře CI/CD pomocí projekt U-SQL ke správě kódu U-SQL umožňuje snadno.

## <a name="build-u-sql-project"></a>Sestavit projekt v U-SQL

Projekt U-SQL lze sestavit pomocí nástroje MSBuild předáním odpovídající parametry. Podle následujících pokynů k nastavení procesu sestavení pro projekty v U-SQL.

### <a name="project-migration"></a>Migrace projektu

Před nastavením úloha sestavení pro projekt v U-SQL, ujistěte se, že používáte nejnovější verzi projekt v U-SQL. V editoru otevřete soubor projektu U-SQL a zkontrolujte, jestli máte následující položky importu:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Pokud ne, máte dvě možnosti, jak migrovat projekt:

- Možnost 1: Změna starého importovat položku k výše uvedené.
- Možnost 2: Starý projekt otevřete v Azure Data Lake Tools pro Visual Studio po verzi 2.3.3000.0. Starou šablonu projektu budou automaticky upgradovat na nejnovější verzi. Nový projekt vytvořený po verzi 2.3.3000.0 používá novou šablonu přímo.

### <a name="get-nuget-package"></a>Získat balíček NuGet

Nástroj MSBuild neposkytuje integrovanou podporu pro typ projektu U-SQL. Chcete-li přidat tuto možnost, budete muset přidat odkaz pro vaše řešení [balíček Microsoft.Azure.DataLake.USQL.SDK Nuget](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) , který přidá službu požadovaný jazyk.

Chcete-li přidat odkaz na balíček NuGet, klikněte pravým tlačítkem na řešení v Průzkumníku řešení a zvolte **spravovat balíčky NuGet**. Nebo můžete přidat soubor s názvem `packages.config` ve složce řešení a přidejte do něj následující obsah.

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Spravovat odkazy na databázi U-SQL

Pokud dotaz příkazy pro objekty databáze U-SQL skriptů U-SQL v projektu U-SQL, například dotaz na tabulku U-SQL nebo odkazovat na sestavení, budete muset odkaz odpovídající projekt databáze U-SQL, který obsahuje definici tyto objekty před sestavování tohoto projektu U-SQL.

[Další informace o projektu databáze U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

### <a name="build-u-sql-project-with-msbuild-command-line"></a>Sestavit projekt v U-SQL pomocí příkazového řádku MSBuild

Po migraci projekt a získávání balíčku NuGet, můžete volat standardní příkazového řádku MSBuild s dalšími argumenty níže a sestavte projekt U-SQL:

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder
``` 

Definice argumentů a hodnoty jsou:

* USQLSDKPath = < balíček Nuget U-SQL > \build\runtime: Tento parametr odkazuje na cestu instalace balíčku NuGet pro služby jazyka U-SQL.
* USQLTargetType = sloučení nebo SyntaxCheck:
    * Sloučení: Režim sloučení zkompiluje soubory kódu na pozadí, jako jsou .cs, soubory .py a .r a inlines výslednou knihovnu uživatelský kód (například binární soubor knihovny dll, Python nebo R kód) do skriptu U-SQL.
    * SyntaxCheck: Režim SyntaxCheck nejprve sloučí soubory kódu na pozadí do skriptu U-SQL a pak zkompiluje skript U-SQL k ověření kódu.
* DataRoot =<DataRoot path>: DataRoot je potřeba jenom pro SyntaxCheck režimu. Při vytváření skriptu s režimem SyntaxCheck, zkontroluje MSBuild odkazy ve skriptu na databázové objekty. Ujistěte se, že chcete nastavit odpovídající místní prostředí, která obsahuje odkazované objekty z databáze U-SQL na počítači sestavení ke složce DataRoot před sestavením. Můžete také spravovat tyto databáze závislostí podle [odkazování na projekt U-SQL database](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). Mějte na paměti, že nástroj MSBuild zkontroluje pouze odkaz na databázi objekty, nikoli soubory.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Průběžná integrace se sadou Visual Studio Team Service

Kromě příkazového řádku zákazníkům také úkol můžete použít Visual Studio Build nebo MSBuild k sestavení projektů U-SQL ve službě Visual Studio Team. Pokud chcete nastavit úlohu sestavení, ujistěte se, že:

1.  Přidat úlohu obnovení NuGet pro získání řešení odkazuje, včetně balíčku NuGet `Azure.DataLake.USQL.SDK`tak, aby MSBuild najdete cíle jazyk U-SQL. 

    ![Data Lake nastavit CI CD MSBuild – úloha pro projekt v U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

2.  Argumenty nástroje MSBuild sady kde lze nastavit argumenty sestavování služby Visual Studio nebo nástroje MSBuild úkolu jako níže, nebo můžete definovat proměnné pro tyto argumenty v definici sestavení VSTS.

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/<your project name>/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.1019-preview/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory)
    ```

    ![Data Lake nastavit CI CD MSBuild proměnné pro projekt v U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

### <a name="u-sql-project-build-output"></a>Výstupní sestavení projektu U-SQL

Po spuštění sestavení, všechny skripty v projektu U-SQL Sestavujeme a výstupem do souboru zip s názvem `USQLProjectName.usqlpack`. Ve výstupu ZIP sestavení se zachová strukturu složek ve vašem projektu.

>[!NOTE]
>
>Jako příkaz vložený do skriptu sestavení výstupu se sloučí soubor pro každý skript U-SQL s kódem.
>

## <a name="test-u-sql-script"></a>Testování skriptů U-SQL

Azure Data Lake nabízí pro skript U-SQL a C# UDO/UDAG/UDF projekty testů:
* [Informace o postupu přidání testovacích případů pro skript U-SQL a rozšířené kód jazyka C#](data-lake-analytics-cicd-test.md#test-u-sql-scripts)
* [Zjistěte, jak spustit tyto testovací případy v aplikaci Visual Studio Team Service](data-lake-analytics-cicd-test.md#run-test-cases-in-visual-studio-team-service)

## <a name="u-sql-job-deployment"></a>Nasazení úlohy U-SQL

Po ověření kódu pomocí sestavení a testování procesu, můžete odeslat úloh U-SQL přímo z Visual Studio Team Service prostřednictvím **úloh prostředí Azure PowerShell**. Skript můžete také nasadit do Azure Data Lake Store nebo Azure Blob Storage a [spouštět plánované úlohy prostřednictvím Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-visual-studio-team-service"></a>Odesílání úloh U-SQL pomocí Visual Studio Team Service

Výstupní sestavení projektu U-SQL je soubor zip se nazývá **USQLProjectName.usqlpack** zahrnuje všechny skripty U-SQL v projektu. Můžete použít [Azure PowserShell úkolů ve službě Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) s níže uvedený ukázkový skript PowserShell odeslat U-SQL úlohy přímo z Visual Studio Team Service sestavení nebo kanál verze.

```powershell
param(
    [Parameter(Mandatory=$true)][string]$AnalyticsAccountName, #ADLA account name
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, #Root folder (e.g. artifacts root folder)
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

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
        # [System.IO.Compression.ZipFile]::ExtractToDirectory($USQLPackfile, $UnzipOutput, 0)
        # $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
        # Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue | Where-Object {$_.DirectoryName -match $subFolder}

    return $USQLFiles
}

Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."
    # submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "($usqlFiles.IndexOf())Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzureRmDataLakeAnalyticsJob -Account $AnalyticsAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        LogJobInformation $jobToSubmit
        
        Write-Output "waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzureRmDataLakeAnalyticsJob -Account $AnalyticsAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
        
        # ProcessResult $jobResult
    }
}

Function ProcessResult($jobResult)
{
    if ($jobResult.Result -eq "Failed")
    {
        Write-Error "Job Failed. Job Id: $($jobResult.JobId), Job Name: $($jobResult.Name), Log: $($jobResult.LogFolder)"
    }
    else
    {
        Write-Output "Job Succeeded. Job Id: $($jobResult.JobId), Job Name: $($jobResult.Name)"
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

    Write-Output "Starting USQL script deployment..."

    # Submit ADLA jobs with usql scripts in given sub-folder.
    # Order is important here. Scripts with least dependency goes first followed 
    # by scripts which more dependencies.
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Nasazení úloh U-SQL pomocí Azure Data Factory

Kromě odesílání úloh U-SQL přímo z Visual Studio Team Service, můžete také nahrát připravených skriptů do služby Azure Data Lake Store nebo Azure Blob Storage a [spouštět plánované úlohy prostřednictvím Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Použití [úloh prostředí Azure PowerShell ve službě Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) s následující ukázkový skript Powershellu pro odeslání U-SQL skriptů k účtu Azure Data Lake Store.

```powershell
param(
    [Parameter(Mandatory=$true)][string]$ADLSName, #ADLA account name
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot #Root folder (e.g. artifacts root folder)
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading DLL files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles
    Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $usqlScripts.FullName -Destination "/ScriptResource2/$usqlScripts" -Force -Recurse
    # $files = @(get-childitem $usqlScripts -recurse)
    # foreach($file in $files)
    # {
    #    Write-Host "Uploading file: $($file.Name)"
    #    Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/ScriptResource/$file" -Force -Recurse
    # }
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

    return $UnzipOutput
}

UploadResources
```

## <a name="cicd-for-u-sql-database"></a>CI/CD pro databázi U-SQL

Azure Data Lake Tools pro Visual Studio poskytuje šablony projektu U-SQL database pomáhá vývojářům vývoj, Správa a nasazení databáze U-SQL, rychle a snadno. [Další informace o databázový projekt U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Sestavit projekt U-SQL database

### <a name="get-nuget-package"></a>Získat balíček NuGet

Nástroj MSBuild neposkytuje integrovanou podporu pro typ projektu U-SQL database. Chcete-li přidat tuto možnost, budete muset přidat odkaz pro vaše řešení [balíček Microsoft.Azure.DataLake.USQL.SDK Nuget](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) , který přidá službu požadovaný jazyk.

Chcete-li přidat odkaz na balíček NuGet, můžete klikněte pravým tlačítkem na řešení v Průzkumníku řešení a zvolte **spravovat balíčky NuGet** řešení, vyhledejte a nainstalujte balíček NuGet. Nebo můžete přidat soubor s názvem "souboru packages.config" ve složce řešení a přidejte do něj následující obsah.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-database-project-with-msbuild-command-line"></a>Projekt databáze U-SQL pomocí příkazového řádku MSBuild sestavit

Můžete volat standardní příkazového řádku MSBuild a předejte odkaz na balíček NuGet sady SDK U-SQL jako další argument podobná níže uvedenému příkladu k sestavení projektu databáze U-SQL:

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Argumenty `USQLSDKPath=<U-SQL Nuget package>\build\runtime` odkazuje na cestu instalace balíčku NuGet pro služby jazyka U-SQL.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Průběžná integrace se sadou Visual Studio Team Service

Kromě příkazového řádku, můžou zákazníci taky využít **Visual Studio Build** nebo **úlohy nástroje MSBuild** k sestavení projektů databáze U-SQL ve službě Visual Studio Team Service. Pokud chcete nastavit úlohu sestavení, ujistěte se, že:

1.  Přidat úlohu obnovení NuGet pro získání řešení odkazuje, včetně balíčku NuGet `Azure.DataLake.USQL.SDK`tak, aby MSBuild najdete cíle jazyk U-SQL. 

    ![Data Lake sady CI CD MSBuild – úloha pro databázový projekt U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

2.  Argumenty nástroje MSBuild sady kde lze nastavit argumenty sestavování služby Visual Studio nebo nástroje MSBuild úkolu jako níže, nebo můžete definovat proměnné pro tyto argumenty v definici sestavení VSTS.

```
/p:USQLSDKPath=$(Build.SourcesDirectory)/<your project name>/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.1019-preview/build/runtime
```

![Data Lake nastavení proměnných CI CD MSBuild pro projekt databáze U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

### <a name="u-sql-database-project-build-output"></a>Výstupní sestavení projektu databáze U-SQL

Výstup pro databázový projekt U-SQL je nasazení balíček U-SQL database, s příponou s názvem sestavení `.usqldbpack`. `.usqldbpack` Balíček je zazipovaný soubor obsahuje všechny příkazy DDL v jeden skript U-SQL ve složce DDL a všechny knihovny DLL debuggle a další soubory pro sestavení ve složce Temp.

## <a name="test-table-valued-function-and-stored-procedure"></a>Testování funkce vracející tabulku a uložené procedury

V tomto okamžiku není podporováno přidávání testovacích případů pro funkce hodnot tabulky a uložené procedury přímo. Jako alternativní řešení můžete vytvořit projekt U-SQL, který má volání těchto funkcí skriptů U-SQL a zápis testovacích případů pro ně. Postupujte podle následujících kroků nastavte testovací případy pro funkce hodnot tabulky a uložené procedury, které jsou definované v projektu databáze U-SQL:

1.  Vytvořte projekt U-SQL pro testovací účely a psaní skriptů U-SQL, volání funkce hodnot tabulky a uložené procedury.
2.  Přidáte odkaz na databázi pro tento projekt v U-SQL. Aby bylo možné získat funkce vracející tabulku a definice uložené procedury, budete muset odkaz databázový projekt, který obsahuje příkaz DDL. [Další informace o odkaz na databázi](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Přidáte testovací případy pro skripty U-SQL, které volají funkce hodnot tabulky a uložené procedury. [Informace o postupu přidání testovacích případů pro skript U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-visual-studio-team-service"></a>Nasazení databáze U-SQL pomocí Visual Studio Team Service

`PackageDeploymentTool.exe` poskytuje programování a rozhraní příkazového řádku, které pomáhají k nasazení package(.usqldbpack) nasazení databáze U-SQL. Je součástí sady SDK [balíček NuGet sady SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), vyhledáním v build/runtime/PackageDeploymentTool.exe. S použitím `PackageDeploymentTool.exe`, databáze U-SQL můžete nasadit do Azure Data Lake Analytics a místní účet.

>[!NOTE]
>
>Příkaz prostředí PowerShell, podporu řádku a Visual Studio Team Service release podpora úkolů pro nasazení databáze U-SQL je na cestě.
>

Pomocí následujícího postupu nastavení úloh nasazení databáze ve službě Visual Studio Team Service:

1. Přidat Powershellový skript úkolu v sestavení nebo kanál verze a spusťte níže uvedený skript prostředí PowerShell. Umožňuje získat závislosti sady Azure SDK pro tuto úlohu `PackageDeploymentTool.exe`. Můžete nastavit parametr - outputfolder k načtení těchto závislostí na některé konkrétní složky. Předat tuto cestu ke složce `PackageDeploymentTool.exe` v kroku 2. 

    ```powershell
    param (
        [string]$outputfolder = "RequiredDll",
        [string]$workingfolder = ""
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.2.3-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.3.3-preview -outf Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.3.3-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force

    echo "Copy required DLLs to output folder..."

    mkdir $outputfolder -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview\lib\net452\*.dll $outputfolder
    copy Microsoft.Azure.Management.DataLake.Store.2.3.3-preview\lib\net452\*.dll $outputfolder
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $outputfolder
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $outputfolder
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $outputfolder
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $outputfolder
    ```

2. Přidat **příkazového řádku úkolu** v sestavení nebo vydané verze kanálu a vyplňte volání skriptu `PackageDeploymentTool.exe`. Ukázkový skript vypadá takto: 

    * Nasazení databáze U-SQL místně

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Nasazení databáze U-SQL do účtu Azure Data Lake Analytics pomocí ověřování interaktivní režim:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Použití secrete ověřování nasazení databáze U-SQL do účtu Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Nasazení databáze U-SQL do účtu Azure Data Lake Analytics pomocí Soubor_certifikátu ověřování:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

**Popis parametru PackageDeploymentTool.exe:**

**Společné parametry:**

|Parametr|Popis|Výchozí hodnota|Požaduje se|
|---------|-----------|-------------|--------|
|Balíček|Cesta k balíčku pro nasazení databáze U-SQL k nasazení|Hodnotu Null|true (pravda)|
|Databáze|Název nasazení databáze / nebo vytvořit|master|false (nepravda)|
|Soubor protokolu|Cesta k souboru pro protokolování, výchozí na standard si (konzola)|Hodnotu Null|false (nepravda)|
|LogLevel|Úrovně protokolování: podrobné, Normální, upozornění, chyby|LogLevel.Normal|false (nepravda)|

**{{Parametr pro místní nasazení:**

|Parametr|Popis|Výchozí hodnota|Požaduje se|
|---------|-----------|-------------|--------|
|Kořenová datová složka|Cesta k kořenové složce místní Data|Hodnotu Null|true (pravda)|

**{{Parametr pro nasazení Azure Data Lake Analytics:**

|Parametr|Popis|Výchozí hodnota|Požaduje se|
|---------|-----------|-------------|--------|
|Účet|Určuje nasazení do které název účtu pomocí účtu Azure Data Lake Analytics|Hodnotu Null|true (pravda)|
|ResourceGroup|Název skupiny prostředků Azure pro účet Azure Data Lake Analytics|Hodnotu Null|true (pravda)|
|SubscriptionId|ID předplatného Azure pro účet Azure Data Lake Analytics|Hodnotu Null|true (pravda)|
|Klient|Název tenanta (název domény adresáře AAD, najdete ho na stránce Správa předplatného na webu Azure portal)|Hodnotu Null|true (pravda)|
|AzureSDKPath|Cesty hledání závislých sestavení v sadě Azure SDK|Hodnotu Null|true (pravda)|
|Interaktivní|Ale nemusíte používat interaktivní režim ověřování|false (nepravda)|false (nepravda)|
|ID klienta|ID aplikace AAD pro žádný interaktivní ověřování, vyžaduje se pro žádný interaktivní ověřování|Hodnotu Null|vyžaduje se pro žádný interaktivní ověřování|
|Secrete|Secrete a hesla pro žádný interaktivní ověřování, ji měli používat jenom v důvěryhodné a zabezpečené prostředí|Hodnotu Null|vyžaduje se pro žádný interaktivní ověřování, nebo použijte SecreteFile|
|SecreteFile|Soubor uloží secrete/heslo pro žádný interaktivní ověřování, ujistěte se, aby byl pouze ke čtení. aktuální uživatel|Hodnotu Null|vyžaduje se pro žádný interaktivní ověřování, nebo použijte Secrete|
|Soubor_certifikátu|Soubor uloží certifikace X.509 pro žádný interaktivní ověřování, se standardně používá klienta secrete ověřování|Hodnotu Null|false (nepravda)|
|JobPrefix|Předpona pro nasazení databáze U-SQL DDL úlohy|Deploy_ + DateTime.Now|false (nepravda)|

## <a name="next-steps"></a>Další kroky

- [Tom, jak testovat kód Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Spusťte skript U-SQL na místním počítači](data-lake-analytics-data-lake-tools-local-run.md)
- [Vývoj databáze U-SQL pomocí databázový projekt U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)