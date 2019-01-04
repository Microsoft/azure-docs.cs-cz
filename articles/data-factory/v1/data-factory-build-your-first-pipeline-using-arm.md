---
title: Sestavení prvního objektu pro vytváření dat (šablona Resource Manageru) | Dokumentace Microsoftu
description: V tomto kurzu vytvoříte ukázkový kanál služby Azure Data Factory pomocí šablony Azure Resource Manageru.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: eb9e70b9-a13a-4a27-8256-2759496be470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: bf39f9d0ea96259aaec14562e302e31781b64231
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023766"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>Kurz: Sestavit svou první datovou továrnu Azure pomocí šablony Azure Resource Manageru
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Šablona Resource Manageru](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
 
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [rychlý start: Vytvoření datové továrny pomocí Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

V tomto článku vytvoříte první objekt pro vytváření dat Azure pomocí šablony Azure Resource Manageru. Pokud chcete udělat kurz pomocí jiných nástrojů nebo sad SDK, vyberte jednu z možností z rozevíracího seznamu.

Kanál v tomto kurzu má jednu aktivitu: **Aktivita HDInsight Hive**. Tato aktivita spouští skript Hive v clusteru Azure HDInsight, který transformuje vstupní data pro vytvoření výstupních dat. Spuštění kanálu je naplánované jednou za měsíc mezi zadaným počátečním a koncovým časem. 

> [!NOTE]
> Datový kanál v tomto kurzu transformuje vstupní data, aby vytvořil výstupní data. Kurz předvádějící způsoby kopírování dat pomocí Azure Data Factory najdete v tématu [kurzu: Kopírování dat z Blob Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> V tomto kurzu kanál obsahuje pouze jednu aktivitu typu: HDInsightHive. Kanál může obsahovat víc než jednu aktivitu. A dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Další informace najdete v tématu [plánování a provádění ve službě Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

## <a name="prerequisites"></a>Požadavky
* Přečtěte si článek [Přehled kurzu](data-factory-build-your-first-pipeline.md) a proveďte **nutné** kroky.
* Podle pokynů v článku [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) si na počítač nainstalujte nejnovější verzi prostředí Azure PowerShell.
* Informace o šablonách Azure Resource Manageru najdete v článku [Vytváření šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md). 

## <a name="in-this-tutorial"></a>V tomto kurzu
| Entita | Popis |
| --- | --- |
| Propojená služba Azure Storage |Propojí účet služby Azure Storage s datovou továrnou. Účet služby Azure Storage v této ukázce obsahuje vstupní a výstupní data pro kanál. |
| Propojená služba HDInsightu na vyžádání |Propojí cluster HDInsight na vyžádání k datové továrně. Cluster se automaticky vytvoří, abyste mohli zpracovat data, a po dokončení zpracování se odstraní. |
| Vstupní datová sada Azure Blob |Odkazuje na propojenou službu Azure Storage. Propojená služba odkazuje na účet služby Azure Storage a datová sada Azure Blob určuje kontejner, složku a název souboru v úložišti, který obsahuje vstupní data. |
| Výstupní datová sada Azure Blob |Odkazuje na propojenou službu Azure Storage. Propojená služba odkazuje na účet služby Azure Storage a datová sada Azure Blob určuje kontejner, složku a název souboru v úložišti, který obsahuje výstupní data. |
| Data Pipeline |Kanál má jednu aktivitu typu HDInsightHive, který využívá vstupní datovou sadu a vytváří výstupní datovou sadu. |

Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál může obsahovat jednu nebo víc aktivit. Existují dva typy aktivit: [aktivity přesunu dat](data-factory-data-movement-activities.md) a [transformace dat](data-factory-data-transformation-activities.md). V tomto kurzu vytvoříte kanál s jednou aktivitou (aktivita Hive).

Následující oddíl poskytuje hotovou šablonu Resource Manageru pro definování entit služby Data Factory, abyste mohli rychle projít kurzem a otestovat šablonu. Pro lepší pochopení toho, jak jsou jednotlivé entity služby Data Factory definovány, přejděte k oddílu [Entity služby Data Factory v šabloně](#data-factory-entities-in-the-template).

## <a name="data-factory-json-template"></a>Šablona JSON služby Data Factory
Šablona Resource Manageru nejvyšší úrovně pro definování datové továrny je: 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
Ve složce **C:\ADFGetStarted** vytvořte soubor JSON s názvem **ADFTutorialARM.json** s následujícím obsahem:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
          "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
          "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
          "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
          "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
    },
    "variables": {
          "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
          "blobInputDatasetName": "AzureBlobInput",
          "blobOutputDatasetName": "AzureBlobOutput",
          "pipelineName": "HiveTransformPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "HDInsightOnDemand",
                  "typeProperties": {
                    "version": "3.5",
                    "clusterSize": 1,
                    "timeToLive": "00:05:00",
                    "osType": "Linux",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
                  }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "fileName": "[parameters('inputBlobName')]",
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  },
                  "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobOutputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  }
            }
          },
          {
            "type": "datapipelines",
            "name": "[variables('pipelineName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]",
                  "[variables('hdInsightOnDemandLinkedServiceName')]",
                  "[variables('blobInputDatasetName')]",
                  "[variables('blobOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "description": "Pipeline that transforms data using Hive script.",
                  "activities": [
                {
                      "type": "HDInsightHive",
                      "typeProperties": {
                        "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                        "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                        "defines": {
                              "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                              "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                        }
                      },
                      "inputs": [
                        {
                              "name": "[variables('blobInputDatasetName')]"
                        }
                      ],
                      "outputs": [
                        {
                              "name": "[variables('blobOutputDatasetName')]"
                        }
                      ],
                      "policy": {
                        "concurrency": 1,
                        "retry": 3
                      },
                      "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                      },
                      "name": "RunSampleHiveActivity",
                      "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                }
                  ],
                  "start": "2017-07-01T00:00:00Z",
                  "end": "2017-07-02T00:00:00Z",
                  "isPaused": false
              }
          }
        ]
      }
    ]
}
```

> [!NOTE]
> Můžete najít další příklad šablony Resource Manageru pro vytvoření služby Azure data factory na [kurzu: Vytvoření kanálu s aktivitou kopírování pomocí šablony Azure Resource Manageru](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md).  
> 
> 

## <a name="parameters-json"></a>Parametry JSON
Vytvořte soubor JSON s názvem **ADFTutorialARM-Parameters.json**, který obsahuje parametry pro šablonu Azure Resource Manageru.  

> [!IMPORTANT]
> Zadejte název a klíč svého účtu služby Azure Storage v parametrech **storageAccountName** a **storageAccountKey** v souboru parametrů. 
> 
> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "value": "<Name of your Azure Storage account>"
        },
        "storageAccountKey": {
            "value": "<Key of your Azure Storage account>"
        },
        "blobContainer": {
            "value": "adfgetstarted"
        },
        "inputBlobFolder": {
            "value": "inputdata"
        },
        "inputBlobName": {
            "value": "input.log"
        },
        "outputBlobFolder": {
            "value": "partitioneddata"
        },
        "hiveScriptFolder": {
              "value": "script"
        },
        "hiveScriptFile": {
              "value": "partitionweblogs.hql"
        }
    }
}
```

