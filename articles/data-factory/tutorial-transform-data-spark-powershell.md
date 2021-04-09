---
title: 'Transformace dat pomocí Sparku v Azure Data Factory '
description: Tento kurz obsahuje podrobné pokyny pro transformaci dat pomocí aktivity Sparku ve službě Azure Data Factory.
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
author: nabhishek
ms.author: abnarain
ms.openlocfilehash: cf425ecb1a8aebf594828bfba50e9aba8ca9dd7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377614"
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Transformace dat v cloudu pomocí aktivity Sparku ve službě Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto kurzu použijete Azure PowerShell k vytvoření kanálu Data Factory, který transformuje data pomocí aktivity Sparku a propojené služby HDInsight na vyžádání. V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny 
> * Vytvoření a nasazení propojených služeb
> * Vytvoření a nasazení kanálu 
> * Zahajte spuštění kanálu.
> * Monitorování spuštění kanálu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Účet Azure Storage**. Vytvoříte skript Pythonu a vstupní soubor a nahrajete je do úložiště Azure. V tomto účtu úložiště se ukládá výstup z programu Sparku. Cluster Spark na vyžádání používá stejný účet úložiště jako primární úložiště.  
* **Azure PowerShell.** Postupujte podle pokynů v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-Az-ps).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Uložení skriptu Pythonu do účtu služby Blob Storage
1. Vytvořte soubor Pythonu s názvem **WordCount_Spark. py** s následujícím obsahem: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Nahraďte **&lt; storageAccountName &gt;** názvem vašeho účtu Azure Storage. Pak soubor uložte. 
3. Ve službě Azure Blob Storage, vytvořte kontejner nazvaný **adftutorial**, pokud ještě neexistuje. 
4. Vytvořte složku **spark**.
5. Ve složce **spark** vytvořte podsložku **script**. 
6. Do podsložky **script** uložte soubor **WordCount_Spark.py**. 


### <a name="upload-the-input-file"></a>Nahrání vstupního souboru
1. Vytvořte soubor **minecraftstory.txt** a nějakým textem. Program Sparku spočítá slova v tomto textu. 
2. Ve složce `spark` vytvořte podsložku `inputfiles`. 
3. Do podsložky `inputfiles` uložte soubor `minecraftstory.txt`. 

## <a name="author-linked-services"></a>Vytvoření propojených služeb
V této části vytvoříte dvě propojené služby: 
    
- Propojená služba Azure Storage, která propojí váš účet služby Azure Storage s datovou továrnou. Toto úložiště používá cluster HDInsight na vyžádání. Obsahuje také skript Sparku, který se má spustit. 
- Propojená služba HDInsight na vyžádání. Azure Data Factory automaticky vytvoří cluster HDInsight, spustí program Sparku a pak odstraní cluster HDInsight, jakmile bude nečinný po předkonfigurovanou dobu. 

### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Pomocí preferovaného editoru vytvořte soubor JSON, zkopírujte do něj následující definici JSON propojené služby Azure Storage a potom tento soubor uložte jako **MyStorageLinkedService.json**.  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>"
      }
    }
}
```
Aktualizujte &lt;storageAccountName&gt; a &lt;storageAccountKey&gt; s použitím názvu a klíče vašeho účtu služby Azure Storage. 


### <a name="on-demand-hdinsight-linked-service"></a>Propojená služba HDInsight na vyžádání
Pomocí preferovaného editoru vytvořte soubor JSON, zkopírujte do něj následující definici JSON propojené služby Azure HDInsight a potom tento soubor uložte jako **MyOnDemandSparkLinkedService.json**.  

```json
{
    "name": "MyOnDemandSparkLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
        "clusterSize": 2,
        "clusterType": "spark",
        "timeToLive": "00:15:00",
        "hostSubscriptionId": "<subscriptionID> ",
        "servicePrincipalId": "<servicePrincipalID>",
        "servicePrincipalKey": {
          "value": "<servicePrincipalKey>",
          "type": "SecureString"
        },
        "tenant": "<tenant ID>",
        "clusterResourceGroup": "<resourceGroupofHDICluster>",
        "version": "3.6",
        "osType": "Linux",
        "clusterNamePrefix":"ADFSparkSample",
        "linkedServiceName": {
          "referenceName": "MyStorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }
}
```
V definici propojené služby aktualizujte hodnoty následujících vlastností: 

- **hostSubscriptionId**. Místo &lt;subscriptionID&gt; použijte ID vašeho předplatného Azure. Cluster HDInsight na vyžádání se vytvoří v tomto předplatném. 
- **tenant**. Místo &lt;tenantID&gt; použijte ID vašeho tenanta Azure. 
- **servicePrincipalId**, **servicePrincipalKey**. Místo &lt;servicePrincipalID&gt; a &lt;servicePrincipalKey&gt; použijte ID a klíč instančního objektu vaší služby v Azure Active Directory. Tento instanční objekt musí být členem role přispěvatele předplatného nebo skupiny prostředků, ve které se cluster vytvoří. Podrobnosti najdete v tématu [Vytvoření aplikace Azure Active Directory a instančního objektu](../active-directory/develop/howto-create-service-principal-portal.md). **ID instančního objektu** je ekvivalentní *ID aplikace* a **klíč instančního objektu** je ekvivalentní hodnotě pro *tajný klíč klienta*.
- **clusterResourceGroup**. Nahraďte &lt;resourceGroupOfHDICluster&gt; názvem skupiny prostředků, ve které se má cluster HDInsight vytvořit. 

> [!NOTE]
> Pro Azure HDInsight platí omezení celkového počtu jader, která můžete v jednotlivých podporovaných oblastech Azure použít. V případě propojené služby HDInsight na vyžádání se cluster HDInsight vytvoří ve stejném umístění jako služba Azure Storage, kterou používá jako primární úložiště. Ujistěte se, že máte dostatečné kvóty pro jádra, aby bylo možné cluster úspěšně vytvořit. Další informace najdete v tématu [Nastavení clusterů v HDInsight se systémem Hadoop, Spark, Kafka a dalšími](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 


## <a name="author-a-pipeline"></a>Vytvoření kanálu 
V tomto kroku vytvoříte nový kanál s aktivitou Sparku. Aktivita používá ukázku **word count** (počet slov). Pokud jste to ještě neudělali, stáhněte obsah z tohoto umístění.

Pomocí preferovaného editoru vytvořte soubor JSON, zkopírujte následující definici JSON kanálu a potom tento soubor uložte jako **MySparkOnDemandPipeline.json**. 

```json
{
  "name": "MySparkOnDemandPipeline",
  "properties": {
    "activities": [
      {
        "name": "MySparkActivity",
        "type": "HDInsightSpark",
        "linkedServiceName": {
            "referenceName": "MyOnDemandSparkLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "rootPath": "adftutorial/spark",
          "entryFilePath": "script/WordCount_Spark.py",
          "getDebugInfo": "Failure",
          "sparkJobLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}
```

Je třeba počítat s následujícím: 

- rootPath odkazuje na složku spark kontejneru adftutorial. 
- entryFilePath odkazuje na soubor WordCount_Spark.py v podsložce script složky spark. 


## <a name="create-a-data-factory"></a>Vytvoření datové továrny 
Vytvořili jste definice propojené služby a kanálu v souborech JSON. Teď vytvoříme datovou továrnu a nasadíme soubory JSON propojené služby a kanálu pomocí rutin PowerShellu. Postupně spusťte následující příkazy PowerShellu: 

1. Nastavte proměnné jednu po druhé.

    **Název skupiny prostředků**
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```

    **Název Data Factory Musí být globálně jedinečný.** 
    ```powershell
    $dataFactoryName = "MyDataFactory09102017"
    ```
    
    **Název kanálu**
    ```powershell
    $pipelineName = "MySparkOnDemandPipeline" # Name of the pipeline
    ```
2. Spusťte **PowerShell**. Nechte prostředí Azure PowerShell otevřené až do konce tohoto kurzu Rychlý start. Pokud ho zavřete a znovu otevřete, bude potřeba tyto příkazy spustit znovu. Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:**[Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.

    Spusťte následující příkaz a zadejte uživatelské jméno a heslo, které používáte k přihlášení na web Azure Portal:
        
    ```powershell
    Connect-AzAccount
    ```        
    Spuštěním následujícího příkazu zobrazíte všechna předplatná pro tento účet:

    ```powershell
    Get-AzSubscription
    ```
    Spuštěním následujícího příkazu vyberte předplatné, se kterým chcete pracovat. Nahraďte **SubscriptionId** číslem ID vašeho předplatného Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. Vytvořte skupinu prostředků ADFTutorialResourceGroup. 

    ```powershell
    New-AzResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Vytvořte datovou továrnu. 

    ```powershell
     $df = Set-AzDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Spusťte následující příkaz pro zobrazení výstupu: 

    ```powershell
    $df
    ```
5. Přejděte do složky, ve které jste vytvořili soubory JSON, a spuštěním následujícího příkazu nasaďte propojenou službu Azure Storage: 
       
    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
6. Spuštěním následujícího příkazu nasaďte propojenou službu Sparku na vyžádání: 
       
    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyOnDemandSparkLinkedService" -File "MyOnDemandSparkLinkedService.json"
    ```
7. Spuštěním následujícího příkazu nasaďte kanál: 
       
    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MySparkOnDemandPipeline.json"
    ```
    
## <a name="start-and-monitor-a-pipeline-run"></a>Spuštění kanálu a jeho monitorování  

1. Zahajte spuštění kanálu. Zaznamená se také ID spuštění kanálu pro budoucí monitorování.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName  
    ```
2. Spusťte následující skript, který bude nepřetržitě kontrolovat stav spuštění kanálu, dokud neskončí.

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    
        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }

    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n" 
    ```  
3. Zde je výstup tohoto ukázkového spuštění: 

    ```
    Pipeline run status: In Progress
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : 
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : 
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 
    DurationInMs      : 
    Status            : InProgress
    Error             :
    …
    
    Pipeline ' MySparkOnDemandPipeline' run finished. Result:
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : MyDataFactory09102017
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : {clusterInUse, jobId, ExecutionProgress, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 9/20/2017 6:46:30 AM
    DurationInMs      : 763466
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "clusterInUse": "https://ADFSparkSamplexxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurehdinsight.net/"
    "jobId": "0"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MySparkActivity"
    ```
4. Pomocí výstupu z programu Sparku potvrďte vytvoření složky `outputfiles` ve složce `spark` kontejneru adftutorial. 


## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob Azure. Naučili jste se: 

> [!div class="checklist"]
> * Vytvoření datové továrny 
> * Vytvoření a nasazení propojených služeb
> * Vytvoření a nasazení kanálu 
> * Zahajte spuštění kanálu.
> * Monitorování spuštění kanálu

Pokud chcete zjistit, jak transformovat data spuštěním skriptu Hivu v clusteru Azure HDInsight ve virtuální síti, přejděte k následujícímu kurzu. 

> [!div class="nextstepaction"]
> [Kurz: transformace dat pomocí podregistru v Azure Virtual Network](tutorial-transform-data-hive-virtual-network.md).





