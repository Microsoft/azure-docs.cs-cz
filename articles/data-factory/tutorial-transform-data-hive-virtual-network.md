---
title: Transformace dat pomocí Hivu ve službě Azure Virtual Network | Dokumentace Microsoftu
description: Tento kurz obsahuje podrobné pokyny pro transformaci dat pomocí aktivity Hivu v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: shengcmsft
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shengc
ms.openlocfilehash: fda9cab53290d7af69e243ce47df702b25d1de67
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Transformace dat ve službě Azure Virtual Network pomocí aktivity Hivu v Azure Data Factory
V tomto kurzu použijete Azure PowerShell k vytvoření kanálu datové továrny, který transformuje data pomocí aktivity Hivu v clusteru HDInsight, který je ve službě Azure Virtual Network. V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny 
> * Vytvoření a nastavení modulu runtime integrace v místním prostředí
> * Vytvoření a nasazení propojených služeb
> * Vytvoření a nasazení kanálu který obsahuje aktivitu Hivu
> * Zahájení spuštění kanálu
> * Monitorování spuštění kanálu 
> * Ověření výstupu 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si [dokumentaci služby Data Factory verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
- **Účet služby Azure Storage**. Vytvoříte skript Hivu a uložíte ho do úložiště Azure. Výstup ze skriptu Hivu je uložený v tomto účtu úložiště. V této ukázce clusteru HDInsight používá tento účet služby Azure Storage jako primární úložiště. 
- **Virtuální síť Azure**. Pokud nemáte virtuální síť Azure, vytvořte ji pomocí [těchto pokynů](../virtual-network/quick-create-portal.md). V této ukázce je HDInsight ve službě Azure Virtual Network. Tady je ukázka konfigurace služby Azure Virtual Network. 

    ![Vytvoření virtuální sítě](media/tutorial-transform-data-using-hive-in-vnet/create-virtual-network.png)
- **Cluster HDInsight**. Vytvořte cluster HDInsight a připojte ho k virtuální síti, kterou jste vytvořili v předchozím kroku, a na základě informací v článku věnovaném [rozšíření Azure HDInsightu s využitím služby Azure Virtual Network](../hdinsight/hdinsight-extend-hadoop-virtual-network.md). Tady je ukázka konfigurace HDInsightu ve virtuální síti. 

    ![HDInsight ve virtuální síti](media/tutorial-transform-data-using-hive-in-vnet/hdinsight-in-vnet-configuration.png)
- **Azure PowerShell**. Postupujte podle pokynů v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-azurerm-ps).

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Uložení skriptu Hivu do vašeho účtu služby Blob Storage

1. Vytvořte soubor SQL Hivu s názvem **hivescript.hql** a s následujícím obsahem:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Ve službě Azure Blob Storage, vytvořte kontejner **adftutorial**, pokud ještě neexistuje.
3. Vytvořte složku s názvem **hivescripts**.
4. Uložte soubor **hivescript.hql** do podsložky **hivescripts**.

 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny


1. Nastavte název skupiny prostředků. Skupinu prostředků vytvoříte v rámci tohoto kurzu. Pokud ale chcete, můžete použít existující skupinu prostředků. 

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```
2. Zadejte název datové továrny. Musí být globálně jedinečný.

    ```powershell
    $dataFactoryName = "MyDataFactory09142017"
    ```
3. Zadejte název pro kanál. 

    ```powershell
    $pipelineName = "MyHivePipeline" # 
    ```
4. Zadejte název pro místní prostředí Integration Runtime. Místní prostředí Integration Runtime potřebujete, pokud služba Data Factory vyžaduje přístup k prostředkům uvnitř virtuální sítě (jako je Azure SQL Database). 
    ```powershell
    $selfHostedIntegrationRuntimeName = "MySelfHostedIR09142017" 
    ```
2. Spusťte **PowerShell**. Nechte prostředí Azure PowerShell otevřené až do konce tohoto kurzu Rychlý start. Pokud ho zavřete a znovu otevřete, bude potřeba tyto příkazy spustit znovu. Data Factory V2 v současné době umožňuje vytváření datových továren jenom v oblastech Východní USA, Východní USA 2 a Západní Evropa. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.

    Spusťte následující příkaz a zadejte uživatelské jméno a heslo, které používáte k přihlášení na web Azure Portal:
        
    ```powershell
    Login-AzureRmAccount
    ```        
    Spuštěním následujícího příkazu zobrazíte všechna předplatná pro tento účet:

    ```powershell
    Get-AzureRmSubscription
    ```
    Spuštěním následujícího příkazu vyberte předplatné, se kterým chcete pracovat. Místo **SubscriptionId** použijte ID vašeho předplatného Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. Vytvořte skupinu prostředků ADFTutorialResourceGroup, pokud ještě ve vašem předplatném neexistuje. 

    ```powershell
    New-AzureRmResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Vytvořte datovou továrnu. 

    ```powershell
     $df = Set-AzureRmDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Spusťte následující příkaz pro zobrazení výstupu: 

    ```powershell
    $df
    ```

## <a name="create-self-hosted-ir"></a>Vytvoření IR v místním prostředí
V této části vytvoříte modul runtime integrace v místním prostředí a přidružíte ho k virtuálnímu počítači Azure ve stejné službě Azure Virtual Network, ve které je váš cluster HDInsight.

1. Vytvořte modul runtime integrace v místním prostředí. Pokud už existuje jiný modul runtime integrace se stejným názvem, použijte jedinečný název.

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted
   ```
    Tento příkaz vytvoří logickou registraci modulu runtime integrace v místním prostředí. 
