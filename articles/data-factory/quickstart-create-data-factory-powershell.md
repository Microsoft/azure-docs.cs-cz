---
title: Kopírování dat v Blob Storage pomocí Azure Data Factory | Microsoft Docs
description: Vytvořte datovou továrnu Azure ke zkopírování dat z jednoho umístění v úložišti objektů blob v Azure do jiného.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 17be4668bacaf94b0421f6cce83dfcf98f302f60
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38237742"
---
# <a name="create-an-azure-data-factory-using-powershell"></a>Vytvoření datové továrny Azure pomocí PowerShellu 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuální verze](quickstart-create-data-factory-powershell.md)

Tento rychlý start popisuje použití PowerShellu k vytvoření datové továrny Azure. Kanál, který vytvoříte v této datové továrně, **kopíruje** data z jedné složky do jiné složky v úložišti objektů blob Azure. Kurz předvádějící způsoby **transformace** dat pomocí Azure Data Factory najdete v tématu [Kurz: Transformace dat pomocí Sparku](transform-data-using-spark.md). 

> [!NOTE]
> Tento článek neposkytuje podrobný úvod do služby Data Factory. Úvod do služby Azure Data Factory najdete v tématu [Úvod do Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="azure-powershell"></a>Azure PowerShell
Nainstalujte nejnovější moduly Azure PowerShellu podle pokynů v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).

#### <a name="log-in-to-powershell"></a>Přihlášení do PowerShellu

1. Spusťte na svém počítači **PowerShell**. Nechte PowerShell otevřený až do konce tohoto rychlého startu. Pokud ho zavřete a znovu otevřete, tyto příkazy bude potřeba znovu spustit.
2. Spusťte následující příkaz a zadejte uživatelské jméno a heslo Azure, které používáte k přihlášení na Azure Portal:
       
    ```powershell
    Connect-AzureRmAccount
    ```        
3. Spuštěním následujícího příkazu zobrazíte všechna předplatná pro tento účet:

    ```powershell
    Get-AzureRmSubscription
    ```
4. Pokud se zobrazí několik předplatných přidružených vašem účtu, spuštěním následujícího příkazu vyberte předplatné, se kterým chcete pracovat. Místo **SubscriptionId** použijte ID vašeho předplatného Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
1. Definujte proměnnou pro název skupiny prostředků, kterou použijete později v příkazech PowerShellu. Zkopírujte do PowerShellu následující text příkazu, zadejte název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) v uvozovkách a pak příkaz spusťte. Například: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    Pokud již skupina prostředků existuje, nepřepisujte ji. Přiřaďte proměnné `$ResourceGroupName` jinou hodnotu a spusťte tento příkaz znovu.
2. Pokud chcete vytvořit skupinu prostředků Azure, spusťte následující příkaz: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'East US'
    ``` 
    Pokud již skupina prostředků existuje, nepřepisujte ji. Přiřaďte proměnné `$ResourceGroupName` jinou hodnotu a spusťte tento příkaz znovu. 
3. Definujte proměnnou název datové továrny. 

    > [!IMPORTANT]
    >  Aktualizujte název datové továrny tak, aby byl globálně jedinečný. Například ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
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
* Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:** [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.

## <a name="create-a-linked-service"></a>Vytvoření propojené služby

V datové továrně vytvořte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V tomto rychlém startu vytvoříte propojenou službu Azure Storage, která slouží jako zdroj i úložiště jímky. Tato propojená služba má informace o připojení, které služba Data Factory používá pro připojení za běhu.

1. Ve složce **C:\ADFv2QuickStartPSH** vytvořte soubor JSON s názvem **AzureStorageLinkedService.json** s následujícím obsahem: (pokud ještě neexistuje, složku ADFv2QuickStartPSH vytvořte). 

    > [!IMPORTANT]
    > Než soubor uložíte, položky &lt;accountName&gt; a &lt;accountKey&gt; nahraďte názvem svého účtu úložiště Azure a jeho klíčem.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
    Pokud používáte Poznámkový blok, v dialogovém okně **Uložit jako** v poli **Uložit jako typ** vyberte **Všechny soubory**. Jinak se k souboru může přidat přípona `.txt`. Například, `AzureStorageLinkedService.json.txt`. Pokud soubor před otevřením v Poznámkovém bloku vytvoříte v Průzkumníku souborů, přípona `.txt` se možná nezobrazí, protože ve výchozím nastavení je nastavená možnost **Skrýt příponu souborů známých typů**. Než budete pokračovat k dalšímu kroku, odeberte příponu `.txt`.
2. V **PowerShellu** přejděte do složky **ADFv2QuickStartPSH**.

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```
3. Spuštěním rutiny **Set-AzureRmDataFactoryV2LinkedService** vytvořte propojenou službu **AzureStorageLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Tady je ukázkový výstup:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>Vytvoření datové sady
V tomto kroku nadefinujete datovou sadu, která představuje data ke kopírování ze zdroje do jímky. Tato datová sada je typu **AzureBlob**. Odkazuje na **propojenou službu Azure Storage**, kterou jste vytvořili v předchozím kroku. Na základě parametru sestaví vlastnost **folderPath**. Pro vstupní datovou sadu aktivita kopírování v kanálu předá vstupní cestu jako hodnotu tohoto parametru. Podobně pro výstupní datovou sadu aktivita kopírování předá výstupní cestu jako hodnotu tohoto parametru. 

