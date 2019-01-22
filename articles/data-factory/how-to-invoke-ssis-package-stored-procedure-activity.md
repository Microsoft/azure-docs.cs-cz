---
title: Spouštění balíčků služby SSIS pomocí aktivity uložené procedury – Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak spustit balíček SQL Server Integration Services (SSIS) v kanálu Azure Data Factory pomocí aktivity uložených procedur.
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
ms.date: 04/17/2018
ms.author: jingwang
ms.openlocfilehash: f4148f3afc0cde7beeef8cbe09bd0abce8732e3a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424400"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Spouštění balíčků služby SSIS pomocí aktivity uložené procedury ve službě Azure Data Factory
Tento článek popisuje, jak spustit balíčku SSIS pomocí aktivity uložených procedur v kanálu Azure Data Factory. 

## <a name="prerequisites"></a>Požadavky

### <a name="azure-sql-database"></a>Azure SQL Database 
Názorný postup v tomto článku se používá Azure SQL database, který hostuje katalog služby SSIS. Můžete také použít Azure SQL Database Managed Instance.

## <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime
Pokud nemáte podle podrobných pokynů v, vytvořte prostředí Azure-SSIS integration runtime [kurzu: Nasazení balíčků SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Uživatelské rozhraní služby Data Factory (portál Azure portal)
V této části použijete uživatelské rozhraní služby Data Factory vytvoříte kanál služby Data Factory s aktivitou uložené procedury, která volá balíčku SSIS.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Prvním krokem je vytvoření datové továrny pomocí webu Azure portal. 

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. Přejděte na [Azure Portal](https://portal.azure.com). 
3. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Datová továrna](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na stránce **Nová datová továrna** jako **název** zadejte **ADFTutorialDataFactory**. 
      
     ![Stránka Nová datová továrna](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Název datové továrny Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba pole názvu, změňte název datové továrny (například na vaše_jméno_ADFTutorialDataFactory). Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
     ![Název není dostupný – chyba](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
4. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
    Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
4. Jako **verzi** vyberte **V2**.
5. Vyberte **umístění** pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze umístění podporovaná službou Data Factory. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou můžou být v jiných umístěních.
6. Zaškrtněte **Připnout na řídicí panel**.     
7. Klikněte na možnost **Vytvořit**.
8. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
    ![Domovská stránka datové továrny](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě aplikaci uživatelského rozhraní služby Azure Data Factory. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Vytvoření kanálu s aktivitou uložené procedury
V tomto kroku použijete uživatelské rozhraní služby Data Factory vytvoříte kanál. Přidat aktivitu uložené procedury do kanálu a nakonfigurovat jej pro spuštění balíčku služby SSIS pomocí sp_executesql uložené procedury. 

1. Na stránce Začínáme, klikněte na **vytvořit kanál**: 

    ![Stránka Začínáme](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. V **aktivity** sady nástrojů, rozbalte **Obecné**a přetáhněte **uložená procedura** aktivity na plochu návrháře kanálu. 

    ![Aktivita uložená procedura přetažení myší](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. V okně vlastnosti aktivity uložených procedur, přepněte **účet SQL** kartu a klikněte na tlačítko **+ nová**. Vytvoření připojení k databázi Azure SQL, který hostuje katalog služby SSIS (databázi SSIDB). 
   
    ![Tlačítko Nová propojená služba](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. V okně **Nová propojená služba** proveďte následující kroky: 

    1. Vyberte **Azure SQL Database** pro **typ**.
    2. Vyberte **výchozí** prostředí Azure Integration Runtime připojit ke službě Azure SQL Database, který je hostitelem `SSISDB` databáze.
    3. Vyberte databázi SQL Azure, který je hostitelem databáze SSISDB pro **název serveru** pole.
    4. Vyberte **SSISDB** pro **název_databáze**.
    5. Pro **uživatelské jméno**, zadejte jméno uživatele, který má přístup k databázi.
    6. Pro **heslo**, zadejte heslo uživatele. 
    7. Otestujte připojení k databázi kliknutím **Test připojení** tlačítko.
    8. Propojenou službu uložit kliknutím **Uložit** tlačítko. 

        ![Propojená služba Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. V okně Vlastnosti přepněte **uložená procedura** karty **účet SQL** kartu, a proveďte následující kroky: 

    1. Vyberte **Upravit**. 
    2. Pro **název uložené procedury** pole, zadejte `sp_executesql`. 
    3. Klikněte na tlačítko **+ nová** v **parametry uložené procedury** oddílu. 
    4. Pro **název** parametru zadejte **příkazu Insert**. 
    5. Pro **typ** parametru zadejte **řetězec**. 
    6. Pro **hodnotu** parametru, zadejte následující dotaz SQL:

        V dotazu SQL, zadejte správné hodnoty **název_složky**, **project_name**, a **název_balíčku** parametry. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Propojená služba Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Chcete-li ověřit konfiguraci kanálu, klikněte na tlačítko **ověřit** na panelu nástrojů. Pokud chcete **Sestavu ověření kanálu** zavřít, klikněte na **>>**.

    ![Ověření kanálu](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Publikování kanálu služby Data Factory kliknutím **Publikovat vše** tlačítko. 

    ![Publikování](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Spuštění a monitorování kanálu
V této části se aktivuje spuštění kanálu a potom monitorovat. 

1. Aktivace spuštění kanálu, klikněte na tlačítko **aktivační událost** na panelu nástrojů a klikněte na tlačítko **aktivovat**. 

    ![Aktivovat](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. V okně **Spuštění kanálu** vyberte **Dokončit**. 
3. Vlevo přepněte na kartu **Monitorování**. Se zobrazí spuštění kanálu a jeho stav spolu s dalšími informacemi (jako je čas začátku spuštění). Pokud chcete zobrazení aktualizovat, klikněte na **Aktualizovat**.

    ![Spuštění kanálu](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí se pouze jednu aktivitu spustit, protože tento kanál obsahuje pouze jednu aktivitu (aktivita uložené procedury).

    ![Spuštění aktivit](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Spuštěním následujícího **dotazu** proti databáze SSISDB databáze na serveru služby Azure SQL a ověřte, že balíček provedeny. 

    ```sql
    select * from catalog.executions
    ```

    ![Ověřte spuštění balíčku](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Můžete také vytvořit naplánovanou aktivační událost pro svůj kanál tak, aby kanál se spouští podle plánu (každou hodinu, každý den atd.). Příklad najdete v tématu [vytvoření datové továrny – uživatelské rozhraní služby Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell
V této části použijete Azure PowerShell k vytvoření kanálu Data Factory s aktivitou uložené procedury, která volá balíčku SSIS. 

Nainstalujte nejnovější moduly Azure PowerShellu podle pokynů v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/azurerm/install-azurerm-ps). 

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Můžete použít stejné datové továrny, který má Azure-SSIS IR, nebo vytvořit samostatné datové továrny. Následující postup předvádí kroky k vytvoření datové továrny. Vytvoření kanálu s aktivitou uložené procedury v této datové továrně. Aktivity uložených procedur spouští uloženou proceduru v databázi SSISDB pro spuštění vašeho balíčku služby SSIS. 

1. Definujte proměnnou pro název skupiny prostředků, kterou použijete později v příkazech PowerShellu. Zkopírujte do PowerShellu následující text příkazu, zadejte název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) v uvozovkách a pak příkaz spusťte. Například: `"adfrg"`. 
   
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

5. Pokud chcete vytvořit datovou továrnu, spusťte následující rutinu **Set-AzureRmDataFactoryV2** s použitím vlastností Location a ResourceGroupName z proměnné $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Je třeba počítat s následujícím:

* Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název a zkuste to znovu.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Pro vytvoření instancí Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem rolí **přispěvatel** nebo **vlastník** nebo **správcem** předplatného Azure.
* Seznam oblastí Azure, ve kterých je momentálně dostupná Data Factory, vyberte oblasti, které vás zajímají na následující stránce a potom rozbalte **Analytics** najít **služby Data Factory**: [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.

### <a name="create-an-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database
Vytvořte propojenou službu, která propojí vaši databázi Azure SQL, který hostuje katalog služby SSIS se svou datovou továrnou. Data Factory používá pro připojení k databázi SSISDB informace v této propojené službě a spouští uloženou proceduru pro spuštění balíčku SSIS. 

1. Vytvořte soubor JSON s názvem **AzureSqlDatabaseLinkedService.json** v **C:\ADF\RunSSISPackage** složka s následujícím obsahem: 

    > [!IMPORTANT]
    > Nahraďte &lt;servername&gt;, &lt;uživatelské jméno&gt;, a &lt;heslo&gt; hodnotami pro Azure SQL Database před uložením tohoto souboru.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. V **prostředí Azure PowerShell**, přepněte **C:\ADF\RunSSISPackage** složky.

3. Spustit **Set-AzureRmDataFactoryV2LinkedService** rutina pro vytvoření propojené služby: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Vytvoření kanálu s aktivitou uložené procedury 
V tomto kroku vytvoříte kanál s aktivitou uložené procedury. Aktivita vyvolá sp_executesql uložené procedury pro spuštění vašeho balíčku služby SSIS. 

1. Vytvořte soubor JSON s názvem **RunSSISPackagePipeline.json** v **C:\ADF\RunSSISPackage** složka s následujícím obsahem:

    > [!IMPORTANT]
    > Nahraďte &lt;název složky&gt;, &lt;název projektu&gt;, &lt;název balíčku&gt; složky, projektu a balíčku v katalogu služby SSIS před uložením tohoto souboru. 

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

2. Pokud chcete vytvořit kanál: **RunSSISPackagePipeline**, spusťte **Set-AzureRmDataFactoryV2Pipeline** rutiny.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Tady je ukázkový výstup:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Vytvoření spuštění kanálu
Použití **Invoke-AzureRmDataFactoryV2Pipeline** rutiny pro spuštění kanálu. Tato rutina vrací ID spuštění kanálu pro budoucí monitorování.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

Spusťte následující skript PowerShellu, který bude nepřetržitě kontrolovat stav spuštění kanálu, dokud nedokončí kopírování dat. Zkopírujte/vložte následující skript v okně PowerShellu a stiskněte klávesu Enter. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

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
V předchozím kroku kdybyste vyvolali kanálu na vyžádání. Můžete také vytvořit aktivační událost plánovače pro spuštění kanálu podle plánu (každou hodinu, každý den atd.).

1. Vytvořte soubor JSON s názvem **MyTrigger.json** v **C:\ADF\RunSSISPackage** složka s následujícím obsahem: 

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
2. V **prostředí Azure PowerShell**, přepněte **C:\ADF\RunSSISPackage** složky.
3. Spustit **Set-AzureRmDataFactoryV2Trigger** rutiny, která vytvoří aktivační událost. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Ve výchozím nastavení trigger je v zastaveném stavu. Spusťte trigger spuštěním **Start-AzureRmDataFactoryV2Trigger** rutiny. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Potvrďte, že aktivační událost se spouští spuštěním **Get-AzureRmDataFactoryV2Trigger** rutiny. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Spusťte následující příkaz po do příští hodiny. Například pokud je aktuální čas UTC času 15:25, spusťte příkaz v 16: 00 UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Spuštěním následujícího dotazu proti databázi SSISDB na serveru služby Azure SQL a ověřte, že balíček provedeny. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Další postup
Také můžete monitorovat kanál pomocí webu Azure portal. Podrobné pokyny najdete v tématu [monitorování kanálu](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
