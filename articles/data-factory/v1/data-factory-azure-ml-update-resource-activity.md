---
title: Aktualizace modelů Machine Learning pomocí Azure Data Factory | Microsoft Docs
description: Popisuje postup vytvoření vytvořit prediktivní kanály pomocí Azure Data Factory a Azure Machine Learning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3702f4b7a58e9ca65a8ee309699a7e31b207159b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Aktualizace pomocí aktivita prostředku aktualizace modely Azure Machine Learning

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Aktivita Hive](data-factory-hive-activity.md) 
> * [Pig aktivity](data-factory-pig-activity.md)
> * [Činnost MapReduce](data-factory-map-reduce.md)
> * [Streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Spark aktivity](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivity rozhraní .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [aktualizace modelů machine learning v datové továrně verze 2](../update-machine-learning-models.md).

Tento článek doplňuje hlavní Azure Data Factory – článek integrace Azure Machine Learning: [vytvořit prediktivní kanály pomocí Azure Machine Learning a Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Pokud jste tak již neučinili, přečtěte si hlavní článek než si přečtete prostřednictvím tohoto článku. 

## <a name="overview"></a>Přehled
V průběhu času prediktivní modely v Azure ML vyhodnocování experimentů muset být retrained pomocí nové vstupní datové sady. Po dokončení práce s retraining, budete chtít aktualizovat webovou službu vyhodnocování retrained modelu ML. Typické postup povolení retraining a aktualizace Azure ML modely prostřednictvím webových služeb jsou:

1. Vytvoření experimentu v [Azure ML Studio](https://studio.azureml.net).
2. Jakmile budete spokojeni s modelem, používají k publikování webových služeb pro obě Azure ML Studio **výukový experiment** a vyhodnocování /**prediktivní experiment**.

Následující tabulka popisuje webové služby použité v tomto příkladu.  V tématu [Machine Learning Přeučování modelů prostřednictvím kódu programu](../../machine-learning/machine-learning-retrain-models-programmatically.md) podrobnosti.

- **Cvičení webové služby** – přijímá Cvičná data a vytváří trénované modely. Výstup retraining je soubor .ilearner v Azure Blob storage. **Výchozí koncový bod** se automaticky vytvoří pro při publikování školení experimentování jako webovou službu. Můžete vytvořit další koncové body, ale v příkladu se používá pouze výchozí koncový bod.
- **Vyhodnocování webové služby** – bez popisku dat příklady obdrží a umožňuje předpovědi. Výstup předpovědi může mít různé formy, například soubor .csv nebo řádků v Azure SQL database, v závislosti na konfiguraci experimentu. Výchozí koncový bod se automaticky vytvoří za vás, když publikujete prediktivní experiment jako webovou službu. 

Následující obrázek znázorňuje vztahy mezi školení a vyhodnocování koncových bodů v Azure ML.

![Webové služby](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Můžete vyvolat **cvičení webové služby** pomocí **aktivita provedení dávky Azure ML**. Vyvolání webové služby školení je stejný jako vyvolání webové služby Azure ML (vyhodnocování webová služba) pro vyhodnocování data. V předchozích částech zabývá vyvolání webové služby Azure ML z podrobně kanál služby Azure Data Factory. 

Můžete vyvolat **vyhodnocování webové služby** pomocí **aktivita prostředku aktualizace Azure ML** aktualizace webové služby s nově naučeného modelu. Následující příklady obsahují definice propojené služby: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Vyhodnocování webové služby je classic webová služba
Pokud je webová služba vyhodnocování **classic webové služby**, vytvořte druhý **jiné než výchozí a aktualizovat koncový bod** pomocí portálu Azure. V tématu [vytvořit koncové body](../../machine-learning/machine-learning-create-endpoint.md) najdete v článku kroky. Po vytvoření koncového bodu aktualizovat jiné než výchozí, proveďte následující kroky:

* Klikněte na tlačítko **BATCH EXECUTION** získat hodnota identifikátoru URI pro **mlEndpoint** vlastnost JSON.
* Klikněte na tlačítko **aktualizace prostředků** odkaz k získání hodnoty identifikátoru URI pro **updateResourceEndpoint** vlastnost JSON. Klíč rozhraní API je na stránce koncového bodu (v pravém dolním rohu).

![aktualizovat koncový bod](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

Následující příklad uvádí definici JSON ukázka AzureML propojené služby. Propojená služba používá apiKey pro ověřování.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Vyhodnocování webové služby je webová služba Azure Resource Manager 
Pokud webová služba je nový typ webové služby, který zveřejňuje koncový bod Azure Resource Manager, není potřeba přidat druhý **jiné než výchozí** koncový bod. **UpdateResourceEndpoint** v propojené službě je ve formátu: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Můžete získat hodnoty pro zástupného v adrese URL při dotazování na webovou službu [portálu služby Azure Machine Learning webové](https://services.azureml.net/). Nový typ prostředku aktualizace koncového bodu vyžaduje token AAD (Azure Active Directory). Zadejte **servicePrincipalId** a **servicePrincipalKey**v AzureML propojené služby. V tématu [postup vytvoření instančního objektu a přiřaďte oprávnění ke správě prostředků Azure](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Zde je ukázka definice AzureML propojené služby: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

V následujícím scénáři najdete další podrobnosti. Obsahuje příklad retraining a aktualizace Azure ML modely z kanál služby Azure Data Factory.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scénář: retraining a aktualizace model Azure ML
Tato část obsahuje ukázkový kanál, který používá **aktivita provedení dávky Azure ML** k přeučování modelu. Kanál také používá **aktivita prostředku aktualizace Azure ML** k aktualizaci modelu v rámci vyhodnocování webové služby. Tato část taky poskytuje fragmenty kódu JSON pro všechny propojené služby, datové sady a kanál v příkladu.

Zde je zobrazení diagramu ukázkový kanál. Jak můžete vidět, aktivita provedení dávky Azure ML přijímá vstup školení a vytvoří výstupní školení (soubor iLearner). Aktivita prostředku aktualizace Azure ML přebírá tento výstup školení a aktualizuje model v vyhodnocování koncový bod webové služby. Aktivita prostředku aktualizace nevytváří žádný výstup. PlaceholderBlob je právě fiktivní výstupní datovou sadu, která je vyžadovaná službou Azure Data Factory ke spuštění kanálu.

![diagram kanálu](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Propojená služba Azure Blob storage:
Azure Storage obsahuje následující data:

* Cvičná data. Vstupní data pro webovou službu Azure ML školení.  
* reprezentuje soubor iLearner. Výstup z webové služby Azure ML školení. Tento soubor je také vstup aktivita prostředku aktualizace.  

Zde je ukázka definici JSON propojené služby:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Školení vstupní datové sady:
Tyto datové sady představuje vstupní Cvičná data pro webovou službu Azure ML školení. Aktivita provedení dávky Azure ML přijímá tuto datovou sadu jako vstup.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Školení výstupní datovou sadu:
Tyto datové sady představuje výstupní soubor iLearner z webové služby Azure ML školení. Aktivita provedení dávky Azure ML vyprodukuje tuto datovou sadu. Tato datová sada je také vstup aktivita prostředku aktualizace Azure ML.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Propojené služby pro koncový bod Azure ML školení
Následující fragment kódu JSON definuje služby Azure Machine Learning propojené, který ukazuje na výchozí koncový bod webové služby školení.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

V **Azure ML Studio**, proveďte následující kroky k získání hodnot pro **mlEndpoint** a **apiKey**:

1. Klikněte na tlačítko **webové služby** v levé nabídce.
2. Klikněte **cvičení webové služby** v seznamu webové služby.
3. Klikněte na možnost Kopírovat do **klíč rozhraní API** textové pole. Vložte klíč do schránky do editoru Data Factory JSON.
4. V **Azure ML studio**, klikněte na tlačítko **BATCH EXECUTION** odkaz.
5. Kopírování **URI požadavku** z **požadavku** části a vložte ho do editoru Data Factory JSON.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Propojená služba Azure ML aktualizovat vyhodnocování koncového bodu:
Následující fragment kódu JSON definuje služby Azure Machine Learning propojené, která odkazuje na jiné než výchozí koncový bod aktualizovat vyhodnocování webové služby.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Zástupný symbol výstupní datovou sadu:
Aktivita prostředku aktualizace Azure ML negeneruje žádný výstup. Azure Data Factory však vyžaduje výstupní datové sady do jednotky plán kanálu. Proto jsme použít datovou sadu fiktivní/zástupný symbol v tomto příkladu.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Kanál
Kanál má dvě aktivity: **AzureMLBatchExecution** a **AzureMLUpdateResource**. Aktivita provedení dávky Azure ML trvá Cvičná data jako vstup a vytvoří soubor iLearner jako výstup. Aktivity vyvolá webovou službu školení (výukový experiment zveřejněné jako webovou službu) se vstupní Cvičná data a přijímá reprezentuje soubor ilearner z webovou službu. PlaceholderBlob je právě fiktivní výstupní datovou sadu, která je vyžadovaná službou Azure Data Factory ke spuštění kanálu.

![diagram kanálu](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```
