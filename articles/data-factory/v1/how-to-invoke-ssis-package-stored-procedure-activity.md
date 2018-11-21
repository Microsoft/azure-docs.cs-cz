---
title: Vyvolání balíčků SSIS pomocí Azure Data Factory - aktivity uložené procedury | Dokumentace Microsoftu
description: Tento článek popisuje, jak k vyvolání balíčku pro SQL Server Integration Services (SSIS) z kanálu služby Azure Data Factory pomocí aktivity uložených procedur.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: eb9d35b132a0aa3f0702604444f8a760bf66cf9a
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275577"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Vyvolání balíčků SSIS pomocí aktivity uložených procedur ve službě Azure Data Factory
Tento článek popisuje, jak vyvolat z kanálu služby Azure Data Factory balíčku SSIS pomocí aktivity uložených procedur. 

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [balíčků vyvolání služby SSIS pomocí aktivity uložených procedur v](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Požadavky

### <a name="azure-sql-database"></a>Azure SQL Database 
Názorný postup v tomto článku se používá Azure SQL database, který hostuje katalog služby SSIS. Můžete také použít Azure SQL Database Managed Instance.

### <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime
Vytvořit prostředí Azure-SSIS integration runtime, pokud nemáte podle podrobných pokynů v [kurz: balíčků služby SSIS nasazovat](../tutorial-create-azure-ssis-runtime-portal.md). Data Factory verze 1 nelze použít k vytvoření prostředí Azure-SSIS integration runtime. 

## <a name="azure-portal"></a>portál Azure
V této části použijete Azure portal vytvoříte kanál služby Data Factory s aktivitou uložené procedury, která volá balíčku SSIS.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Prvním krokem je vytvoření datové továrny pomocí webu Azure portal. 

1. Přejděte na [Azure Portal](https://portal.azure.com). 
2. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Datová továrna](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na stránce **Nová datová továrna** jako **název** zadejte **ADFTutorialDataFactory**. 
      
     ![Stránka Nová datová továrna](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Název datové továrny Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba pole názvu, změňte název datové továrny (například na vaše_jméno_ADFTutorialDataFactory). Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](data-factory-naming-rules.md).

    `Data factory name ADFTutorialDataFactory is not available`
3. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
4. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
    Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../../azure-resource-manager/resource-group-overview.md).  
4. Vyberte **V1** pro **verze**.
5. Vyberte **umístění** pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze umístění podporovaná službou Data Factory. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou můžou být v jiných umístěních.
6. Zaškrtněte **Připnout na řídicí panel**.     
7. Klikněte na možnost **Vytvořit**.
8. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
    ![Domovská stránka datové továrny](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Klikněte na tlačítko **vytvořit a nasadit** dlaždice editoru služby Data Factory.

    ![Data Factory Editor](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-editor.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database
Vytvořte propojenou službu, která propojí vaši databázi Azure SQL, který hostuje katalog služby SSIS se svou datovou továrnou. Data Factory používá pro připojení k databázi SSISDB informace v této propojené službě a spouští uloženou proceduru pro spuštění balíčku SSIS. 

1. V editoru služby Data Factory, klikněte na tlačítko **nové datové úložiště** na nabídku a klikněte na **Azure SQL Database**. 

    ![Nové datové úložiště -> Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-sql-database-linked-service-menu.png)
2. V pravém podokně proveďte následující kroky:

    1. Nahraďte `<servername>` s názvem serveru Azure SQL. 
    2. Nahraďte `<databasename>` s **SSISDB** (název databázi katalogu služby SSIS). 
    3. Nahraďte `<username@servername>` s názvem uživatele, který má přístup k serveru Azure SQL. 
    4. Nahraďte `<password>` pomocí hesla pro daného uživatele. 
    5. Propojenou službu nasadíte kliknutím **nasadit** tlačítko na panelu nástrojů. 

        ![Propojená služba Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-definition.png)

### <a name="create-a-dummy-dataset-for-output"></a>Vytvořte fiktivní datovou sadu pro výstup
Tento výstupní datová sada je fiktivní datovou sadu, která řídí plán kanálu. Všimněte si, že frekvence je nastavená na Hour a interval je nastavená na 1. Proto kanál spouští jednou za hodinu v rámci kanálu počáteční a koncové časy. 

1. V levém podokně z editoru služby Data Factory, klikněte na tlačítko **... Další** -> **nová datová sada** -> **Azure SQL**.

    ![Více -> Nová datová sada](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-dataset-menu.png)
2. Zkopírujte následující fragment kódu JSON do editoru JSON v pravém podokně. 
    
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
3. Na panelu nástrojů klikněte na **Nasadit**. Tato akce nasadí datovou sadu ve službě Azure Data Factory. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Vytvoření kanálu s aktivitou uložené procedury 
V tomto kroku vytvoříte kanál s aktivitou uložené procedury. Aktivita vyvolá sp_executesql uložené procedury pro spuštění vašeho balíčku služby SSIS. 

1. V levém podokně klikněte na tlačítko **... Další** a poté na **Nový kanál.
2. Do editoru JSON, zkopírujte následující fragment kódu JSON: 

    > [!IMPORTANT]
    > Nahraďte &lt;název složky&gt;, &lt;název projektu&gt;, &lt;název balíčku&gt; složky, projektu a balíčku v katalogu služby SSIS před uložením tohoto souboru.

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
            "start": "2018-01-19T00:00:00Z",
            "end": "2018-01-19T05:00:00Z",
            "isPaused": false
        }
    }    
    ```
3. Na panelu nástrojů klikněte na **Nasadit**. Tato akce nasadí kanál ke službě Azure Data Factory. 

### <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu
Plán na výstupní datová sada je definována jako po hodinách. Koncový čas profilace je pět hodin od času spuštění. Proto se zobrazí pět spuštění kanálu. 

1. Zavřete editor windows tak, aby se zobrazí domovská stránka datové továrny. Klikněte na tlačítko **monitorování a správa** dlaždici. 

    ![Dlaždice Diagram](./media/how-to-invoke-ssis-package-stored-procedure-activity/monitor-manage-tile.png)
2. Aktualizace **počáteční čas** a **čas ukončení** k **01/18/2018 08:30 AM** a **01/20/2018 08:30 AM**a klikněte na tlačítko **použít**. Měli byste vidět **okna aktivit** související se spuštěním kanálu. 

    ![Okna aktivit](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-windows.png)

Další informace o monitorování kanálů najdete v tématu [monitorování a Správa kanálů Azure Data Factory pomocí monitorování a správu aplikací](data-factory-monitor-manage-app.md).

## <a name="azure-powershell"></a>Azure PowerShell
V této části použijete Azure PowerShell k vytvoření kanálu Data Factory s aktivitou uložené procedury, která volá balíčku SSIS.

Nainstalujte nejnovější moduly Azure PowerShellu podle pokynů v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Následující postup předvádí kroky k vytvoření datové továrny. Vytvoření kanálu s aktivitou uložené procedury v této datové továrně. Aktivity uložených procedur spouští uloženou proceduru v databázi SSISDB pro spuštění vašeho balíčku služby SSIS.

1. Definujte proměnnou pro název skupiny prostředků, kterou použijete později v příkazech PowerShellu. Zkopírujte do PowerShellu následující text příkazu, zadejte název [skupiny prostředků Azure](../../azure-resource-manager/resource-group-overview.md) v uvozovkách a pak příkaz spusťte. Například: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Pokud již skupina prostředků existuje, nepřepisujte ji. Přiřaďte proměnné `$ResourceGroupName` jinou hodnotu a spusťte tento příkaz znovu.
2. Pokud chcete vytvořit skupinu prostředků Azure, spusťte následující příkaz: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Pokud již skupina prostředků existuje, nepřepisujte ji. Přiřaďte proměnné `$ResourceGroupName` jinou hodnotu a spusťte tento příkaz znovu. 
3. Definujte proměnnou název datové továrny. 

    > [!IMPORTANT]
    >  Aktualizujte název datové továrny tak, aby byl globálně jedinečný. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Vytvořit datovou továrnu, spusťte následující příkaz **New-AzureRmDataFactory** rutiny použitím vlastností Location a ResourceGroupName z proměnné $ResGrp: 
    
    ```powershell       
    $df = New-AzureRmDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Je třeba počítat s následujícím:

* Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název a zkuste to znovu.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Pro vytvoření instancí Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem rolí **přispěvatel** nebo **vlastník** nebo **správcem** předplatného Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database
Vytvořte propojenou službu, která propojí vaši databázi Azure SQL, který hostuje katalog služby SSIS se svou datovou továrnou. Data Factory používá pro připojení k databázi SSISDB informace v této propojené službě a spouští uloženou proceduru pro spuštění balíčku SSIS. 

1. Vytvořte soubor JSON s názvem **AzureSqlDatabaseLinkedService.json** v **C:\ADF\RunSSISPackage** složka s následujícím obsahem: 

    > [!IMPORTANT]
    > Nahraďte &lt;servername&gt;, &lt;uživatelské jméno&gt;@&lt;servername&gt; a &lt;heslo&gt; hodnotami pro Azure SQL Database před ukládání souboru.

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
2. V **prostředí Azure PowerShell**, přepněte **C:\ADF\RunSSISPackage** složky.
3. Spustit **New-AzureRmDataFactoryLinkedService** rutina pro vytvoření propojené služby: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Vytvoření výstupní datové sady
Tento výstupní datová sada je fiktivní datovou sadu, která řídí plán kanálu. Všimněte si, že frekvence je nastavená na Hour a interval je nastavená na 1. Proto kanál spouští jednou za hodinu v rámci kanálu počáteční a koncové časy. 

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
2. Spustit **New-AzureRmDataFactoryDataset** rutina pro vytvoření datové sady. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Vytvoření kanálu s aktivitou uložené procedury 
V tomto kroku vytvoříte kanál s aktivitou uložené procedury. Aktivita vyvolá sp_executesql uložené procedury pro spuštění vašeho balíčku služby SSIS. 

1. Vytvořte soubor JSON s názvem **MyPipeline.json** v **C:\ADF\RunSSISPackage** složka s následujícím obsahem:

    > [!IMPORTANT]
    > Nahraďte &lt;název složky&gt;, &lt;název projektu&gt;, &lt;název balíčku&gt; složky, projektu a balíčku v katalogu služby SSIS před uložením tohoto souboru.

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

2. Vytvořte kanál: **RunSSISPackagePipeline**, spusťte **New-AzureRmDataFactoryPipeline** rutiny.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

2. Spustit **Get-AzureRmDataFactorySlice** získat tak podrobné údaje o všech řezech datové sady výstupní datovou sadu **, která je výstupní tabulkou kanálu.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Všimněte si, že hodnota StartDateTime, kterou tady určíte, je stejná jako počáteční čas uvedený v kódu JSON kanálu. 
3. Spuštěním rutiny **Get-AzureRmDataFactoryRun** získáte podrobnosti o spouštění aktivity pro určitý řez.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Rutinu můžete spouštět opakovaně, dokud se u řezu neobjeví stav **Připraveno** nebo **Nezdařilo se**. 

    Spuštěním následujícího dotazu proti databázi SSISDB na serveru služby Azure SQL a ověřte, že balíček provedeny. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Další postup
Podrobnosti o aktivitě uložené procedury, najdete v článku [aktivity uložené procedury](data-factory-stored-proc-activity.md) článku.