> [!IMPORTANT]
> Můžete mít samostatné soubory JSON s parametry pro vývojové, testovací a produkční prostředí, které lze použít se stejnou šablonou JSON služby Data Factory. Pomocí skriptu prostředí PowerShell můžete zautomatizovat nasazování entit služby Data Factory v těchto prostředích. 
> 
> 

## <a name="create-data-factory"></a>Vytvoření objektu pro vytváření dat
1. Otevřete prostředí **Azure PowerShell** a spusťte následující příkaz: 
   * Spusťte následující příkaz a zadejte uživatelské jméno a heslo, které používáte k přihlášení na web Azure Portal.
    ```PowerShell
    Connect-AzureRmAccount
    ```  
   * Spuštěním následujícího příkazu zobrazíte všechna předplatná pro tento účet.
    ```PowerShell
    Get-AzureRmSubscription
    ``` 
   * Spuštěním následujícího příkazu vyberte předplatné, se kterým chcete pracovat. Mělo by to být stejné předplatné, které jste použili na webu Azure Portal.
    ```
    Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
    ```   
2. Spuštěním následujícího příkazu nasaďte entity služby Data Factory pomocí šablony Resource Manageru, kterou jste vytvořili v kroku 1. 

    ```PowerShell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>Monitorování kanálu
1. Po přihlášení na web [Azure Portal](https://portal.azure.com/) klikněte na **Procházet** a vyberte **Objekty pro vytváření dat**.
     ![Procházet -&gt; Datové továrny](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2. V okně **Datové továrny** klikněte na objekt pro vytváření dat (**TutorialFactoryARM**), který jste vytvořili.    
3. V okně **Objekt pro vytváření dat** vašeho objektu pro vytváření dat klikněte na **Diagram**.

     ![Dlaždice Diagram](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4. V **Zobrazení diagramu** uvidíte přehled kanálů a datové sady použité v tomto kurzu.
   
   ![Zobrazení diagramu](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
5. V Zobrazení diagramu dvakrát klikněte na datovou sadu **AzureBlobOutput**. Zobrazí se řez, který se právě zpracovává.
   
    ![Datová sada](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
6. Po dokončení zpracování bude řez ve stavu **Připraveno**. Vytváření clusteru HDInsight na vyžádání většinou nějakou dobu trvá (přibližně 20 minut). Proto počítejte s tím, že zpracování řezu kanálem bude trvat **přibližně 30 minut**.
   
    ![Datová sada](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)    
7. Pokud je řez ve stavu **Připraveno**, zkontrolujte, jestli se ve složce **partitioneddata** v kontejneru **adfgetstarted** ve službě Blob Storage nachází výstupní data.  

Pokyny k použití oken Azure Portal k monitorování kanálu a datových sad, které jste vytvořili v tomto kurzu, najdete v článku [Monitorování datových sad a kanálu](data-factory-monitor-manage-pipelines.md).

K monitorování datových kanálů můžete také použít aplikaci pro monitorování a správu. Podrobnosti o použití této aplikace najdete v článku [Monitorování a správa kanálů služby Azure Data Factory pomocí monitorovací aplikace](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Po úspěšném zpracování řezu se vstupní soubor odstraní. Pokud tedy chcete spustit řez znovu nebo si znovu projít tento kurz, načtěte vstupní soubor (input.log) do složky inputdata v kontejneru adfgetstarted.
> 
> 

## <a name="data-factory-entities-in-the-template"></a>Entity služby Data Factory v šabloně
### <a name="define-data-factory"></a>Definování datové továrny
Datovou továrnu definujete v šabloně Resource Manageru, jak je znázorněno v následující ukázce:  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "West US"
}
```
Hodnota dataFactoryName je definována takto: 