2. Pomocí PowerShellu načtěte ověřovací klíče pro registraci modulu runtime integrace v místním prostředí. Jeden z těchto klíčů zkopírujte pro registraci místního prostředí Integration Runtime.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName | ConvertTo-Json
   ```

   Zde je ukázkový výstup: 

   ```powershell
   {
       "AuthKey1":  "IR@0000000000000000000000000000000000000=",
       "AuthKey2":  "IR@0000000000000000000000000000000000000="
   }
   ```
    Poznamenejte si hodnotu **AuthKey1** bez uvozovek. 
3. Vytvořte virtuální počítač Azure a připojte ho do stejné virtuální sítě, která obsahuje příslušný cluster HDInsight. Podrobnosti najdete v tématu věnovaném [postupu při vytváření virtuálních počítačů](../virtual-network/quick-create-portal.md#create-virtual-machines). Připojte ho k virtuální síti Azure. 
4. Ve virtuálním počítači Azure stáhněte [modul runtime integrace v místním prostředí](https://www.microsoft.com/download/details.aspx?id=39717). Použijte ověřovací klíč získaný v předchozím kroku a tento modul runtime integrace v místním prostředí ručně zaregistrujte. 

   ![Registrace modulu runtime integrace](media/tutorial-transform-data-using-hive-in-vnet/register-integration-runtime.png)

   Po úspěšném dokončení registrace modulu runtime integrace v místním prostředí se zobrazí zpráva o ![úspěšné registraci](media/tutorial-transform-data-using-hive-in-vnet/registered-successfully.png).

   Jakmile se uzel připojí ke cloudové službě, zobrazí se stránka ![Node is connected](media/tutorial-transform-data-using-hive-in-vnet/node-is-connected.png) (Uzel se připojil).

## <a name="author-linked-services"></a>Vytvoření propojených služeb

V této části vytvoříte a nasadíte dvě propojené služby:
- Propojená služba Azure Storage, která propojí váš účet služby Azure Storage s datovou továrnou. Toto úložiště používá cluster HDInsight jako primární. V tomto případě používáme tento účet Azure Storage také k uložení skriptu Hivu a výstup tohoto skriptu.
- Propojená služba HDInsight. Azure Data Factory odešle skript Hivu tomuto clusteru HDInsight ke spuštění.

### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage

Pomocí preferovaného editoru vytvořte soubor JSON, zkopírujte následující definici JSON propojené služby Azure Storage a potom tento soubor uložte jako **MyStorageLinkedService.json**.

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }  
    }
}
```

Položky **&lt;accountname&gt; a &lt;accountkey&gt;** nahraďte názvem svého účtu Azure Storage a jeho klíčem.

### <a name="hdinsight-linked-service"></a>Propojená služba HDInsight

Pomocí preferovaného editoru vytvořte soubor JSON, zkopírujte následující definici JSON propojené služby Azure HDInsight a potom tento soubor uložte jako **MyHDInsightLinkedService.json**.

```
{
  "name": "MyHDInsightLinkedService",
  "properties": {     
      "type": "HDInsight",
      "typeProperties": {
          "clusterUri": "https://<clustername>.azurehdinsight.net",
          "userName": "<username>",
          "password": {
            "value": "<password>",
            "type": "SecureString"
          },
          "linkedServiceName": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }
  }
}
```

V definici propojené služby aktualizujte hodnoty následujících vlastností:

- **userName**. Uživatelské jméno pro přihlášení clusteru, které jste zadali při vytváření clusteru. 
- **password**. Heslo pro tohoto uživatele.
- **clusterUri**. Zadejte adresu URL clusteru HDInsight v následujícím formátu: https://<clustername>.azurehdinsight.net.  V tomto článku se předpokládá, že máte ke clusteru přístup přes internet. To znamená, že se ke clusteru můžete připojit třeba na `https://clustername.azurehdinsight.net`. Tato adresa se používá veřejnou brány, která není dostupná, pokud jste k omezení přístupu z internetu použili skupiny zabezpečení sítě (NSG) nebo uživatelem definované trasy (UDR). Aby služba Data Factory mohla odesílat úlohy do clusterů HDInsight ve službě Azure Virtual Network, musíte ji nakonfigurovat tak, aby tuto adresu URL bylo možné přeložit na privátní IP adresu brány, kterou používá HDInsight.

  1. Na webu Azure Portal otevřete službu Virtual Network, ve které je HDInsight. Otevřete síťové rozhraní s názvem začínajícím textem `nic-gateway-0`. Poznamenejte si jeho privátní IP adresu. Příklad: 10.6.0.15. 
  2. Pokud Azure Virtual Network má server DNS, aktualizujte záznam DNS tak, aby se adresa URL clusteru HDInsight `https://<clustername>.azurehdinsight.net` dala přeložit na `10.6.0.15`. Toto je doporučený postup. Pokud ve službě Azure Virtual Network nemáte server DNS, můžete to dočasně obejít tak, že upravíte soubor hosts (C:\Windows\System32\drivers\etc) všech virtuálních počítačů, které se registrovaly jako uzly místního prostředí Integration Runtime, a to přidáním položky jako je tato: 
  
        `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V PowerShellu přejděte do složky, ve které jste vytvořili soubory JSON, a spusťte následující příkaz pro nasazení propojených služeb: 

1. V PowerShellu přejděte do složky, ve které jste vytvořili soubory JSON.
2. Spuštěním následujícího příkazu vytvořte propojenou službu Azure Storage. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
3. Spuštěním následujícího příkazu vytvořte propojenou službu Azure HDInsight. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyHDInsightLinkedService" -File "MyHDInsightLinkedService.json"
    ```

## <a name="author-a-pipeline"></a>Vytvoření kanálu
V tomto kroku pomocí aktivity Hivu vytvoříte nový kanál. Tato aktivity spustí skript Hivu, který vrátí data z ukázkové tabulky a uloží je do cesty, které jste definovali. Pomocí preferovaného editoru vytvořte soubor JSON, zkopírujte následující definici JSON kanálu a potom tento soubor uložte jako **MyHivePipeline.json**.


```json
{
  "name": "MyHivePipeline",
  "properties": {
    "activities": [
      {
        "name": "MyHiveActivity",
        "type": "HDInsightHive",
        "linkedServiceName": {
            "referenceName": "MyHDILinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "scriptPath": "adftutorial\\hivescripts\\hivescript.hql",
          "getDebugInfo": "Failure",
          "defines": {           
            "Output": "wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/"
          },
          "scriptLinkedService": {
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

- Parametr **scriptPath** odkazuje na cestu ke skriptu Hivu v účtu Azure Storage, který jste použili pro MyStorageLinkedService. V této cestě se rozlišují velká a malá písmena.
- **Output** je argument použitý ve skriptu Hivu. Při zadávání odkazu na existující složku ve službě Azure Storage použijte formát `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`. V této cestě se rozlišují velká a malá písmena. 

Přejděte do složky, ve které jste vytvořili soubory JSON, a spusťte následující příkaz pro nasazení kanálu: 


```powershell
Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MyHivePipeline.json"
```

## <a name="start-the-pipeline"></a>Spuštění kanálu 

1. Zahajte spuštění kanálu. Zaznamená se také ID spuštění kanálu pro budoucí monitorování.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
   ```
2. Spusťte následující skript, který bude nepřetržitě kontrolovat stav spuštění kanálu, dokud neskončí.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

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

   Tady je výstup tohoto ukázkového spuštění:

    ```json
    Pipeline run status: In Progress
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 000000000-0000-0000-000000000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            :
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    :
    DurationInMs      :
    Status            : InProgress
    Error             :
    
    Pipeline ' MyHivePipeline' run finished. Result:
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 0000000-0000-0000-0000-000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            : {logLocation, clusterInUse, jobId, ExecutionProgress...}
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    : 9/18/2017 6:59:16 AM
    DurationInMs      : 63636
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "logLocation": "wasbs://adfjobs@adfv2samplestor.blob.core.windows.net/HiveQueryJobs/000000000-0000-47c3-9b28-1cdc7f3f2ba2/18_09_2017_06_58_18_023/Status"
    "clusterInUse": "https://adfv2HivePrivate.azurehdinsight.net"
    "jobId": "job_1505387997356_0024"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "MySelfhostedIR"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MyHiveActivity"
    ```
4. Zkontrolujte složku `outputfolder`. Měla by obsahovat nový soubor vytvořený jako výsledek dotazu Hivu, který vypadá podobně jako následující ukázkový výstup: 

   ```
   8 en-US SCH-i500 California
   23 en-US Incredible Pennsylvania
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   246 en-US SCH-i500 District Of Columbia
   246 en-US SCH-i500 District Of Columbia
   ```

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Vytvoření datové továrny 
> * Vytvoření a nastavení modulu runtime integrace v místním prostředí
> * Vytvoření a nasazení propojených služeb
> * Vytvoření a nasazení kanálu který obsahuje aktivitu Hivu
> * Zahájení spuštění kanálu
> * Monitorování spuštění kanálu 
> * Ověření výstupu 

Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Větvení a řetězení tok řízení Data Factory](tutorial-control-flow.md)



