---
title: Jak nastavit kanál CI/CD pro Azure Data Lake Analytics
description: Přečtěte si, jak nastavit průběžnou integraci a průběžné nasazování pro Azure Data Lake Analytics.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 09/14/2018
ms.openlocfilehash: 95b638b85e0746d2995488f2a28a5fb2512b1063
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015260"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Jak nastavit kanál CI/CD pro Azure Data Lake Analytics  

V tomto článku se dozvíte, jak nastavit kanál průběžné integrace a nasazování (CI/CD) pro úlohy U-SQL a databáze U-SQL.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>Použití CI/CD pro úlohy U-SQL

Nástroje Azure Data Lake pro Visual Studio poskytuje typ projektu U-SQL, který vám pomůže organizovat skripty U-SQL. Použití projektu U-SQL ke správě kódu U-SQL usnadňuje další scénáře CI/CD.

## <a name="build-a-u-sql-project"></a>Sestavení projektu U-SQL

Projekt U-SQL lze vytvořit pomocí Microsoft Build Engine (MSBuild) předáním odpovídajících parametrů. Postupujte podle kroků v tomto článku a nastavte proces sestavení pro projekt U-SQL.

### <a name="project-migration"></a>Migrace projektu

Než nastavíte úlohu sestavení pro projekt U-SQL, ujistěte se, že máte nejnovější verzi projektu U-SQL. V editoru otevřete soubor projektu U-SQL a ověřte, že máte tyto položky importu:

```xml
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
```

Pokud ne, máte k dispozici dvě možnosti migrace projektu:

- Možnost 1: Změňte starou položku importu na předchozí.
- Možnost 2: Otevřete původní projekt v Nástroje Azure Data Lake pro Visual Studio. Použijte verzi novější než 2.3.3000.0. Stará šablona projektu bude automaticky upgradována na nejnovější verzi. Nové projekty vytvořené s novější verzí než 2.3.3000.0 používají novou šablonu.

### <a name="get-nuget"></a>Získat NuGet

MSBuild neposkytuje integrovanou podporu pro projekty U-SQL. Chcete-li získat tuto podporu, musíte přidat odkaz na vaše řešení do balíčku NuGet [Microsoft. Azure. datalake. USQL. SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) , který přidá požadovanou jazykovou službu.

