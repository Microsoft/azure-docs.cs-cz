---
title: Spustit balíček SSIS s aktivitou uložené procedury – Azure
description: Tento článek popisuje, jak spustit balíček služba SSIS (SQL Server Integration Services) (SSIS) v kanálu Azure Data Factory pomocí aktivity uložená procedura.
author: swinarko
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6260606fe56d4dfc6bac93e04e726b5fd3298777
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391503"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Spuštění balíčku SSIS pomocí aktivity Uložená procedura ve službě Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak spustit balíček SSIS v kanálu Azure Data Factory pomocí aktivity uložené procedury. 

## <a name="prerequisites"></a>Požadavky

### <a name="azure-sql-database"></a>Azure SQL Database 
Návod v tomto článku používá Azure SQL Database k hostování katalogu SSIS. Můžete také použít spravovanou instanci Azure SQL.

## <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime
Vytvořte prostředí Azure-SSIS Integration runtime, pokud ho nepoužíváte, a to podle podrobných pokynů v tomto [kurzu: nasaďte balíčky SSIS](./tutorial-deploy-ssis-packages-azure.md).

## <a name="data-factory-ui-azure-portal"></a>Uživatelské rozhraní Data Factory (Azure Portal)
V této části použijete Data Factory uživatelské rozhraní k vytvoření kanálu Data Factory s aktivitou uložené procedury, která vyvolá balíček SSIS.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Prvním krokem je vytvoření datové továrny pomocí Azure Portal. 

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. Přejděte na [Azure Portal](https://portal.azure.com). 
3. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Objekt pro vytváření dat](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na stránce **Nová datová továrna** jako **název** zadejte **ADFTutorialDataFactory**. 
      
     ![Stránka Nová datová továrna](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba pole názvu, změňte název datové továrny (například na vaše_jméno_ADFTutorialDataFactory). Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
     ![Název není dostupný – chyba](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
4. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
   - Vyberte **vytvořit novou** a zadejte název skupiny prostředků.   
         
     Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
4. Jako **verzi** vyberte **V2**.
5. Vyberte **umístění** pro objekt pro vytváření dat. V rozevíracím seznamu se zobrazí pouze umístění podporovaná službou Data Factory. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou můžou být v jiných umístěních.
6. Zaškrtněte **Připnout na řídicí panel**.     
7. Klikněte na **Vytvořit**.
8. Na řídicím panelu se zobrazí následující dlaždice se stavem: **nasazování datové továrny**. 

     ![nasazování dlaždice datové továrny](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
     ![Domovská stránka objektu pro vytváření dat](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě aplikaci uživatelského rozhraní služby Azure Data Factory. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Vytvoření kanálu s aktivitou uložených procedur
V tomto kroku použijete uživatelské rozhraní Data Factory k vytvoření kanálu. Přidáte aktivitu uložené procedury do kanálu a nakonfigurujete ji tak, aby spouštěla balíček SSIS pomocí uložené procedury sp_executesql. 

1. Na stránce Začínáme klikněte na **vytvořit kanál**: 

    ![Stránka Začínáme](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Na panelu nástrojů **aktivity** rozbalte **Obecné** a přetáhněte aktivitu **uložená procedura** na plochu návrháře kanálu. 

    ![Aktivita uložené procedury přetažení](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. V okně vlastnosti aktivity uložená procedura přepněte na kartu **účet SQL** a klikněte na **+ Nový**. V Azure SQL Database, který je hostitelem katalogu SSIS (SSIDB Database), vytvoříte připojení k databázi. 
   
    ![Tlačítko Nová propojená služba](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. V okně **Nová propojená služba** proveďte následující kroky: 

    1. Jako **typ** vyberte **Azure SQL Database** .
    2. Vyberte **výchozí** Azure Integration runtime pro připojení k Azure SQL Database, který je hostitelem `SSISDB` databáze.
    3. Vyberte Azure SQL Database, který je hostitelem databáze SSISDB pro pole **název serveru** .
    4. Jako **název databáze** vyberte **SSISDB** .
    5. Do pole **uživatelské jméno** zadejte jméno uživatele, který má přístup k databázi.
    6. Jako **heslo** zadejte heslo uživatele. 
    7. Otestujte připojení k databázi kliknutím na tlačítko **Testovat připojení** .
    8. Kliknutím na tlačítko **Uložit** uložte propojenou službu. 

        ![Snímek obrazovky, který ukazuje proces přidání nové propojené služby.](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. V okně Vlastnosti přepněte na kartu **uložená procedura** na kartě **účet SQL** a proveďte následující kroky: 

    1. Vyberte **Upravit**. 
    2. Do pole **název uložené procedury** zadejte `sp_executesql` . 
    3. V části **parametry uložené procedury** klikněte na **+ Nový** . 
    4. Jako **název** parametru zadejte **stmt**. 
    5. Pro **typ** parametru zadejte **řetězec**. 
    6. Pro **hodnotu** parametru zadejte následující dotaz SQL:

        V dotazu SQL zadejte správné hodnoty pro parametry **Folder_name**, **PROJECT_NAME** a **package_name** . 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Propojená služba Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Pokud chcete ověřit konfiguraci kanálu, klikněte na **ověřit** na panelu nástrojů. Pokud chcete **Sestavu ověření kanálu** zavřít, klikněte na **>>**.

    ![Ověření kanálu](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Kliknutím na tlačítko **publikovat vše** klikněte na publikovat kanál a Data Factory. 

    ![Publikovat](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Spuštění a monitorování kanálu
V této části aktivujete spuštění kanálu a pak ho monitorujte. 

1. Pokud chcete aktivovat spuštění kanálu, klikněte na **aktivační událost** na panelu nástrojů a pak klikněte na **aktivovat**. 

    ![Aktivovat](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. V okně **Spuštění kanálu** vyberte **Dokončit**. 
3. Vlevo přepněte na kartu **Monitorování**. Zobrazí se spuštění kanálu a jeho stav spolu s dalšími informacemi (například počáteční čas spuštění). Pokud chcete zobrazení aktualizovat, klikněte na **Aktualizovat**.

    ![Spuštění kanálu](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí se pouze jedno spuštění aktivity, protože kanál má pouze jednu aktivitu (aktivita uložených procedur).

    ![Spuštění aktivit](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Můžete spustit následující **dotaz** pro databázi SSISDB v SQL Database a ověřit, zda byl balíček spuštěn. 

    ```sql
    select * from catalog.executions
    ```

    ![Ověření provedení balíčku](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Pro svůj kanál můžete také vytvořit plánovanou aktivační událost, aby se kanál spouštěl podle plánu (každou hodinu, každý den atd.). Příklad najdete v tématu [Vytvoření datové továrny – Data Factory uživatelského rozhraní](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V této části použijete Azure PowerShell k vytvoření kanálu Data Factory s aktivitou uložené procedury, která vyvolá balíček SSIS. 

Nainstalujte nejnovější Azure PowerShell moduly podle pokynů v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-az-ps). 

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Můžete buď použít stejný objekt pro vytváření dat, který má Azure-SSIS IR, nebo vytvořit samostatnou datovou továrnu. Následující postup popisuje kroky k vytvoření datové továrny. V této datové továrně vytvoříte kanál s aktivitou uložené procedury. Aktivita uložené procedury provede uloženou proceduru v databázi SSISDB ke spuštění balíčku SSIS. 

1. Definujte proměnnou pro název skupiny prostředků, kterou použijete později v příkazech PowerShellu. Zkopírujte do PowerShellu následující text příkazu, zadejte název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md) v uvozovkách a pak příkaz spusťte. Příklad: `"adfrg"`. 
   
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

5. Pokud chcete vytvořit datovou továrnu, spusťte následující rutinu **set-AzDataFactoryV2** s použitím vlastnosti location a ResourceGroupName z proměnné $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Je třeba počítat s následujícím:

* Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název a zkuste to znovu.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Pro vytvoření instancí Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem rolí **přispěvatel** nebo **vlastník** nebo **správcem** předplatného Azure.
* Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:**[Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.

### <a name="create-an-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database
Vytvořte propojenou službu, která propojí vaši databázi, která je hostitelem katalogu SSIS, s datovou továrnou. Data Factory používá k připojení k databázi SSISDB informace v této propojené službě a spustí uloženou proceduru pro spuštění balíčku SSIS. 

1. Ve složce **C:\ADF\RunSSISPackage** vytvořte soubor JSON s názvem **AzureSqlDatabaseLinkedService.js** s následujícím obsahem: 

    > [!IMPORTANT]
    > &lt; &gt; &lt; &gt; &lt; Před uložením souboru položky servername, username a password nahraďte &gt; hodnotami vaší Azure SQL Database.

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

2. V **Azure PowerShell** přepněte do složky **C:\ADF\RunSSISPackage** .

3. Spuštěním rutiny **set-AzDataFactoryV2LinkedService** vytvořte propojenou službu: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Vytvoření kanálu s aktivitou uložených procedur 
V tomto kroku vytvoříte kanál s aktivitou uložené procedury. Tato aktivita vyvolá uloženou proceduru sp_executesql pro spuštění balíčku SSIS. 

1. Ve složce **C:\ADF\RunSSISPackage** vytvořte soubor JSON s názvem **RunSSISPackagePipeline.js** s následujícím obsahem:

    > [!IMPORTANT]
    > &lt; &gt; Před uložením souboru nahraďte název složky, &lt; název projektu &gt; , &lt; název balíčku &gt; názvy složky, projektu a balíčku v katalogu SSIS. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. Pokud chcete vytvořit kanál: **RunSSISPackagePipeline**, spusťte rutinu **set-AzDataFactoryV2Pipeline** .

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Zde je ukázkový výstup:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Vytvoření spuštění kanálu
Ke spuštění kanálu použijte rutinu **Invoke-AzDataFactoryV2Pipeline** . Tato rutina vrací ID spuštění kanálu pro budoucí monitorování.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

Spusťte následující skript PowerShellu, který bude nepřetržitě kontrolovat stav spuštění kanálu, dokud nedokončí kopírování dat. Zkopírujte/vložte následující skript v okně PowerShellu a stiskněte klávesu Enter. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>Vytvoření triggeru
V předchozím kroku jste vyvolali kanál na vyžádání. Můžete také vytvořit aktivační proceduru Schedule pro spuštění kanálu podle plánu (každou hodinu, každý den atd.).

1. Ve složce **C:\ADF\RunSSISPackage** vytvořte soubor JSON s názvem **MyTrigger.js** s následujícím obsahem: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. V **Azure PowerShell** přepněte do složky **C:\ADF\RunSSISPackage** .
3. Spusťte rutinu **set-AzDataFactoryV2Trigger** , která vytvoří Trigger. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Ve výchozím nastavení je aktivační událost v zastaveném stavu. Spusťte Trigger spuštěním rutiny **Start-AzDataFactoryV2Trigger** . 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Spuštěním rutiny **Get-AzDataFactoryV2Trigger** ověřte, že je aktivační událost spuštěná. 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Po další hodiny spusťte následující příkaz. Pokud je například aktuální čas 3:25. UTC, spusťte příkaz ve 4. UTC. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Můžete spustit následující dotaz pro databázi SSISDB v SQL Database a ověřit, zda byl balíček spuštěn. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Další kroky
Kanál můžete také monitorovat pomocí Azure Portal. Podrobné pokyny najdete v tématu [monitorování kanálu](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).