---
title: Spusťte balíček SSIS s provést SSIS balíček aktivita – Azure | Microsoft Docs
description: Tento článek popisuje způsob spouštění balíčku integrační služby SSIS (SQL Server) v kanál služby Azure Data Factory pomocí aktivity spuštění balíčku služby SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 05/25/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: ffe65260e73aa2daa4ab63840a00076000f8e6a8
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264901"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Spusťte balíček SSIS s spuštění aktivity balíčku služby SSIS v Azure Data Factory
Tento článek popisuje způsob spouštění balíčku služby SSIS v kanál služby Azure Data Factory pomocí spuštění balíčku služby SSIS aktivitu. 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Aktivita provedení balíčku služby SSIS není k dispozici v verze 1 služby Data Factory, který je všeobecně dostupná (GA). Alternativní metoda pro balíček služby SSIS verze 1 služby Data Factory, najdete v části [balíčky SSIS spustit pomocí aktivity uložené procedury v verze 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Požadavky

### <a name="azure-sql-database"></a>Azure SQL Database 
Návod v tomto článku používá databázi Azure SQL, který je hostitelem služby SSIS katalogu. Můžete také použít Azure spravované Instance SQL (Preview).

## <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime
Vytvoření modulu runtime integrace Azure SSIS, pokud nemáte dodržením podrobných pokynů v [kurz: balíčky nasazení SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Uživatelské rozhraní objektu pro vytváření dat (portál Azure)
V této části použijte uživatelské rozhraní objektu pro vytváření dat vytvořit objekt pro vytváření dat kanál s spuštění balíčku služby SSIS aktivitu, která spustí balíček služby SSIS.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Prvním krokem je pro vytváření dat pomocí portálu Azure. 

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
      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
    Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
4. Jako **verzi** vyberte **V2 (Preview)**.
5. Vyberte **umístění** pro objekt pro vytváření dat. V rozevíracím seznamu se zobrazí pouze umístění podporovaná službou Data Factory. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou můžou být v jiných umístěních.
6. Zaškrtněte **Připnout na řídicí panel**.     
7. Klikněte na možnost **Vytvořit**.
8. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
    ![Domovská stránka objektu pro vytváření dat](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě aplikaci uživatelského rozhraní služby Azure Data Factory. 

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Vytvoření kanálu s aktivitou spuštění balíčku služby SSIS
V tomto kroku použijete rozhraní Data Factory vytvořit kanál. Přidejte aktivitu, spuštění balíčku služby SSIS do kanálu a nakonfigurovat jej pro spuštění balíčku služby SSIS. 

1. Na stránku Začínáme, klikněte na **vytvořit kanál**: 

    ![Stránka Začínáme](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. V **aktivity** sada nástrojů, rozbalte položku **Obecné**a přetáhněte **spuštění balíčku služby SSIS** aktivity na plochu návrháře kanálu. 

   ![Přetáhněte aktivitu SSIS na plochu návrháře](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Na **Obecné** kartě vlastností pro spuštění balíčku služby SSIS aktivitu, zadejte název a popis pro aktivitu. Nastavte volitelné vypršení časového limitu a opakujte hodnoty.

    ![Nastavit vlastnosti na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Na **nastavení** vlastností pro spuštění balíčku služby SSIS aktivitu, vyberte modul Runtime integrace Azure SSIS přidružené `SSISDB` databáze, kde je balíček nasazen. Zadejte cestu k balíčku v `SSISDB` databáze ve formátu `<folder name>/<project name>/<package name>.dtsx`. Volitelně zadejte provádění 32bitová verze a úroveň předdefinované nebo vlastní protokolování a zadat cestu ve formátu prostředí `<folder name>/<environment name>`.

    ![Nastavit vlastnosti na kartě nastavení](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. Chcete-li ověřit konfiguraci kanálu, klikněte na tlačítko **ověřením** na panelu nástrojů. Pokud chcete **Sestavu ověření kanálu** zavřít, klikněte na **>>**.

6. Publikování kanálu pro vytváření dat kliknutím **publikovat všechny** tlačítko. 

### <a name="optionally-parameterize-the-activity"></a>Volitelně Parametrizace aktivity

Volitelně můžete přiřadit hodnoty, výrazy nebo funkce, které mohou odkazovat na objekt pro vytváření dat systému proměnné, do projektu nebo balíček parametry ve formátu JSON na **Upřesnit** kartě. Například můžete přiřadit parametry kanálu pro vytváření dat do projektu služby SSIS nebo balíček parametry, jak je znázorněno na následujícím snímku obrazovky:

![Přidání parametrů ke spuštění balíčku služby SSIS aktivity](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters.png)

### <a name="run-and-monitor-the-pipeline"></a>Spuštění a monitorování kanálu
V této části se aktivuje spuštění kanálu a jeho sledování. 

1. Chcete-li aktivovat kanálu spustit, klikněte na tlačítko **aktivační událost** na panelu nástrojů a klikněte na tlačítko **nyní spustit**. 

    ![Aktivovat](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. V okně **Spuštění kanálu** vyberte **Dokončit**. 

3. Vlevo přepněte na kartu **Monitorování**. Zobrazí kanálu spustit a její stav se společně s dalšími informacemi (například čas spuštění spuštění). Pokud chcete zobrazení aktualizovat, klikněte na **Aktualizovat**.

    ![Spuštění kanálu](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

4. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí jenom jedna aktivita spustit, protože kanál obsahuje pouze jednu aktivitu (aktivita spuštění balíčku služby SSIS).

    ![Spuštění aktivit](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

5. Můžete spustit následující **dotazu** proti SSISDB databáze serveru Azure SQL ověřit, jestli balíček provést. 

    ```sql
    select * from catalog.executions
    ```

    ![Ověření spuštění balíčku](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

6. Můžete také získat ID spuštění SSISDB z výstupu spuštění aktivity kanálu a pomocí ID zkontrolujte komplexnější protokoly spuštění a chybové zprávy v aplikaci SSMS.

    ![Získání ID spuštění.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

> [!NOTE]
> Můžete také vytvořit naplánované aktivační událost pro svůj kanál tak, aby kanál spouští podle plánu (hodinový, denní, atd.). Příklad, naleznete v části [vytvořit objekt pro vytváření dat – uživatelské rozhraní objektu pro vytváření dat](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell
V této části použijte prostředí Azure PowerShell k vytvoření kanálu pro vytváření dat s SSIS aktivitu, která spustí balíček služby SSIS. 

Nainstalujte nejnovější moduly Azure PowerShellu podle pokynů v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps). 

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Můžete buď použijte stejné data factory, který má IR Azure SSIS nebo vytvoření samostatné data factory. Následující postup popisuje kroky k vytvoření služby data factory. Vytvoření kanálu s aktivitou SSIS v této datové továrně. Spuštění služby SSIS aktivity vašeho balíčku služby SSIS. 

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
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
                                            -Location $ResGrp.Location `
                                            -Name $dataFactoryName 
    ```

Je třeba počítat s následujícím:

* Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název a zkuste to znovu.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Pro vytvoření instancí Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem rolí **přispěvatel** nebo **vlastník** nebo **správcem** předplatného Azure.
* V současné době Data Factory verze 2 můžete vytvořit datové továrny pouze ve východní USA, východ US2, západní Evropa a oblasti jihovýchodní Asie. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.

### <a name="create-a-pipeline-with-an-ssis-activity"></a>Vytvoření kanálu s aktivitou SSIS 
V tomto kroku vytvoříte kanál s aktivitou SSIS. Spuštění aktivity vašeho balíčku služby SSIS. 

1. Vytvořte soubor JSON s názvem **RunSSISPackagePipeline.json** v **C:\ADF\RunSSISPackage** složku s obsahem, podobně jako v následujícím příkladu:

    > [!IMPORTANT]
    > Před uložením souboru nahraďte názvy objektů, popisy a cesty, vlastnosti a hodnoty parametrů, hesla a jiné hodnoty proměnné. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [{
                "name": "mySSISActivity",
                "description": "My SSIS package/activity description",
                "type": "ExecuteSSISPackage",
                "typeProperties": {
                    "connectVia": {
                        "referenceName": "myAzureSSISIR",
                        "type": "IntegrationRuntimeReference"
                    },
                    "runtime": "x64",
                    "loggingLevel": "Basic",
                    "packageLocation": {
                        "packagePath": "FolderName/ProjectName/PackageName.dtsx"            
                    },
                    "environmentPath":   "FolderName/EnvironmentName",
                    "projectParameters": {
                        "project_param_1": {
                            "value": "123"
                        }
                    },
                    "packageParameters": {
                        "package_param_1": {
                            "value": "345"
                        }
                    },
                    "projectConnectionManagers": {
                        "MyAdonetCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "abc"
                                }
                            }
                        }
                    },
                    "packageConnectionManagers": {
                        "MyOledbCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "def"
                                }
                            }
                        }
                    },
                    "propertyOverrides": {
                        "\\PackageName.dtsx\\MaxConcurrentExecutables ": {
                            "value": 8,
                            "isSensitive": false
                        }
                    }
                },
                "policy": {
                    "timeout": "0.01:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30
                }
            }]
        }
    }
    ```

2.  V prostředí Azure PowerShell přepnout `C:\ADF\RunSSISPackage` složky.

3. K vytvoření kanálu **RunSSISPackagePipeline**spusťte **Set-AzureRmDataFactoryV2Pipeline** rutiny.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                   -ResourceGroupName $ResGrp.ResourceGroupName `
                                                   -Name "RunSSISPackagePipeline"
                                                   -DefinitionFile ".\RunSSISPackagePipeline.json"
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
Použití **Invoke-AzureRmDataFactoryV2Pipeline** můžete spustit kanál. Tato rutina vrací ID spuštění kanálu pro budoucí monitorování.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

Spusťte následující skript PowerShellu, který bude nepřetržitě kontrolovat stav spuštění kanálu, dokud nedokončí kopírování dat. Zkopírujte/vložte následující skript v okně PowerShellu a stiskněte klávesu Enter. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

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

Také můžete monitorovat kanál pomocí portálu Azure. Podrobné pokyny najdete v tématu [kanál monitorovat](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="create-a-trigger"></a>Vytvořit aktivační událost
V předchozím kroku jste spustili kanálu na vyžádání. Můžete také vytvořit aktivační událost plán chcete-li kanál spouštět podle plánu (hodinový, denní, atd.).

1. Vytvořte soubor JSON s názvem **MyTrigger.json** v **C:\ADF\RunSSISPackage** složku s následujícím obsahem: 

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
2. V **prostředí Azure PowerShell**, přepnout **C:\ADF\RunSSISPackage** složky.
3. Spustit **Set-AzureRmDataFactoryV2Trigger** rutiny, které vytvoří aktivační událost. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Ve výchozím nastavení aktivační událost je v zastaveném stavu. Spusťte aktivační událost spuštěním **Start-AzureRmDataFactoryV2Trigger** rutiny. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. Zkontrolujte, zda je spuštěná aktivační událost spuštěním **Get-AzureRmDataFactoryV2Trigger** rutiny. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. Spusťte následující příkaz po do příští hodiny. Například pokud je aktuální čas UTC 3:25 hodin, spusťte příkaz na 16: 00 UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    Můžete spustit následující dotaz na databázi SSISDB v serveru Azure SQL ověřit, jestli balíček provést. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Další postup
Najdete v příspěvku blogu:
-   [Modernizovat a rozšířit vaše pracovní postupy ETL/ELT s aktivitami SSIS v ADF kanálů](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
