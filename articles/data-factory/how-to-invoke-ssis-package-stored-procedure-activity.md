---
title: Spuštění balíčku SSIS s aktivitou uložené procedury – Azure
description: Tento článek popisuje, jak spustit balíček SQL Server Integration Services (SSIS) v kanálu Azure Data Factory pomocí aktivity uložené procedury.
services: data-factory
documentationcenter: ''
author: swinarko
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: sawinark
ms.openlocfilehash: 7a935fa4c4e91cf8adcd6df467ac56eeecaf46c9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605944"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Spuštění balíčku SSIS pomocí aktivity Uložená procedura ve službě Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak spustit balíček SSIS v kanálu Azure Data Factory pomocí aktivity uložené procedury. 

## <a name="prerequisites"></a>Požadavky

### <a name="azure-sql-database"></a>Azure SQL Database 
Návod v tomto článku používá databázi Azure SQL, která je hostitelem katalogu SSIS. Můžete také použít spravovanou instanci Azure SQL Database.

## <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime
Vytvořte runtime integrace Azure-SSIS, pokud ho nemáte podle podrobných pokynů v [kurzu: Nasazení balíčků SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>UI datové továrny (portál Azure)
V této části použijete ui factory k vytvoření kanálu data factory s uloženou aktivitou procedury, která vyvolá balíček SSIS.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Prvním krokem je vytvoření datové továrny pomocí portálu Azure. 

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
   - Vyberte **Vytvořit nový**a zadejte název skupiny prostředků.   
         
     Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
4. Jako **verzi** vyberte **V2**.
5. Vyberte **umístění** pro objekt pro vytváření dat. V rozevíracím seznamu se zobrazí pouze umístění podporovaná službou Data Factory. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou můžou být v jiných umístěních.
6. Zaškrtněte **Připnout na řídicí panel**.     
7. Klikněte na **Vytvořit**.
8. Na řídicím panelu se zobrazí následující dlaždice se stavem: **Nasazení datové továrny**. 

     ![nasazování dlaždice datové továrny](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
     ![Domovská stránka objektu pro vytváření dat](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě aplikaci uživatelského rozhraní služby Azure Data Factory. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Vytvoření kanálu s uloženou aktivitou procedury
V tomto kroku použijete ui factory data k vytvoření kanálu. Přidáte uloženou aktivitu procedury do kanálu a nakonfigurujete ji tak, aby spouštěla balíček SSIS pomocí sp_executesql uloženou proceduru. 

1. Na stránce Začínáme klikněte na **Vytvořit kanál**: 

    ![Stránka Začínáme](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. V panelu nástrojů **Aktivity** rozbalte **položku Obecné**a přetáhněte aktivitu **uložené procedury** na povrch návrháře kanálu. 

    ![Aktivita uložené procedury přetažením](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. V okně vlastností aktivity uložené procedury přepněte na kartu **Účet SQL** a klepněte na tlačítko **+ Nový**. Vytvoříte připojení k databázi Azure SQL, která je hostitelem katalogu SSIS (databáze SSIDB). 
   
    ![Tlačítko Nová propojená služba](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. V okně **Nová propojená služba** proveďte následující kroky: 

    1. Vyberte **Azure SQL Database** for **Type**.
    2. Vyberte **výchozí** modul runtime integrace Azure pro připojení `SSISDB` k databázi Azure SQL, která databázi hostuje.
    3. Vyberte databázi Azure SQL, která je hostitelem databáze SSISDB pro pole **název serveru.**
    4. Vyberte **SSISDB** pro **název databáze**.
    5. Do **pole Uživatelské jméno**zadejte jméno uživatele, který má přístup k databázi.
    6. V **části Heslo**zadejte heslo uživatele. 
    7. Otestujte připojení k databázi klepnutím na tlačítko **Testovat připojení.**
    8. Uložte propojenou službu kliknutím na tlačítko **Uložit.** 

        ![Propojená služba Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. V okně vlastností přepněte na kartu **Uložená procedura** na kartě **Účet SQL** a proveďte následující kroky: 

    1. Vyberte **Upravit**. 
    2. Pro pole Název uložené `sp_executesql` **procedury** zadejte . 
    3. V části **Parametry uložené procedury** klepněte na **tlačítko + Nový.** 
    4. Pro **název** parametru zadejte **stmt**. 
    5. Pro **typ** parametru zadejte **String**. 
    6. Pro **hodnotu** parametru zadejte následující dotaz SQL:

        V dotazu SQL zadejte správné hodnoty pro parametry **folder_name**, **project_name**a **package_name.** 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Propojená služba Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Chcete-li ověřit konfiguraci kanálu, klepněte na tlačítko **Ověřit** na panelu nástrojů. Pokud chcete **Sestavu ověření kanálu** zavřít, klikněte na **>>**.

    ![Ověření kanálu](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Publikujte kanál do datové továrny kliknutím na tlačítko **Publikovat vše.** 

    ![Publikování](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Spuštění a sledování potrubí
V této části spustíte spuštění kanálu a pak jej sledovat. 

1. Pokud chcete spustit kanál, klikněte na panelu nástrojů na **Aktivovat** a klikněte na **Aktivovat .** 

    ![Aktivovat](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. V okně **Spuštění kanálu** vyberte **Dokončit**. 
3. Vlevo přepněte na kartu **Monitorování**. Zobrazí se spuštění kanálu a jeho stav spolu s dalšími informacemi (například Čas spuštění). Pokud chcete zobrazení aktualizovat, klikněte na **Aktualizovat**.

    ![Spuštění kanálu](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí se pouze jedna aktivita spustit jako potrubí má pouze jednu aktivitu (aktivity uložené procedury).

    ![Spuštění aktivit](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Můžete spustit následující **dotaz** proti databázi SSISDB na serveru Azure SQL a ověřit, že balíček byl spuštěn. 

    ```sql
    select * from catalog.executions
    ```

    ![Ověření spuštění balíčků](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Můžete také vytvořit naplánovanou aktivační událost pro váš kanál tak, aby kanál běží podle plánu (každou hodinu, denně atd.). Příklad najdete v [tématu Vytvoření datové továrny – ui datové továrny](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V této části použijete Azure PowerShell k vytvoření kanálu data factory s uloženou aktivitou procedury, která vyvolá balíček SSIS. 

Nainstalujte nejnovější moduly Azure PowerShell podle pokynů v části [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-az-ps). 

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Můžete použít stejnou datovou továrnu, která má Azure-SSIS IR nebo vytvořit samostatnou datovou továrnu. Následující postup obsahuje kroky k vytvoření datové továrny. Vytvoříte kanál s aktivitou uložené procedury v této datové továrně. Uložená aktivita procedury provede uloženou proceduru v databázi SSISDB ke spuštění balíčku SSIS. 

1. Definujte proměnnou pro název skupiny prostředků, kterou použijete později v příkazech PowerShellu. Zkopírujte do PowerShellu následující text příkazu, zadejte název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md) v uvozovkách a pak příkaz spusťte. Například: `"adfrg"`. 
   
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

5. Chcete-li vytvořit datovou továrnu, spusťte následující rutinu **Set-AzDataFactoryV2** pomocí vlastnosti Location a ResourceGroupName z proměnné $ResGrp: 
    
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
Vytvořte propojenou službu, která propojí databázi Azure SQL, která je hostitelem katalogu SSIS, s vaší továrně dat. Data Factory používá informace v této propojené služby pro připojení k databázi SSISDB a provede uloženou proceduru ke spuštění balíčku SSIS. 

1. Vytvořte soubor JSON s názvem **AzureSqlDatabaseLinkedService.json** ve složce **C:\ADF\RunSSISPackage** s následujícím obsahem: 

    > [!IMPORTANT]
    > Před &lt;uložením souboru nahraďte název_serveru&gt;, &lt;uživatelské jméno&gt;a &lt;heslo&gt; hodnotami databáze Azure SQL Database.

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

3. Spusťte rutinu **Set-AzDataFactoryV2LinkedService** a vytvořte propojenou službu: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Vytvoření kanálu s uloženou aktivitou procedury 
V tomto kroku vytvoříte kanál s aktivitou uložené procedury. Aktivita vyvolá sp_executesql uloženou proceduru ke spuštění balíčku SSIS. 

1. Vytvořte soubor JSON s názvem **RunSSISPackagePipeline.json** ve složce **C:\ADF\RunSSPACKAGE** s následujícím obsahem:

    > [!IMPORTANT]
    > Před &lt;uložením souboru nahraďte&gt;název složky &lt;, NÁZEV&gt;PROJEKTU , &lt;NÁZEV BALÍČKU&gt; názvy složek, projektů a balíčků v katalogu SSIS. 

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

2. Chcete-li vytvořit kanál: **RunSSISPackagePipeline**, spusťte rutinu **Set-AzDataFactoryV2Pipeline.**

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
Ke spuštění kanálu použijte rutinu **Invoke-AzDataFactoryV2Pipeline.** Tato rutina vrací ID spuštění kanálu pro budoucí monitorování.

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

### <a name="create-a-trigger"></a>Vytvoření aktivační události
V předchozím kroku jste vyvolali kanál na vyžádání. Můžete také vytvořit aktivační událost plánu pro spuštění kanálu podle plánu (každou hodinu, denně atd.).

1. Vytvořte soubor JSON s názvem **MyTrigger.json** ve složce **C:\ADF\RunSSISPackage** s následujícím obsahem: 

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
2. V **prostředí Azure PowerShell**přepněte do složky **C:\ADF\RunSSISPackage.**
3. Spusťte rutinu **Set-AzDataFactoryV2Trigger,** která vytvoří aktivační událost. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Ve výchozím nastavení je aktivační událost v zastaveném stavu. Spusťte aktivační událost spuštěním rutiny **Start-AzDataFactoryV2Trigger.** 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Zkontrolujte, zda je aktivační událost spuštěna spuštěním rutiny **Get-AzDataFactoryV2Trigger.** 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Spusťte následující příkaz po další hodině. Pokud je například aktuální čas 3:25 PM UTC, spusťte příkaz v 16:00 UTC. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Můžete spustit následující dotaz proti databázi SSISDB na serveru Azure SQL a ověřit, že balíček byl spuštěn. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Další kroky
Můžete také sledovat kanálu pomocí portálu Azure. Podrobné pokyny naleznete v tématu [Sledování kanálu](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
