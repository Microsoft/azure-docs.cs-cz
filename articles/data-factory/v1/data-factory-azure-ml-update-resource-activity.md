---
title: Aktualizace modelů Machine Learning pomocí Azure Data Factory
description: Popisuje, jak vytvořit prediktivní kanály pomocí Azure Data Factory a Azure Machine Learning
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: afc79badd19fa180e631f1f8fa9735567a0b1e33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978709"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Aktualizace modelů Azure Machine Learning pomocí aktivity aktualizačních prostředků

> [!div class="op_single_selector" title1="Transformační aktivity"]
> * [Aktivita úlu](data-factory-hive-activity.md) 
> * [Aktivita prasat](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Aktivita streamování hadoopu](data-factory-hadoop-streaming-activity.md)
> * [Aktivita jiskry](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivita rozhraní .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [téma aktualizace modelů strojového učení v datové továrně](../update-machine-learning-models.md).

Tento článek doplňuje hlavní Azure Data Factory – Azure Machine Learning integrace článek: [Vytvoření prediktivní kanály pomocí Azure Machine Learning a Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Pokud jste tak ještě neučinili, přečtěte si hlavní článek před přečtením tohoto článku. 

## <a name="overview"></a>Přehled
V průběhu času je třeba přeškolit prediktivní modely v experimentech vyhodnocování Azure ML pomocí nových vstupních datových sad. Po dokončení rekvalifikace, chcete aktualizovat vyhodnocování webové služby s retrained ML modelu. Typické kroky umožňující rekvalifikaci a aktualizaci modelů Azure ML prostřednictvím webových služeb jsou:

1. Vytvořte experiment v [Azure Machine Learning Studio (klasické)](https://studio.azureml.net).
2. Až budete s modelem spokojeni, použijte Azure Machine Learning Studio (klasické) k publikování webových služeb pro **trénovací experiment** i vyhodnocování či**prediktivní experiment**.

Následující tabulka popisuje webové služby používané v tomto příkladu.  Podrobnosti najdete [v tématu Programové přeškolovací modely strojového učení.](../../machine-learning/machine-learning-retrain-models-programmatically.md)

- **Školení webová služba** - Přijímá trénovací data a vytváří trénované modely. Výstup retraining je soubor .ilearner v úložišti objektů blob Azure. **Výchozí koncový bod** se automaticky vytvoří při publikování trénovacího experimentu jako webové služby. Můžete vytvořit více koncových bodů, ale v příkladu používá pouze výchozí koncový bod.
- **Vyhodnocování webové služby** – přijímá neoznačené příklady dat a dělá předpovědi. Výstup předpověď může mít různé formuláře, jako je například soubor .csv nebo řádky v databázi Azure SQL, v závislosti na konfiguraci experimentu. Výchozí koncový bod se automaticky vytvoří při publikování prediktivníexperiment jako webové služby. 

Následující obrázek znázorňuje vztah mezi školení a bodování koncových bodů v Azure ML.

![Webové služby](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Můžete vyvolat **školení webové služby** pomocí **aktivity azure ml dávkového spuštění**. Vyvolání trénovací webové služby je stejné jako vyvolání webové služby Azure ML (vyhodnocování webové služby) pro vyhodnocování dat. Předchozí části popisují, jak vyvolat webovou službu Azure ML z kanálu Azure Data Factory podrobně. 

**Webovou službu hodnocení** můžete vyvolat pomocí **aktivity aktualizačních prostředků Azure ML** k aktualizaci webové služby pomocí nově trénovaného modelu. Následující příklady poskytují definice propojených služeb: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Bodování webové služby je klasická webová služba
Pokud je webová služba vyhodnocování **klasickou webovou službou**, vytvořte druhý **nevýchozí a aktualizovatelný koncový bod** pomocí portálu Azure. Postup najdete v článku [Vytvoření koncových bodů.](../../machine-learning/machine-learning-create-endpoint.md) Po vytvoření nevýchozího aktualizovatelného koncového bodu proveďte následující kroky:

* Chcete-li získat hodnotu URI pro vlastnost **mlEndpoint** JSON, klepněte na tlačítko **BATCH EXECUTION.**
* Chcete-li získat hodnotu IDENTIFIKÁTOR URI pro vlastnost **updateResourceEndpoint** JSON, klepněte na tlačítko AKTUALIZOVAT ODKAZ **PROSTŘEDEK.** Klíč rozhraní API je na samotné stránce koncového bodu (v pravém dolním rohu).

![aktualizovatelný koncový bod](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

Následující příklad obsahuje ukázkovou definici JSON pro propojenou službu AzureML. Propojená služba používá apiKey pro ověřování.  

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
Pokud je webová služba nový typ webové služby, která zveřejňuje koncový bod Azure Resource Manager, není nutné přidat druhý **koncový bod, než výchozí.** **UpdateResourceEndpoint** v propojené službě je formátu: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Hodnoty pro držitele míst můžete získat v adrese URL při dotazování webové služby na [portálu Webových služeb Azure Machine Learning](https://services.azureml.net/). Nový typ koncového bodu prostředku aktualizace vyžaduje token AAD (Azure Active Directory). Zadejte **servicePrincipalId** a **servicePrincipalKey** v propojené službě Azure Machine Learning. Podívejte [se, jak vytvořit instanční objekt a přiřadit oprávnění ke správě prostředků Azure](../../active-directory/develop/howto-create-service-principal-portal.md). Tady je ukázka definice propojené služby AzureML: 

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

Následující scénář obsahuje další podrobnosti. Má příklad pro přetrénování a aktualizaci modelů Azure ML z kanálu Azure Data Factory.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scénář: přeškolení a aktualizace modelu Azure ML
Tato část obsahuje ukázkový kanál, který používá **aktivitu azure ml dávkového spuštění** k přeškolení modelu. Kanál také používá **aktivitu azure ml aktualizační prostředek** k aktualizaci modelu ve webové službě vyhodnocování. Část také obsahuje fragmenty JSON pro všechny propojené služby, datové sady a kanál v příkladu.

Zde je zobrazení diagramu ukázkového kanálu. Jak můžete vidět, aktivita dávkového spuštění Azure ML přebírá vstup školení a vytváří tréninkový výstup (soubor iLearner). Aktivita aktualizačních prostředků Azure ML přebírá tento výstup školení a aktualizuje model v koncovém bodě vyhodnocování webové služby. Aktualizace aktivity zdrojů nevytváří žádný výstup. Zástupný objekt Blob je pouze fiktivní výstupní datová sada, kterou služba Azure Data Factory vyžaduje ke spuštění kanálu.

![diagram potrubí](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Propojená služba úložiště objektů blob Azure:
Azure Storage obsahuje následující data:

* údaje o školení. Vstupní data pro webovou službu školení Azure ML.  
* iLearner. Výstup z webové služby školení Azure ML. Tento soubor je také vstupem do aktivity aktualizovat zdroj.  

Zde je ukázka json definice propojené služby:

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

### <a name="training-input-dataset"></a>Trénování vstupní datové sady:
Následující datová sada představuje vstupní trénovací data pro webovou službu školení Azure Machine Learning. Aktivita dávkového spuštění Azure Machine Learning přebírá tuto datovou sadu jako vstup.

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

### <a name="training-output-dataset"></a>Trénování výstupní datové sady:
Následující datová sada představuje výstupní soubor iLearner z webové služby školení Azure ML. Aktivita dávkového spuštění Azure ML vytváří tuto datovou sadu. Tato datová sada je také vstup pro aktivitu azure ml aktualizační prostředky.

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

### <a name="linked-service-for-azure-machine-learning-training-endpoint"></a>Propojený koncový bod školení Azure Machine Learning
Následující fragment JSON definuje propojenou službu Azure Machine Learning, která odkazuje na výchozí koncový bod školicí webové služby.

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

V **Azure Machine Learning Studio (classic)**, postupujte takto, chcete-li získat hodnoty pro **mlEndpoint** a **apiKey**:

1. V levé nabídce klikněte na **WEBOVÉ SLUŽBY.**
2. Klikněte na **webovou službu školení** v seznamu webových služeb.
3. Klikněte na kopírovat vedle textového pole **klíče rozhraní API.** Vložte klíč do schránky do editoru JSON datové továrny.
4. V **Azure Machine Learning Studio (klasické)** klikněte na odkaz **BATCH SPUŠTĚNÍ.**
5. Zkopírujte **identifikátor URI požadavku** z části **Požadavek** a vložte jej do editoru JSON datové továrny.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Koncový bod aktualizovatelného vyhodnocování propojené služby pro Azure ML:
Následující fragment JSON definuje propojenou službu Azure Machine Learning, která odkazuje na nevýchozí aktualizovatelný koncový bod vyhodnocovací webové služby.  

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

### <a name="placeholder-output-dataset"></a>Zástupná výstupní datová sada:
Aktivita aktualizovat prostředek Azure ML negeneruje žádný výstup. Azure Data Factory však vyžaduje výstupní datovou sadu řídit plán kanálu. Proto v tomto příkladu používáme fiktivní/zástupnou datovou sadu.  

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
Kanál má dvě aktivity: **AzureMLBatchExecution** a **AzureMLUpdateResource**. Azure ML Batch Execution aktivita bere trénovací data jako vstup a vytvoří soubor iLearner jako výstup. Aktivita vyvolá trénovací webovou službu (trénovací experiment vystavený jako webová služba) se vstupními trénovacími daty a obdrží soubor ilearner z webové služby. Zástupný objekt Blob je pouze fiktivní výstupní datová sada, kterou služba Azure Data Factory vyžaduje ke spuštění kanálu.

![diagram potrubí](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

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
