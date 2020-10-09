---
title: Vyvolat balíček SSIS pomocí aktivity uložená procedura Azure Data Factory
description: Tento článek popisuje, jak vyvolat balíček služba SSIS (SQL Server Integration Services) (SSIS) z kanálu Azure Data Factory pomocí aktivity uložená procedura.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: ab3b5c2ba892205f87235f7f0ce009719016622d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85322128"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Vyvolání balíčku SSIS pomocí aktivity uložená procedura v Azure Data Factory
Tento článek popisuje, jak vyvolat balíček SSIS z kanálu Azure Data Factory pomocí aktivity uložené procedury. 

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [vyvolání balíčků SSIS pomocí aktivity uložená procedura v](../how-to-invoke-ssis-package-stored-procedure-activity.md)tématu.

## <a name="prerequisites"></a>Požadavky

### <a name="azure-sql-database"></a>Azure SQL Database 
Návod v tomto článku používá Azure SQL Database. Můžete také použít spravovanou instanci Azure SQL.

### <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime
Vytvořte prostředí Azure-SSIS Integration runtime, pokud ho nepoužíváte, a to podle podrobných pokynů v tomto [kurzu: nasaďte balíčky SSIS](../tutorial-create-azure-ssis-runtime-portal.md). Data Factory verze 1 nelze použít k vytvoření prostředí Azure-SSIS Integration runtime. 

## <a name="azure-powershell"></a>Azure PowerShell
V této části použijete Azure PowerShell k vytvoření kanálu Data Factory s aktivitou uložené procedury, která vyvolá balíček SSIS.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Nainstalujte nejnovější Azure PowerShell moduly podle pokynů v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Následující postup popisuje kroky k vytvoření datové továrny. V této datové továrně vytvoříte kanál s aktivitou uložené procedury. Aktivita uložené procedury provede uloženou proceduru v databázi SSISDB ke spuštění balíčku SSIS.

1. Definujte proměnnou pro název skupiny prostředků, kterou použijete později v příkazech PowerShellu. Zkopírujte do PowerShellu následující text příkazu, zadejte název [skupiny prostředků Azure](../../azure-resource-manager/management/overview.md) v uvozovkách a pak příkaz spusťte. Například: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Pokud již skupina prostředků existuje, nepřepisujte ji. Přiřaďte proměnné `$ResourceGroupName` jinou hodnotu a spusťte tento příkaz znovu.
2. Pokud chcete vytvořit skupinu prostředků Azure, spusťte následující příkaz: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Pokud již skupina prostředků existuje, nepřepisujte ji. Přiřaďte proměnné `$ResourceGroupName` jinou hodnotu a spusťte tento příkaz znovu. 
3. Definujte proměnnou název datové továrny. 

    > [!IMPORTANT]
    >  Aktualizujte název datové továrny tak, aby byl globálně jedinečný. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Pokud chcete vytvořit datovou továrnu, spusťte následující rutinu **New-AzDataFactory** s použitím vlastnosti location a ResourceGroupName z proměnné $ResGrp: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Je třeba počítat s následujícím:

* Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název a zkuste to znovu.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Pro vytvoření instancí Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem rolí **přispěvatel** nebo **vlastník** nebo **správcem** předplatného Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database
Vytvořte propojenou službu, která propojí vaši databázi v Azure SQL Database, která hostuje katalog SSIS s vaší datovou továrnou. Data Factory používá k připojení k databázi SSISDB informace v této propojené službě a spustí uloženou proceduru pro spuštění balíčku SSIS. 

1. Ve složce **C:\ADF\RunSSISPackage** vytvořte soubor JSON s názvem **AzureSqlDatabaseLinkedService.js** s následujícím obsahem: 

    > [!IMPORTANT]
    > &lt; &gt; &lt; &gt; @ &lt; &gt; Před uložením souboru položky servername, username ServerName a Password nahraďte &lt; &gt; hodnotami vaší Azure SQL Database.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. V **Azure PowerShell**přepněte do složky **C:\ADF\RunSSISPackage** .
3. Spuštěním rutiny **New-AzDataFactoryLinkedService** vytvořte propojenou službu: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Vytvoření výstupní datové sady
Tato výstupní datová sada je fiktivní datová sada, která řídí plán kanálu. Všimněte si, že frekvence je nastavená na Hour (hodina) a interval je nastavená na 1. Proto se kanál spustí jednou za hodinu v počátečním a koncovém času kanálu. 

1. Vytvořit OutputDataset.jsv souboru s následujícím obsahem: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Spuštěním rutiny **New-AzDataFactoryDataset** Vytvořte datovou sadu. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Vytvoření kanálu s aktivitou uložených procedur 
V tomto kroku vytvoříte kanál s aktivitou uložené procedury. Tato aktivita vyvolá uloženou proceduru sp_executesql pro spuštění balíčku SSIS. 

1. Ve složce **C:\ADF\RunSSISPackage** vytvořte soubor JSON s názvem **MyPipeline.js** s následujícím obsahem:

    > [!IMPORTANT]
    > &lt; &gt; Před uložením souboru nahraďte název složky, &lt; název projektu &gt; , &lt; název balíčku &gt; názvy složky, projektu a balíčku v katalogu SSIS.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Pokud chcete vytvořit kanál: **RunSSISPackagePipeline**, spusťte rutinu **New-AzDataFactoryPipeline** .

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Spuštěním rutiny **Get-AzDataFactorySlice** získáte podrobnosti o všech řezech výstupní datové sady * *, což je výstupní tabulka kanálu.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Všimněte si, že hodnota StartDateTime, kterou tady určíte, je stejná jako počáteční čas uvedený v kódu JSON kanálu. 
1. Spuštěním rutiny **Get-AzDataFactoryRun** získáte podrobnosti o spuštění aktivit pro konkrétní řez.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Rutinu můžete spouštět opakovaně, dokud se u řezu neobjeví stav **Připraveno** nebo **Nezdařilo se**. 

    Můžete spustit následující dotaz pro databázi SSISDB na serveru, abyste ověřili, že se balíček spustil. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Další kroky
Podrobnosti o aktivitě uložené procedury najdete v článku o [aktivitě uložené procedury](data-factory-stored-proc-activity.md) .