Chcete-li přidat odkaz na balíček NuGet, klikněte pravým tlačítkem na řešení v aplikaci Visual Studio Průzkumník řešení a vyberte možnost **Spravovat balíčky NuGet**. Případně můžete do složky řešení přidat soubor s názvem `packages.config` a vložit do něj následující obsah:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
```

### <a name="manage-u-sql-database-references"></a>Spravovat odkazy na databázi U-SQL

Skripty u-SQL v projektu U-SQL mohou mít příkazy dotazu pro objekty databáze U-SQL. V takovém případě je nutné před vytvořením projektu U-SQL odkazovat na odpovídající projekt databáze U-SQL, který obsahuje definici Objects. Příkladem je dotaz na tabulku U-SQL nebo odkaz na sestavení.

Další informace o [projektu U-SQL Database](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
> Příkaz DROP může způsobit nechtěné odstranění. Chcete-li povolit příkaz DROP, je nutné explicitně zadat argumenty nástroje MSBuild. **AllowDropStatement** povolí operaci přetažení, která nesouvisí s daty, třeba drop Assembly a drop Table vracející funkci. **AllowDataDropStatement** povolí operaci odkládacích dat, jako je například vyřazení tabulky a řazení. Před použitím AllowDataDropStatement musíte povolit AllowDropStatement.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Sestavení projektu U-SQL pomocí příkazového řádku MSBuild

Nejprve migrujte projekt a získejte balíček NuGet. Pak zavolejte standardní příkazový řádek nástroje MSBuild s následujícími dalšími argumenty pro sestavení projektu U-SQL:

```console
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
```

Definice argumentů a jejich hodnoty jsou následující:

- **USQLSDKPath = \<U-SQL Nuget package> \build\runtime**. Tento parametr odkazuje na instalační cestu balíčku NuGet pro službu jazyka U-SQL.
- **USQLTargetType = Merge nebo SyntaxCheck**:

  - **Sloučit**. Režim sloučení zkompiluje soubory kódu na pozadí. Příklady jsou soubory **cs**, **. py** a **. r** . Zaznamená výsledné uživatelsky definované knihovny kódu do skriptu U-SQL. Příkladem může být binární soubor DLL, Python nebo kód R.

  - **SyntaxCheck**. Režim SyntaxCheck nejprve sloučí soubory kódu na pozadí do skriptu U-SQL. Potom zkompiluje skript U-SQL, který ověří váš kód.

- **Dataroot = \<DataRoot path>**. Dataroot je potřeba jenom pro režim SyntaxCheck. Při sestavení skriptu pomocí režimu SyntaxCheck zkontroluje nástroj MSBuild odkazy na databázové objekty ve skriptu. Před sestavením nastavte odpovídající místní prostředí, které obsahuje odkazované objekty z databáze U-SQL ve složce dataroot počítače sestavení. Tyto závislosti databáze můžete spravovat také [odkazem na projekt databáze U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). Nástroj MSBuild kontroluje pouze odkazy na objekty databáze, nikoli soubory.

- **EnableDeployment = true** nebo **false**. EnableDeployment označuje, zda je povoleno nasadit v průběhu procesu sestavení odkazované databáze U-SQL. Pokud odkazujete na projekt databáze U-SQL a spotřebujete databázové objekty ve skriptu U-SQL, nastavte tento parametr na **hodnotu true**.

### <a name="continuous-integration-through-azure-pipelines"></a>Průběžná integrace prostřednictvím Azure Pipelines

Kromě příkazového řádku můžete také použít úlohu sestavení sady Visual Studio nebo MSBuild k sestavení projektů U-SQL v Azure Pipelines. Chcete-li nastavit kanál sestavení, nezapomeňte do kanálu sestavení přidat dvě úlohy: úlohu obnovení NuGet a úlohu MSBuild.

![Úloha MSBuild pro projekt U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Přidejte úlohu obnovení NuGet pro získání balíčku NuGet odkazovaného řešení, který obsahuje `Azure.DataLake.USQL.SDK` , aby nástroj MSBuild mohl najít cíle jazyka U-SQL. Nastavte **pokročilý**  >  **cílový adresář** na, `$(Build.SourcesDirectory)/packages` Pokud chcete použít ukázku argumentů MSBuild přímo v kroku 2.

   ![Úloha obnovení NuGet pro projekt U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Nastavte argumenty MSBuild v nástrojích pro sestavení sady Visual Studio nebo v úloze MSBuild, jak je znázorněno v následujícím příkladu. Případně můžete definovat proměnné pro tyto argumenty v kanálu sestavení Azure Pipelines.

   ![Definování proměnných CI/CD MSBuild pro projekt U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
   ```

### <a name="u-sql-project-build-output"></a>Výstup sestavení projektu U-SQL

Po spuštění sestavení jsou všechny skripty v projektu U-SQL sestaveny a výstup do souboru zip s názvem `USQLProjectName.usqlpack` . Struktura složek v projektu je udržována ve výstupu metody zip Build.

> [!NOTE]
> Soubory kódu na pozadí každého skriptu U-SQL budou sloučeny jako vložený příkaz do výstupu sestavení skriptu.

## <a name="test-u-sql-scripts"></a>Testování skriptů U-SQL

Azure Data Lake poskytuje testovací projekty pro skripty U-SQL a C# UDO/UDAG/UDF:

- Přečtěte si, jak [Přidat testovací případy pro skripty U-SQL a Rozšířený kód jazyka C#](data-lake-analytics-cicd-test.md#test-u-sql-scripts) .

- Naučte se [spouštět testovací případy v Azure Pipelines](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Nasazení úlohy U-SQL

Po ověření kódu prostřednictvím procesu sestavení a testování můžete odesílat úlohy U-SQL přímo z Azure Pipelines prostřednictvím úlohy Azure PowerShell. Tento skript můžete také nasadit do Azure Data Lake Store nebo úložiště objektů BLOB v Azure a [spouštět naplánované úlohy prostřednictvím Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md).

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Odesílat úlohy U-SQL prostřednictvím Azure Pipelines

Výstup sestavení projektu U-SQL je soubor zip s názvem **USQLProjectName. usqlpack**. Soubor zip obsahuje všechny skripty U-SQL v projektu. Můžete použít [úlohu Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell) v kanálech s následujícím ukázkovým skriptem PowerShellu pro odesílání úloh U-SQL přímo z Azure Pipelines.

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

>[!NOTE]
> Příkazy: `Submit-AzDataLakeAnalyticsJob` a `Wait-AzDataLakeAnalyticsJob` jsou Azure PowerShell rutiny pro Azure Data Lake Analytics v rozhraní Azure Resource Manager Framework. Muset pracovní stanici s nainstalovanou Azure PowerShell. Další příkazy a příklady najdete v [seznamu příkazů](/powershell/module/Az.DataLakeAnalytics) .
>

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Nasazení úloh U-SQL prostřednictvím Azure Data Factory

Úlohy U-SQL můžete odesílat přímo z Azure Pipelines. Můžete také nahrát sestavené skripty do Azure Data Lake Store nebo úložiště objektů BLOB v Azure a [spouštět naplánované úlohy prostřednictvím Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md).

Pomocí [úlohy Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell) v Azure Pipelines pomocí následujícího ukázkového skriptu PowerShellu nahrajte skripty U-SQL do účtu Azure Data Lake Store:

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

Nástroje Azure Data Lake pro Visual Studio poskytuje šablony projektů databáze U-SQL, které vám pomůžou vyvíjet, spravovat a nasazovat databáze U-SQL. Přečtěte si další informace o [projektu U databáze U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Sestavení projektu U-SQL Database

### <a name="get-the-nuget-package"></a>Získání balíčku NuGet

MSBuild neposkytuje integrovanou podporu pro projekty databáze U-SQL. Chcete-li získat tuto možnost, musíte přidat odkaz na vaše řešení do balíčku NuGet [Microsoft. Azure. datalake. USQL. SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) , který přidá požadovanou jazykovou službu.

Odkaz na balíček NuGet přidáte tak, že kliknete pravým tlačítkem na řešení v aplikaci Visual Studio Průzkumník řešení. Vyberte možnost **Spravovat balíčky NuGet**. Pak vyhledejte a nainstalujte balíček NuGet. Případně můžete do složky řešení přidat soubor s názvem **packages.config** a vložit do něj následující obsah:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Sestavení databázového projektu U-SQL pomocí příkazového řádku MSBuild

Chcete-li vytvořit projekt databáze U-SQL, zavolejte standardní příkazový řádek nástroje MSBuild a předejte odkaz na balíček NuGet U-SQL SDK jako další argument. Prohlédněte si následující příklad: 

```console
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Argument `USQLSDKPath=<U-SQL Nuget package>\build\runtime` odkazuje na instalační cestu balíčku NuGet pro službu jazyka U-SQL.

### <a name="continuous-integration-with-azure-pipelines"></a>Průběžná integrace s využitím Azure Pipelines

Kromě příkazového řádku můžete pomocí sestavení sady Visual Studio nebo úlohy MSBuild vytvořit projekty databáze U-SQL v Azure Pipelines. Chcete-li nastavit úlohu sestavení, nezapomeňte do kanálu sestavení přidat dvě úlohy: úlohu obnovení NuGet a úlohu MSBuild.

   ![Úloha CI/CD MSBuild pro projekt U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Přidáním úlohy obnovení NuGet získáte balíček NuGet odkazovaný řešením, který obsahuje `Azure.DataLake.USQL.SDK` , aby nástroj MSBuild mohl najít cíle jazyka U-SQL. Nastavte **pokročilý**  >  **cílový adresář** na, `$(Build.SourcesDirectory)/packages` Pokud chcete použít ukázku argumentů MSBuild přímo v kroku 2.

   ![Úloha NuGet pro CI/CD pro projekt U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Nastavte argumenty MSBuild v nástrojích pro sestavení sady Visual Studio nebo v úloze MSBuild, jak je znázorněno v následujícím příkladu. Případně můžete definovat proměnné pro tyto argumenty v kanálu sestavení Azure Pipelines.

   ![Definování proměnných CI/CD MSBuild pro projekt databáze U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```

### <a name="u-sql-database-project-build-output"></a>Výstup sestavení projektu U-SQL Database

Výstup sestavení pro projekt databáze U-SQL je balíček pro nasazení U-SQL Database s názvem s příponou `.usqldbpack` . `.usqldbpack`Balíček je soubor zip, který obsahuje všechny příkazy DDL v jednom skriptu U-SQL ve složce DDL. Zahrnuje všechny **knihovny DLL** a další soubory pro sestavení v dočasné složce.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Testování funkcí s hodnotou tabulky a uložených procedur

Přidání testovacích případů pro funkce vracející tabulku a přímo uložených procedur není aktuálně podporováno. Jako alternativní řešení můžete vytvořit projekt U-SQL, který má skripty U-SQL, které tyto funkce volají, a zapsat testovací případy pro ně. Proveďte následující kroky a nastavte testovací případy pro funkce vracející tabulku a uložené procedury definované v projektu databáze U-SQL:

1. Vytvořte projekt U-SQL pro účely testování a zapište skripty U-SQL, které volají funkce vracející tabulku a uložené procedury.

1. Přidejte odkaz na databázi do projektu U-SQL. Chcete-li získat definici funkce vracející tabulku a uloženou proceduru, musíte odkazovat na projekt databáze, který obsahuje příkaz DDL. Přečtěte si další informace o [databázových odkazech](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

1. Přidejte testovací případy pro skripty U-SQL, které volají funkce vracející tabulku a uložené procedury. Naučte se, jak [Přidat testovací případy pro skripty u-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Nasazení databáze U-SQL prostřednictvím Azure Pipelines

`PackageDeploymentTool.exe` poskytuje rozhraní pro programování a příkazový řádek, které vám pomůžou nasadit balíčky pro nasazení databáze U-SQL, **. usqldbpack**. Sada SDK je součástí [balíčku NuGet U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), který je umístěný v **sestavení/za běhu/PackageDeploymentTool.exe**. Pomocí nástroje `PackageDeploymentTool.exe` můžete nasadit databáze U-SQL do obou Azure Data Lake Analytics i místních účtů.

> [!NOTE]
>
> Podpora příkazového řádku PowerShellu a podpora Azure Pipelines úlohy vydání pro nasazení databáze U-SQL momentálně čeká na vyřízení.
>

Při nastavování úlohy nasazení databáze v Azure Pipelines proveďte následující kroky:

1. Přidejte úlohu skriptu PowerShellu do kanálu sestavení nebo vydání a spusťte následující skript PowerShellu. Tato úloha pomáhá získat závislosti sady Azure SDK pro `PackageDeploymentTool.exe` a `PackageDeploymentTool.exe` . Parametry **-AzureSDK** a **-DBDeploymentTool** můžete nastavit tak, aby se načetly závislosti a nástroj pro nasazení na konkrétní složky. Předat cestu **AzureSDK** `PackageDeploymentTool.exe` jako parametr **-AzureSDKPath** v kroku 2.

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

2. Přidejte **úlohu příkazového řádku** do kanálu sestavení nebo vydání a zadejte do skriptu volání `PackageDeploymentTool.exe` . `PackageDeploymentTool.exe` je umístěn v rámci definované složky **$DBDeploymentTool** . Vzorový skript je následující: 

   - Místní nasazení U-SQL Database:

      ```cmd
      PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
      ```

   - K nasazení databáze U-SQL do účtu Azure Data Lake Analytics použijte interaktivní režim ověřování:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
      ```

   - Pomocí **tajného** ověřování nasaďte databázi u-SQL do účtu Azure Data Lake Analytics:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
      ```

   - Pomocí ověřování typu **Soubor_certifikátu** nasaďte databázi u-SQL do účtu Azure Data Lake Analytics:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
      ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>PackageDeploymentTool.exe popisy parametrů

#### <a name="common-parameters"></a>Společné parametry

| Parametr | Popis | Výchozí hodnota | Vyžadováno |
|---------|-----------|-------------|--------|
|Balíček|Cesta k balíčku pro nasazení U-SQL Database, která se má nasadit|null|true|
|databáze|Název databáze, která má být nasazena nebo vytvořena.|master|false (nepravda)|
|Protokolů|Cesta k souboru pro protokolování. Výchozí na standardní (konzola)|null|false (nepravda)|
|LogLevel|Úroveň protokolu: Verbose, normální, varování nebo chyba.|LogLevel. Normal|false (nepravda)|

#### <a name="parameter-for-local-deployment"></a>Parametr pro místní nasazení

|Parametr|Popis|Výchozí hodnota|Vyžadováno|
|---------|-----------|-------------|--------|
|DataRoot|Cesta ke kořenové složce místních dat|null|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parametry pro nasazení Azure Data Lake Analytics

|Parametr|Popis|Výchozí hodnota|Vyžadováno|
|---------|-----------|-------------|--------|
|Účet|Určuje, který účet Azure Data Lake Analytics se má nasadit podle názvu účtu.|null|true|
|ResourceGroup|Název skupiny prostředků Azure pro účet Azure Data Lake Analytics.|null|true|
|SubscriptionId|ID předplatného Azure pro účet Azure Data Lake Analytics.|null|true|
|Tenant|Název tenanta je název domény Azure Active Directory (Azure AD). Najdete ho na stránce pro správu předplatného v Azure Portal.|null|true|
|AzureSDKPath|Cesta pro hledání závislých sestavení v sadě Azure SDK|null|true|
|Interaktivní|Určuje, zda má být pro ověřování použit interaktivní režim.|false (nepravda)|false (nepravda)|
|ClientId|ID aplikace Azure AD, které je vyžadováno pro neinteraktivní ověřování.|null|Vyžaduje se pro neinteraktivní ověřování.|
|Tajný kód|Tajný klíč nebo heslo pro neinteraktivní ověřování. Měl by se používat jenom v důvěryhodném a zabezpečeném prostředí.|null|Vyžaduje se pro neinteraktivní ověřování nebo jinak použít SecreteFile.|
|SecreteFile|Soubor uloží tajný klíč nebo heslo pro neinteraktivní ověřování. Ujistěte se, že je budete moct přečíst jenom aktuální uživatel.|null|Vyžaduje se pro neinteraktivní ověřování nebo jinak použít tajný klíč.|
|Soubor_certifikátu|Soubor uloží certifikaci X. 509 pro neinteraktivní ověřování. Ve výchozím nastavení se používá ověřování pomocí tajného klíče klienta.|null|false (nepravda)|
| JobPrefix | Předpona pro databázové nasazení úlohy DDL U-SQL | Deploy_ + DateTime. Now | false (nepravda) |

## <a name="next-steps"></a>Další kroky

- [Postup testování kódu Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
- [Spusťte na svém místním počítači skript U-SQL](data-lake-analytics-data-lake-tools-local-run.md).
- [Pro vývoj databáze u-SQL použijte projekt u-SQL Database](data-lake-analytics-data-lake-tools-develop-usql-database.md).