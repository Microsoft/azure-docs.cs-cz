---
title: Vyvolání balíčku služby SSIS pomocí Azure Data Factory - aktivity uložené procedury | Microsoft Docs
description: Tento článek popisuje, jak má být vyvolán balíček integrační služby SSIS (SQL Server) z kanál služby Azure Data Factory pomocí aktivity uložené procedury.
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
ms.openlocfilehash: 9608af734d39627435a81e1d853c2546eabcdb0f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623430"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Vyvolání balíčku služby SSIS pomocí aktivity uložené procedury v Azure Data Factory
Tento článek popisuje, jak má být vyvolán balíčku služby SSIS z kanál služby Azure Data Factory pomocí aktivity uložené procedury. 

> [!NOTE]
> Tento článek se týká verze 1 objektu pro vytváření dat, která je obvykle dostupná. Pokud používáte verze 2 služby Data Factory, který je ve verzi Public Preview, najdete v části [balíčky SSIS vyvolání pomocí aktivity uložené procedury v verze 2](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Požadavky

### <a name="azure-sql-database"></a>Azure SQL Database 
Návod v tomto článku používá databázi Azure SQL, který je hostitelem služby SSIS katalogu. Můžete také použít Azure spravované Instance SQL (Preview).

### <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime
Vytvoření modulu runtime integrace Azure SSIS, pokud nemáte dodržením podrobných pokynů v [kurz: balíčky nasazení SSIS](../tutorial-create-azure-ssis-runtime-portal.md). Je nutné vytvořit objekt pro vytváření dat verze 2: vytvoření modulu runtime integrace Azure SSIS. 

## <a name="azure-portal"></a>Azure Portal
V této části použijte portál Azure k vytvoření objektu pro vytváření dat kanál s aktivitou uložené procedury, která volá balíčku služby SSIS.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Prvním krokem je pro vytváření dat pomocí portálu Azure. 

1. Přejděte na [Azure Portal](https://portal.azure.com). 
2. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Objekt pro vytváření dat](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na stránce **Nová datová továrna** jako **název** zadejte **ADFTutorialDataFactory**. 
      
     ![Stránka Nová datová továrna](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba pole názvu, změňte název datové továrny (například na vaše_jméno_ADFTutorialDataFactory). Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](data-factory-naming-rules.md).

    `Data factory name ADFTutorialDataFactory is not available`
3. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
4. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
    Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../../azure-resource-manager/resource-group-overview.md).  
4. Vyberte **V1** pro **verze**.
5. Vyberte **umístění** pro objekt pro vytváření dat. V rozevíracím seznamu se zobrazí pouze umístění podporovaná službou Data Factory. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou můžou být v jiných umístěních.
6. Zaškrtněte **Připnout na řídicí panel**.     
7. Klikněte na možnost **Vytvořit**.
8. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
    ![Domovská stránka objektu pro vytváření dat](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Klikněte na tlačítko **vytvořit a nasadit** dlaždici spustíte editoru služby Data Factory.

    ![Data Factory Editor](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-editor.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database
Vytvoření propojené služby propojení Azure SQL database, který je hostitelem služby SSIS katalogu datovou továrnu. Objekt pro vytváření dat používá informace v rámci této propojené služby pro připojení k databázi SSISDB a spustí uloženou proceduru spustit balíčku služby SSIS. 

1. V editoru služby Data Factory, klikněte na tlačítko **nové datové úložiště** v nabídce a klikněte na tlačítko **Azure SQL Database**. 

    ![Nové datové úložiště -> Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-sql-database-linked-service-menu.png)
2. V pravém podokně proveďte následující kroky:

    1. Nahraďte `<servername>` s názvem serveru Azure SQL. 
    2. Nahraďte `<databasename>` s **SSISDB** (název databáze katalogu služby SSIS). 
    3. Nahraďte `<username@servername>` s názvem uživatele, který má přístup k serveru Azure SQL. 
    4. Nahraďte `<password>` pomocí hesla pro uživatele. 
    5. Propojenou službu nasadíte kliknutím **nasadit** tlačítka na panelu nástrojů. 

        ![Propojená služba Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-definition.png)

### <a name="create-a-dummy-dataset-for-output"></a>Vytvoření fiktivní datové sady pro výstup
Tato výstupní datové sady je fiktivní datovou sadu, která určuje plán kanálu. Všimněte si, že je nastavena frekvence na hodinu a interval je nastavena na hodnotu 1. Proto kanálu spouští jednou za hodinu v rámci kanálu spuštění a ukončení. 

1. V levém podokně z editoru služby Data Factory klikněte na tlačítko **... Další** -> **nová datová sada** -> **Azure SQL**.

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
3. Na panelu nástrojů klikněte na **Nasadit**. Tato akce nasadí datovou sadu do služby Azure Data Factory. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Vytvoření kanálu s aktivitou uložené procedury 
V tomto kroku vytvoříte kanál s aktivitou uložené procedury. Aktivity vyvolá sp_executesql uložený postup spuštění vašeho balíčku služby SSIS. 

1. V levém podokně klikněte na **... Další** a poté na **Nový kanál**.
2. Zkopírujte následující fragment kódu JSON do editoru JSON: 

    > [!IMPORTANT]
    > Nahraďte &lt;název složky&gt;, &lt;název projektu&gt;, &lt;název balíčku&gt; s názvy složek, projekt a balíčku v katalogu služby SSIS před uložením souboru.

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
Plán na výstupní datovou sadu je definován jako každou hodinu. Konečný čas kanálu je pět hodin od času zahájení. Proto uvidíte pět spustí kanálu. 

1. Zavřete editor windows tak, aby se zobrazí domovská stránka služby data Factory. Klikněte na tlačítko **monitorování a správa** dlaždici. 

    ![Dlaždice Diagram](./media/how-to-invoke-ssis-package-stored-procedure-activity/monitor-manage-tile.png)
2. Aktualizace **počáteční čas** a **čas ukončení** k **01/18 nebo 2018 08:30 AM** a **01/20/2018 08:30 AM**a klikněte na tlačítko **použít**. Měli byste vidět **aktivity windows** přidružené kanálu spustit. 

    ![Aktivity windows](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-windows.png)

Další informace o monitorování kanály najdete v tématu [monitorování a Správa kanálů služby Azure Data Factory pomocí monitorování a správu aplikací](data-factory-monitor-manage-app.md).

## <a name="azure-powershell"></a>Azure PowerShell
V této části použijte prostředí Azure PowerShell k vytvoření objektu pro vytváření dat kanál s aktivitou uložené procedury, která volá balíčku služby SSIS.

Nainstalujte nejnovější moduly Azure PowerShellu podle pokynů v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Následující postup popisuje kroky k vytvoření služby data factory. Vytvoření kanálu s aktivitou uložené procedury v této datové továrně. Aktivita uložené procedury spustí uloženou proceduru v databázi SSISDB ke spuštění vašeho balíčku služby SSIS.

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

5. K vytvoření objektu pro vytváření dat, spusťte následující **New-AzureRmDataFactory** rutiny pomocí vlastnosti umístění a název skupiny prostředků z $ResGrp proměnné: 
    
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
Vytvoření propojené služby propojení Azure SQL database, který je hostitelem služby SSIS katalogu datovou továrnu. Objekt pro vytváření dat používá informace v rámci této propojené služby pro připojení k databázi SSISDB a spustí uloženou proceduru spustit balíčku služby SSIS. 

1. Vytvořte soubor JSON s názvem **AzureSqlDatabaseLinkedService.json** v **C:\ADF\RunSSISPackage** složku s následujícím obsahem: 

    > [!IMPORTANT]
    > Nahraďte &lt;servername&gt;, &lt;uživatelské jméno&gt;@&lt;servername&gt; a &lt;heslo&gt; s hodnotami před databázi SQL Azure ukládání souboru.

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
2. V **prostředí Azure PowerShell**, přepnout **C:\ADF\RunSSISPackage** složky.
3. Spustit **New-AzureRmDataFactoryLinkedService** rutiny pro vytvoření propojené služby: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Vytvoření výstupní datové sady
Tato výstupní datové sady je fiktivní datovou sadu, která určuje plán kanálu. Všimněte si, že je nastavena frekvence na hodinu a interval je nastavena na hodnotu 1. Proto kanálu spouští jednou za hodinu v rámci kanálu spuštění a ukončení. 

1. Vytvořte soubor OuputDataset.json s následujícím obsahem: 
    
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
2. Spustit **New-AzureRmDataFactoryDataset** vytvořte datovou sadu. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Vytvoření kanálu s aktivitou uložené procedury 
V tomto kroku vytvoříte kanál s aktivitou uložené procedury. Aktivity vyvolá sp_executesql uložený postup spuštění vašeho balíčku služby SSIS. 

1. Vytvořte soubor JSON s názvem **MyPipeline.json** v **C:\ADF\RunSSISPackage** složku s následujícím obsahem:

    > [!IMPORTANT]
    > Nahraďte &lt;název složky&gt;, &lt;název projektu&gt;, &lt;název balíčku&gt; s názvy složek, projekt a balíčku v katalogu služby SSIS před uložením souboru.

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

2. K vytvoření kanálu: **RunSSISPackagePipeline**spusťte **New-AzureRmDataFactoryPipeline** rutiny.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

2. Spustit **Get-AzureRmDataFactorySlice** získat tak podrobné údaje o všech řezech tabulky Výstupní datovou sadu **, která je výstupní tabulkou kanálu.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Všimněte si, že hodnota StartDateTime, kterou tady určíte, je stejná jako počáteční čas uvedený v kódu JSON kanálu. 
3. Spuštěním rutiny **Get-AzureRmDataFactoryRun** získáte podrobnosti o spouštění aktivity pro určitý řez.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Rutinu můžete spouštět opakovaně, dokud se u řezu neobjeví stav **Připraveno** nebo **Nezdařilo se**. 

    Můžete spustit následující dotaz na databázi SSISDB v serveru Azure SQL ověřit, jestli balíček provést. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Další postup
Podrobnosti o aktivitě uložené procedury najdete v tématu [aktivity uložené procedury](data-factory-stored-proc-activity.md) článku.

