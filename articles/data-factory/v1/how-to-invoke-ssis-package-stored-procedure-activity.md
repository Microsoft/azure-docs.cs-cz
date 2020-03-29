---
title: Vyvolat balíček SSIS pomocí Azure Data Factory – aktivita uložené procedury
description: Tento článek popisuje, jak vyvolat balíček SQL Server Integration Services (SSIS) z kanálu Azure Data Factory pomocí aktivity uložené procedury.
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
ms.openlocfilehash: ea86c4670a8eb6dc5e2133ed01045e8aada0f707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438787"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Vyvolání balíčku SSIS pomocí uložené aktivity procedury v Azure Data Factory
Tento článek popisuje, jak vyvolat balíček SSIS z kanálu Azure Data Factory pomocí uložené aktivity procedury. 

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Vyvolání balíčků SSIS pomocí uložené aktivity procedury v aplikaci](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Požadavky

### <a name="azure-sql-database"></a>Azure SQL Database 
Návod v tomto článku používá databázi Azure SQL, která je hostitelem katalogu SSIS. Můžete také použít spravovanou instanci Azure SQL Database.

### <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime
Vytvořte runtime integrace Azure-SSIS, pokud ho nemáte podle podrobných pokynů v [kurzu: Nasazení balíčků SSIS](../tutorial-create-azure-ssis-runtime-portal.md). Data Factory verze 1 nelze použít k vytvoření za běhu integrace Azure-SSIS. 

## <a name="azure-powershell"></a>Azure PowerShell
V této části použijete Azure PowerShell k vytvoření kanálu data factory s uloženou aktivitou procedury, která vyvolá balíček SSIS.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Nainstalujte nejnovější moduly Azure PowerShell podle pokynů v části [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Následující postup obsahuje kroky k vytvoření datové továrny. Vytvoříte kanál s aktivitou uložené procedury v této datové továrně. Uložená aktivita procedury provede uloženou proceduru v databázi SSISDB ke spuštění balíčku SSIS.

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

5. Chcete-li vytvořit datovou továrnu, spusťte následující rutinu **New-AzDataFactory** pomocí vlastnosti Location a ResourceGroupName z proměnné $ResGrp: 
    
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
Vytvořte propojenou službu, která propojí databázi Azure SQL, která je hostitelem katalogu SSIS, s vaší továrně dat. Data Factory používá informace v této propojené služby pro připojení k databázi SSISDB a provede uloženou proceduru ke spuštění balíčku SSIS. 

1. Vytvořte soubor JSON s názvem **AzureSqlDatabaseLinkedService.json** ve složce **C:\ADF\RunSSISPackage** s následujícím obsahem: 

    > [!IMPORTANT]
    > Před &lt;uložením souboru nahraďte&gt;název_serveru , &lt;uživatelské jméno_serveru&gt;@&lt;&gt; a &lt;heslo&gt; hodnotami databáze Azure SQL Database.

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
2. V **prostředí Azure PowerShell**přepněte do složky **C:\ADF\RunSSISPackage.**
3. Spusťte rutinu **New-AzDataFactoryLinkedService** a vytvořte propojenou službu: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Vytvoření výstupní datové sady
Tato výstupní datová sada je fiktivní datová sada, která řídí plán kanálu. Všimněte si, že frekvence je nastavena na hodinu a interval je nastaven na 1. Proto kanálu běží jednou za hodinu v rámci počáteční a koncové časy kanálu. 

1. Vytvořte soubor OutputDataset.json s následujícím obsahem: 
    
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
2. Spusťte rutinu **New-AzDataFactoryDataset** a vytvořte datovou sadu. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Vytvoření kanálu s uloženou aktivitou procedury 
V tomto kroku vytvoříte kanál s aktivitou uložené procedury. Aktivita vyvolá sp_executesql uloženou proceduru ke spuštění balíčku SSIS. 

1. Vytvořte soubor JSON s názvem **MyPipeline.json** ve složce **C:\ADF\RunSSISPackage** s následujícím obsahem:

    > [!IMPORTANT]
    > Před &lt;uložením souboru nahraďte&gt;název složky , &lt;&gt; &lt;&gt; projektu a balíčku v katalogu SSIS názvy složek, projektů a balíčků.

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

2. Chcete-li vytvořit kanál: **RunSSISPackagePipeline**, spusťte rutinu **New-AzDataFactoryPipeline.**

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Spusťte **Get-AzDataFactorySlice,** chcete-li získat podrobnosti o všech řezech výstupní datové sady**, což je výstupní tabulka kanálu.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Všimněte si, že hodnota StartDateTime, kterou tady určíte, je stejná jako počáteční čas uvedený v kódu JSON kanálu. 
1. Spusťte **Get-AzDataFactoryRun** získat podrobnosti o spuštění aktivity pro konkrétní řez.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Rutinu můžete spouštět opakovaně, dokud se u řezu neobjeví stav **Připraveno** nebo **Nezdařilo se**. 

    Můžete spustit následující dotaz proti databázi SSISDB na serveru Azure SQL a ověřit, že balíček byl spuštěn. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Další kroky
Podrobnosti o aktivitě uložené procedury naleznete v článku [Aktivita uložené procedury.](data-factory-stored-proc-activity.md)

