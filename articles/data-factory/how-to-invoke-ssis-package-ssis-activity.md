---
title: Spouštění balíčků služby SSIS s spuštění balíčku aktivity SSIS - Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak spouštět balíčků SQL Server Integration Services (SSIS) v kanálu Azure Data Factory pomocí aktivity spustit balíčků služby SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/16/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: cda439973c584a57cadc30de7fb931732682de00
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092453"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Spouštění balíčků služby SSIS se spuštění aktivity balíčku SSIS v Azure Data Factory
Tento článek popisuje způsob spouštění balíčku služby SSIS v kanálu Azure Data Factory pomocí aktivity spustit balíčků služby SSIS. 

## <a name="prerequisites"></a>Požadavky

**Azure SQL Database**. Názorný postup v tomto článku se používá Azure SQL database, který hostuje katalog služby SSIS. Můžete také použít Azure SQL Database Managed Instance.

## <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime
Vytvořit prostředí Azure-SSIS integration runtime, pokud nemáte podle podrobných pokynů v [kurz: balíčků služby SSIS nasazovat](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Spuštění balíčku na webu Azure Portal
V této části použijete uživatelské rozhraní služby Data Factory k vytvoření kanálu Data Factory s aktivitou spuštění balíčku služby SSIS, na kterém běží balíčku SSIS.

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

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Vytvoření kanálu s aktivitou spuštění balíčku služby SSIS
V tomto kroku použijete uživatelské rozhraní služby Data Factory vytvoříte kanál. Přidat aktivitu spustit balíčků SSIS do tohoto kanálu a jeho spouštění balíčků služby SSIS nakonfigurovat. 

1. Na stránce Začínáme, klikněte na **vytvořit kanál**: 

    ![Stránka Začínáme](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. V **aktivity** sady nástrojů, rozbalte **Obecné**a přetáhněte **spuštění balíčku služby SSIS** aktivity na plochu návrháře kanálu. 

   ![Přetáhněte na plochu návrháře aktivity spuštění balíčku služby SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Na **Obecné** karty vlastností pro aktivity spuštění balíčku služby SSIS, zadejte název a popis aktivity. Nastavit volitelný časový limit a hodnoty opakování.

    ![Nastavte vlastnosti na kartě Obecné](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Na **nastavení** karta Vlastnosti balíčků služby SSIS spuštění aktivity, vyberte přidružené k prostředí Azure-SSIS Integration Runtime `SSISDB` databáze, ve kterém je balíček nasazen. Zadejte cestu k balíčku v `SSISDB` databáze ve formátu `<folder name>/<project name>/<package name>.dtsx`. Volitelně zadat 32-bit spuštění a úroveň předdefinovaným nebo vlastním protokolování a zadejte cestu k prostředí ve formátu `<folder name>/<environment name>`.

    ![Nastavte vlastnosti na kartě nastavení](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. Chcete-li ověřit konfiguraci kanálu, klikněte na tlačítko **ověřit** na panelu nástrojů. Pokud chcete **Sestavu ověření kanálu** zavřít, klikněte na **>>**.

6. Publikování kanálu služby Data Factory kliknutím **Publikovat vše** tlačítko. 

### <a name="optionally-parameterize-the-activity"></a>Volitelně můžete parametrizovat aktivity

Volitelně můžete přiřadíte hodnoty, výrazy nebo funkce, které mohou odkazovat na objekt pro vytváření dat systémové proměnné, do vašeho projektu nebo balíček parametrů ve formátu JSON pomocí tlačítko "Zobrazit zdrojový kód" v dolní části činnost vaše spuštění balíčku služby SSIS nebo "Kód" tlačítko v pravém horním rohu oblasti vašeho kanálu. Můžete například přiřadit parametry kanálu služby Data Factory do projektu služby SSIS nebo balíček parametrů, jak je znázorněno na následujících snímcích obrazovky:

![Upravit skript JSON pro aktivity spuštění balíčku služby SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters.png)

![Přidání parametrů do aktivity spuštění balíčku služby SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters2.png)

![Přidání parametrů do aktivity spuštění balíčku služby SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters2.png)

### <a name="run-the-pipeline"></a>Spuštění kanálu
V této části se aktivuje spuštění kanálu a potom monitorovat. 

1. Aktivace spuštění kanálu, klikněte na tlačítko **aktivační událost** na panelu nástrojů a klikněte na tlačítko **aktivovat**. 

    ![Aktivovat](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. V okně **Spuštění kanálu** vyberte **Dokončit**. 

### <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Vlevo přepněte na kartu **Monitorování**. Se zobrazí spuštění kanálu a jeho stav spolu s dalšími informacemi (jako je čas začátku spuštění). Pokud chcete zobrazení aktualizovat, klikněte na **Aktualizovat**.

    ![Spuštění kanálu](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Zobrazí se pouze jednu aktivitu spustit, protože tento kanál obsahuje pouze jednu aktivitu (aktivita spustit balíčků služby SSIS).

    ![Spuštění aktivit](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Spuštěním následujícího **dotazu** proti databáze SSISDB databáze na serveru služby Azure SQL a ověřte, že balíček provedeny. 

    ```sql
    select * from catalog.executions
    ```

    ![Ověřte spuštění balíčku](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Můžete také získat ID spuštění SSISDB z výstupu aktivity spuštění kanálu a použijte ID komplexnější protokoly spuštění a chybové zprávy v aplikaci SSMS.

    ![Získat ID spuštění.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Naplánovat kanál pomocí triggeru

Můžete také vytvořit naplánovanou aktivační událost pro svůj kanál tak, aby kanál se spouští podle plánu (každou hodinu, každý den atd.). Příklad najdete v tématu [vytvoření datové továrny – uživatelské rozhraní služby Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Spusťte balíček pomocí Powershellu
V této části použijete Azure PowerShell k vytvoření kanálu Data Factory s aktivitou spuštění balíčku služby SSIS, na kterém běží balíčku SSIS. 

Nainstalujte nejnovější moduly Azure PowerShellu podle pokynů v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps). 

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Můžete použít stejné datové továrny, který má Azure-SSIS IR, nebo vytvořit samostatné datové továrny. Následující postup předvádí kroky k vytvoření datové továrny. Vytvoření kanálu s aktivitou spuštění balíčku služby SSIS v této datové továrně. Spuštění aktivit spuštění balíčku služby SSIS balíčku služby SSIS. 

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
* Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:** [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Vytvoření kanálu s aktivitou spuštění balíčku služby SSIS 
V tomto kroku vytvoříte kanál s aktivitou spuštění balíčku služby SSIS. Spuštění aktivit vašeho balíčku služby SSIS. 

1. Vytvořte soubor JSON s názvem **RunSSISPackagePipeline.json** v **C:\ADF\RunSSISPackage** složku s obsahem, podobně jako v následujícím příkladu:

    > [!IMPORTANT]
    > Před uložením souboru nahraďte názvy objektů, popisy a cesty, vlastnosti a hodnoty parametrů, hesla a další hodnoty proměnných. 

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
                    "environmentPath":   "FolderName/EnvironmentName",
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
                        "\\PackageName.dtsx\\MaxConcurrentExecutables ": {
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

2.  V prostředí Azure PowerShell, přepněte `C:\ADF\RunSSISPackage` složky.

3. Chcete-li vytvořit kanál **RunSSISPackagePipeline**, spusťte **Set-AzureRmDataFactoryV2Pipeline** rutiny.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                   -ResourceGroupName $ResGrp.ResourceGroupName `
                                                   -Name "RunSSISPackagePipeline"
                                                   -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Tady je ukázkový výstup:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="run-the-pipeline"></a>Spuštění kanálu
Použití **Invoke-AzureRmDataFactoryV2Pipeline** rutiny pro spuštění kanálu. Tato rutina vrací ID spuštění kanálu pro budoucí monitorování.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorování kanálu

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

Také můžete monitorovat kanál pomocí webu Azure portal. Podrobné pokyny najdete v tématu [monitorování kanálu](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Naplánovat kanál pomocí triggeru
V předchozím kroku jste spustili kanálu na vyžádání. Můžete také vytvořit aktivační událost plánovače pro spuštění kanálu podle plánu (každou hodinu, každý den atd.).

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
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Ve výchozím nastavení trigger je v zastaveném stavu. Spusťte trigger spuštěním **Start-AzureRmDataFactoryV2Trigger** rutiny. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. Potvrďte, že aktivační událost se spouští spuštěním **Get-AzureRmDataFactoryV2Trigger** rutiny. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. Spusťte následující příkaz po do příští hodiny. Například pokud je aktuální čas UTC času 15:25, spusťte příkaz v 16: 00 UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    Spuštěním následujícího dotazu proti databázi SSISDB na serveru služby Azure SQL a ověřte, že balíček provedeny. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Další postup
Najdete v následujícím blogovém příspěvku:
-   [Modernizace a Rozšiřte své pracovní postupy ETL/ELT pomocí služby SSIS aktivit v kanálech ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