```json
"dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
```
Je to jedinečný řetězec vycházející z ID skupiny prostředků.  

### <a name="defining-data-factory-entities"></a>Definování entit služby Data Factory
V šabloně JSON jsou definovány následující entity služby Data Factory: 

* [Propojená služba Azure Storage](#azure-storage-linked-service)
* [Propojená služba HDInsightu na vyžádání](#hdinsight-on-demand-linked-service)
* [Vstupní datová sada Azure Blob](#azure-blob-input-dataset)
* [Výstupní datová sada Azure Blob](#azure-blob-output-dataset)
* [Data Pipeline s aktivitou kopírování](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
V tomto oddílu zadáte název a klíč svého účtu služby Azure Storage. Podrobnosti o vlastnostech JSON sloužících k definování propojené služby Azure Storage najdete v oddílu [Propojená služba Azure Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service). 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
Vlastnost **connectionString** používá parametry storageAccountName a storageAccountKey. Hodnoty těchto parametrů se předávají pomocí konfiguračního souboru. Definice také používá proměnné azureStorageLinkedService a dataFactoryName definované v šabloně. 

#### <a name="hdinsight-on-demand-linked-service"></a>Propojená služba HDInsightu na vyžádání
Podrobnosti o vlastnostech JSON používaných k definici propojené služby HDInsightu najdete v článku [Propojené služby Compute](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).  

```json
{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
        }
    }
}
```
Je třeba počítat s následujícím: 

* Pomocí výše uvedeného kódu JSON služba Data Factory vytvoří cluster HDInsight **se systémem Linux** za vás. Podrobnosti najdete v tématu [Propojená služba HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
* Místo clusteru HDInsight na vyžádání můžete použít také **vlastní cluster HDInsight**. Podrobnosti najdete v tématu [Propojená služba HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
* Cluster HDInsight vytvoří **výchozí kontejner** ve službě Blob Storage, kterou jste určili v kódu JSON (**linkedServiceName**). Při odstranění clusteru HDInsight neprovede odstranění tohoto kontejneru. Toto chování je záměrné. Díky propojené službě HDInsight na vyžádání se cluster HDInsight vytvoří pokaždé, když je potřeba zpracovat určitý řez, pokud neexistuje aktivní cluster (**timeToLive**), a po dokončení zpracování se zase odstraní.
  
    Po zpracování dalších řezů se vám ve službě Azure Blob Storage objeví spousta kontejnerů. Pokud je nepotřebujete k řešení potíží s úlohami, můžete je odstranit, abyste snížili náklady na úložiště. Názvy těchto kontejnerů používají následující formát: „adf**název_vašeho_objektu_pro_vytváření_dat**-**název_propojené_služby**-razítko_data_a_času“. K odstranění kontejnerů ze služby Azure Blob Storage můžete použít nástroje, jako je třeba [Průzkumník úložišť od Microsoftu](http://storageexplorer.com/).

Podrobnosti najdete v tématu [Propojená služba HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

#### <a name="azure-blob-input-dataset"></a>Vstupní datová sada Azure Blob
Zadáte názvy kontejneru objektů blob, složky a souboru, který obsahuje vstupní data. Podrobnosti o vlastnostech JSON sloužících k definování datové sady Azure Blob najdete v oddílu [Vlastnosti datové sady Azure Blob](data-factory-azure-blob-connector.md#dataset-properties). 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true
    }
}
```
Tato definice používá následující parametry definované v šabloně parametrů: blobContainer, inputBlobFolder a inputBlobName. 

#### <a name="azure-blob-output-dataset"></a>Výstupní datová sada Azure Blob
Určete název kontejneru objektu blob a složku, která obsahuje výstupní data. Podrobnosti o vlastnostech JSON sloužících k definování datové sady Azure Blob najdete v oddílu [Vlastnosti datové sady Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).  

```json
{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        }
    }
}
```

Tato definice používá následující parametry definované v šabloně parametrů: blobContainer a outputBlobFolder. 

#### <a name="data-pipeline"></a>Data Pipeline
Definujete kanál, který převádí data spuštěním skriptu Hive v clusteru Azure HDInsight na vyžádání. Popisy elementů JSON sloužících k definování kanálu v tomto příkladu najdete v oddílu [Kód JSON kanálu](data-factory-create-pipelines.md#pipeline-json). 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('hdInsightOnDemandLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('blobOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "description": "Pipeline that transforms data using Hive script.",
        "activities": [
        {
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                "defines": {
                    "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                    "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                }
            },
            "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
            ],
            "outputs": [
            {
                "name": "[variables('blobOutputDatasetName')]"
            }
            ],
            "policy": {
                "concurrency": 1,
                "retry": 3
            },
            "scheduler": {
                "frequency": "Month",
                "interval": 1
            },
            "name": "RunSampleHiveActivity",
            "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
        }
        ],
        "start": "2017-07-01T00:00:00Z",
        "end": "2017-07-02T00:00:00Z",
        "isPaused": false
    }
}
```

## <a name="reuse-the-template"></a>Znovupoužití šablony
V tomto kurzu jste vytvořili šablonu pro definování entit služby Data Factory a šablonu pro předávání hodnot parametrů. Chcete-li použít stejnou šablonu k nasazení entit služby Data Factory do různých prostředí, vytvořte pro každé prostředí soubor parametrů a použijte jej při nasazování příslušného prostředí.     

Příklad:  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```
Všimněte si, že první příkaz používá soubor parametrů pro vývojové prostředí, druhý příkaz používá soubor parametrů pro testovací prostředí a třetí příkaz používá soubor parametrů pro produkční prostředí.  

Šablonu můžete také znovu použít k provádění opakujících se úloh. Například: Potřebujete vytvořit mnoho datových továren s jedním nebo více kanály, které implementují stejnou logiku, ale každá datová továrna používá jiný účet služby Azure Storage a Azure SQL Database. V tomto scénáři použijete k vytvoření datových továren stejnou šablonu ve stejném prostředí (vývojové, testovací nebo produkční) s různými soubory parametrů. 

## <a name="resource-manager-template-for-creating-a-gateway"></a>Šablona Resource Manageru pro vytvoření brány
Tady je ukázka šablony Resource Manageru pro vytvoření logické brány v pozadí. Nainstalujte bránu na místní počítač nebo virtuální počítač Azure s modelem IaaS a pomocí klíče ji zaregistrujte ve službě Data Factory. Podrobnosti najdete v článku [Přesun dat mezi místním prostředím a cloudem](data-factory-move-data-between-onprem-and-cloud.md).

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
    },
    "variables": {
        "dataFactoryName":  "GatewayUsingArmDF",
        "apiVersion": "2015-10-01",
        "singleQuote": "'"
    },
    "resources": [
        {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "eastus",
            "resources": [
                {
                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                    "type": "gateways",
                    "apiVersion": "[variables('apiVersion')]",
                    "name": "GatewayUsingARM",
                    "properties": {
                        "description": "my gateway"
                    }
                }            
            ]
        }
    ]
}
```
Tato šablona vytvoří objekt pro vytváření dat s názvem gatewayusingarmdf, který má bránu s názvem: GatewayUsingARM. 

## <a name="see-also"></a>Viz také
| Téma | Popis |
|:--- |:--- |
| [Kanály](data-factory-create-pipelines.md) |Tento článek vám pomůže pochopit kanály a aktivity ve službě Azure Data Factory a porozumět tomu, jak se dají ve vaší situaci nebo firmě použít k sestavení kompletních pracovních postupů založených na datech. |
| [Datové sady](data-factory-create-datasets.md) |Tento článek vám pomůže pochopit datové sady ve službě Azure Data Factory. |
| [Plánování a provádění](data-factory-scheduling-and-execution.md) |Tento článek vysvětluje aspekty plánování a provádění aplikačního modelu služby Azure Data Factory. |
| [Monitorování a správa kanálů pomocí monitorovací aplikace](data-factory-monitor-manage-app.md) |Tento článek popisuje, jak monitorovat, spravovat a ladit kanály pomocí aplikace pro monitorování a správu. |