1. Ve složce **C:\ADFv2QuickStartPSH** vytvořte soubor JSON s názvem **BlobDataset.json** s následujícím obsahem:

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "@{dataset().path}"
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Spuštěním rutiny **Set-AzureRmDataFactoryV2Dataset** vytvořte datovou sadu **BlobDataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    Tady je ukázkový výstup:

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Vytvoření kanálu
  
V tomto rychlém startu vytvoříte kanál s jednou aktivitou, který přebírá dva parametry – cestu ke vstupnímu objektu blob a cestu k výstupnímu objektu blob. Hodnoty pro tyto parametry se nastaví při aktivaci nebo spuštění kanálu. Aktivita kopírování používá stejnou datovou sadu objektů blob, kterou jste vytvořili v předchozím kroku jako vstup a výstup. Když se tato datová sada použije jako vstupní, zadá se vstupní cesta. A když se tato datová sada použije jako výstupní, zadá se výstupní cesta. 

1. Ve složce **C:\ADFv2QuickStartPSH** vytvořte soubor JSON s názvem **Adfv2QuickStartPipeline.json** s následujícím obsahem:

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Spuštěním rutiny **Set-AzureRmDataFactoryV2Pipeline** vytvořte kanál **Adfv2QuickStartPipeline**.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

    Tady je ukázkový výstup:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>Vytvoření spuštění kanálu

V tomto kroku nastavíte hodnoty pro parametry kanálu **inputPath** a **outputPath** na skutečné cesty k objektům blob zdroje a jímky. Potom s použitím těchto argumentů vytvoříte spuštění kanálu. 

1. Ve složce **C:\ADFv2QuickStartPSH** vytvořte soubor JSON s názvem **PipelineParameters.json** s následujícím obsahem:

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. Spuštěním rutiny **Invoke-AzureRmDataFactoryV2Pipeline** vytvořte spuštění kanálu a předejte mu hodnoty parametrů. Tato rutina vrací ID spuštění kanálu pro budoucí monitorování.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Spusťte následující skript PowerShellu, který bude nepřetržitě kontrolovat stav spuštění kanálu, dokud nedokončí kopírování dat. Zkopírujte/vložte následující skript v okně PowerShellu a stiskněte klávesu Enter. 

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

    Tady je ukázkový výstup spuštění kanálu:

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

    Pokud se zobrazí následující chyba:
    ```
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```
    Proveďte následující kroky: 
    1. V AzureStorageLinkedService.json potvrďte, že název a klíč účtu Azure Storage Account jsou správné. 
    2. Ověřte správnost formátu připojovacího řetězce. Vlastnosti, například AccountName a AccountKey, jsou oddělené znakem středníku (`;`). 
    3. Pokud máte název účtu a klíč účtu v lomených závorkách, odeberte je. 
    4. Tady je příklad připojovacího řetězce: 

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageacountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```
    5. Znovu vytvořte propojenou službu pomocí postupu v části [Vytvoření propojené služby](#create-a-linked-service). 
    6. Znovu spusťte kanál pomocí postupu v části [Vytvoření spuštění kanálu](#create-a-pipeline-run). 
    7. Znovu spusťte aktuální monitorovací příkaz k monitorování nového spuštění kanálu. 
1. Spusťte následující skript, který načte podrobnosti o spuštění aktivity kopírování, například velikost načtených/zapsaných dat.

    ```powershell
    Write-Output "Activity run details:"
    $Result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
    ```
3. Zkontrolujte, jestli se zobrazí výstup podobný následujícímu ukázkovému výstupu výsledku spuštění aktivity:

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedDataIntegrationUnits": 2
    "billedDuration": 14
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)] 

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob v Azure. Projděte si [kurzy](tutorial-copy-data-dot-net.md), kde se dozvíte o použití služby Data Factory ve více scénářích. 
