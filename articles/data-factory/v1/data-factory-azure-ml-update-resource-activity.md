---
title: Aktualizace modelů Machine Learning pomocí Azure Data Factory | Dokumentace Microsoftu
description: Popisuje, jak vytvořit vytváření prediktivních kanálů pomocí služby Azure Data Factory a Azure Machine Learning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0c0e0e3983344bba76f5f305ecaf73f91110f3bc
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020077"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Aktualizace modelů Azure Machine Learning pomocí aktivity aktualizace prostředku

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Aktivita hivu](data-factory-hive-activity.md) 
> * [Aktivita pig](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktivita Spark](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivita .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [aktualizace modelů strojového učení ve službě Data Factory](../update-machine-learning-models.md).

Tento článek doplňuje hlavní Azure Data Factory – článek integrace Azure Machine Learning: [Vytváření prediktivních kanálů pomocí Azure Machine Learning a Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Pokud jste tak již neučinili, přečtěte si v hlavním článku před přečtení tohoto článku. 

## <a name="overview"></a>Přehled
V průběhu času prediktivních modelů v Azure ML hodnocení experimentů musí být retrained pomocí nové vstupní datové sady. Jakmile budete hotovi s přetrénování, budete chtít aktualizovat hodnoticí webové služby s modelem retrained ML. Typický postup povolení retraining a aktualizace modelů Azure ML prostřednictvím webové služby jsou:

1. Vytvoření experimentu v [Azure ML Studio](https://studio.azureml.net).
2. Pokud jste spokojeni s modelem, pomocí Azure ML Studio publikovat webové služby pro obě **výukového experimentu** a vyhodnocování /**prediktivní experiment**.

Následující tabulka popisuje webové služby, který je použitý v tomto příkladu.  Zobrazit [modelů Machine Learning Přeučování](../../machine-learning/machine-learning-retrain-models-programmatically.md) podrobnosti.

- **Školení webová služba** – obdrží trénovacích dat a vytvoří trénované modely. Výstup přetrénování je soubor .ilearner ve službě Azure Blob storage. **Výchozí koncový bod** se automaticky vytvoří pro vás, když publikujete na školení experiment jako webové služby. Můžete vytvořit další koncové body, ale v příkladu se používá pouze výchozí koncový bod.
- **Webová služba vyhodnocování** – obdrží příkladů bez popisku dat a vytváří předpovědi. Výstup předpovědí, může mít různé podoby, jako například soubor .csv nebo řádků v databázi Azure SQL, v závislosti na konfiguraci testu. Výchozí koncový bod se automaticky vytvoří za vás, když publikujete prediktivní experiment jako webové služby. 

Následující obrázek znázorňuje vztah mezi trénování a vyhodnocování koncových bodů v Azure ML.

![Webové služby](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Můžete vyvolat **školení webová služba** pomocí **aktivita provedení dávky služby Azure ML**. Vyvolání webové služby školení je stejná jako u volání webové služby Azure ML (bodování webová služba) pro vyhodnocení data. Předchozí části se věnují vyvolání webové služby Azure ML z kanálu služby Azure Data Factory podrobně. 

Můžete vyvolat **vyhodnocování webová služba** pomocí **aktivita prostředku aktualizace Azure ML** jak aktualizovat webovou službu s nově trénovaného modelu. Následující příklady popisují definice propojené služby: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Vyhodnocování webová služba je klasickou webovou službou
Pokud je webová služba pro vyhodnocení **klasickou webovou službou**, vytvořte druhý **nevýchozí a aktualizovat koncový bod** pomocí webu Azure portal. Zobrazit [vytvořit koncové body](../../machine-learning/machine-learning-create-endpoint.md) kde najdete kroky. Po vytvoření aktualizovat koncový bod jiné než výchozí, proveďte následující kroky:

* Klikněte na tlačítko **BATCH EXECUTION** má být získána hodnota identifikátoru URI pro **mlEndpoint** vlastnost JSON.
* Klikněte na tlačítko **aktualizace prostředku** odkaz k získání hodnoty identifikátoru URI pro **updateResourceEndpoint** vlastnost JSON. Klíč rozhraní API se na stránce koncového bodu (v pravém dolním rohu).

![aktualizovat koncový bod](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

Následující příklad uvádí ukázková definice JSON pro službu Azure ml propojený. Propojená služba používá apiKey pro ověřování.  

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

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Vyhodnocování webové služby je webová služba v Azure Resource Manageru 
Pokud webová služba je nový typ webová služba, která zpřístupňuje koncový bod Azure Resource Manageru, není potřeba přidat druhý **nevýchozí** koncového bodu. **UpdateResourceEndpoint** v propojené službě je ve formátu: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Můžete získat hodnoty místo zástupné znaky v adrese URL při dotazování na webovou službu [Azure Machine Learning Web Services portálu](https://services.azureml.net/). Nový typ koncový bod aktualizace prostředku vyžaduje token AAD (Azure Active Directory). Zadejte **servicePrincipalId** a **servicePrincipalKey**ve službě Azure ml propojenou službu. Zobrazit [vytvoření instančního objektu a přiřazení oprávnění ke správě prostředků Azure](../../active-directory/develop/howto-create-service-principal-portal.md). Tady je ukázková definice AzureML propojené služby: 

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

V následujícím scénáři najdete další podrobnosti. Obsahuje příklad přetrénování a aktualizace modelů Azure ML z kanálu služby Azure Data Factory.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scénář: přetrénování a aktualizace modelu Azure ML
Tato část obsahuje ukázkový kanál, který používá **aktivita provedení dávky Azure ML** k přeučování modelu. Kanál také používá **aktivita prostředku aktualizace Azure ML** k aktualizaci modelu v hodnoticí webové služby. V části také poskytuje fragmentů JSON propojené služby, datové sady a kanál v tomto příkladu.

Tady je zobrazení diagramu ukázkový kanál. Jak je vidět, aktivita provedení dávky služby ML Azure přijímá vstup školení a vytváří výstup trénovacích (reprezentuje soubor iLearner). Aktivita prostředků aktualizace ML Azure přebírá tento výstup školení a aktualizace modelu v bodovací koncový bod webové služby. Aktivita prostředků aktualizace nevytváří žádný výstup. PlaceholderBlob je jenom fiktivní výstupní datovou sadu, která požaduje služba Azure Data Factory ke spuštění kanálu.

![diagram kanálu](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Propojená služba Azure Blob storage:
Azure Storage obsahuje následující data:

* Cvičná data. Vstupní data webové služby Azure ML školení.  
* reprezentuje soubor iLearner. Výstup z webové služby Azure ML školení. Tento soubor je také vstup do aktivity aktualizace prostředku.  

Tady je ukázková definice JSON propojené služby:

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
Následující datová sada reprezentuje vstupní trénovacích dat webové služby Azure ML školení. Aktivita provedení dávky služby Azure ML přijímá jako vstup této datové sadě.

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
Následující datová sada reprezentuje soubor iLearner, který výstup z webové služby Azure ML školení. Aktivita provedení dávky služby ML Azure vytvoří tuto datovou sadu. Tato datová sada je také vstup do aktivita prostředku aktualizace Azure ML.

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

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Propojené služby pro koncový bod školení Azure ML
Následující fragment kódu JSON definuje služby Azure Machine Learning propojený, který odkazuje na výchozí koncový bod webové služby, školení.

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

V **Azure ML Studio**, proveďte kroky k získání hodnot pro **mlEndpoint** a **apiKey**:

1. Klikněte na tlačítko **webových služeb** v nabídce vlevo.
2. Klikněte na tlačítko **školení webová služba** v seznamu webových služeb.
3. Klikněte na tlačítko kopírování vedle **klíč rozhraní API** textového pole. Vložte klíč do schránky do editoru JSON služby Data Factory.
4. V **Azure ML studio**, klikněte na tlačítko **BATCH EXECUTION** odkaz.
5. Kopírovat **Request URI** z **žádosti** části a vložte ho do editoru JSON služby Data Factory.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Propojené služby pro Azure ML aktualizovat bodovací koncový bod:
Následující fragment kódu JSON definuje služby Azure Machine Learning propojený, který odkazuje na jiné než výchozí koncový bod aktualizovat hodnoticí webové služby.  

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
Aktivita prostředku aktualizace Azure ML negeneruje žádný výstup. Azure Data Factory však vyžaduje výstupní datové centrum umožňující prosazovat plán kanálu. Proto používáme datovou sadu dummy/zástupný symbol v tomto příkladu.  

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
Kanál má dvě aktivity: **AzureMLBatchExecution** a **AzureMLUpdateResource**. Aktivita provedení dávky služby Azure ML trvá trénovací data jako vstup a vytvoří soubor iLearner jako výstup. Aktivita vyvolá školení webové služby (výukového experimentu vystavena jako webové služby) se vstupní trénovacích dat a soubor ilearner, který obdrží z webové služby. PlaceholderBlob je jenom fiktivní výstupní datovou sadu, která požaduje služba Azure Data Factory ke spuštění kanálu.

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
